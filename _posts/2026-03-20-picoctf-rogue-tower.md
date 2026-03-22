---
layout: post
title: "Rogue Tower"
date: 2026-03-20
categories: [picoctf2026, forensics]
ctf: picoctf2026
difficulty: Medium
points: 300
author: "matcha / Ding Jie"
description: "Identifying a rogue cell tower from captured network traffic and decrypting its exfiltrated data."
---

## Challenge

**Category:** Forensics · **Points:** 300 · **CTF:** picoCTF 2026

---

## Analysis

Filtering for HTTP traffic in the capture, there are GET requests from several IP addresses. Inspecting each one reveals which belongs to the rogue tower:

- `10.100.156.251` → IMSI: `310410316992912`, CELL: `15606` — **legitimate**
- `10.100.50.122` → IMSI: `310410308555787`, CELL: `92058` — **unauthorised tower**

---

## Approach

**Step 1 — Isolate rogue tower traffic**

Filter to just the rogue tower's POST requests and compile the data it sent. This gives us the following Base64-looking string:

```
QFFWWnZjfkxCCFJABmhbBFxUakEFQAtFb1xXVgEHAAQBRQ==
```

**Step 2 — Identify the encryption**

The string is clearly encoded. Hint 3 states: *"The encryption key is derived from the victim device's IMSI."*

This narrows the brute force significantly — we try XOR with different substrings of the IMSI `310410308555787` against the ciphertext.

**Step 3 — Brute force the XOR key**

```python
import base64
import itertools

ciphertext = base64.b64decode("QFFWWnZjfkxCCFJABmhbBFxUakEFQAtFb1xXVgEHAAQBRQ==")
imsi = "310410308555787"

for length in range(1, len(imsi) + 1):
    for start in range(len(imsi) - length + 1):
        key = imsi[start:start + length].encode()
        pt = bytes(c ^ key[i % len(key)] for i, c in enumerate(ciphertext))
        if b'picoCTF' in pt:
            print(f"Key: {key} → {pt}")
```

The correct substring of the IMSI XOR-decrypts the ciphertext to reveal the flag.

---

## Key Takeaway

Rogue cell towers (IMSI catchers / Stingrays) capture device identifiers and can intercept traffic. XOR encryption with a predictable key (like a device's own IMSI) provides essentially no security.

---

## Flag

```
picoCTF{placeholder_rogue_tower}
```
