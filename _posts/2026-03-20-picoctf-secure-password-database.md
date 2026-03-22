---
layout: post
title: "Secure Password Database"
date: 2026-03-20
categories: [picoctf2026, rev]
ctf: picoctf2026
difficulty: Medium
points: 200
author: "matcha / Ding Jie"
description: "Heartbleed-style memory leak in a binary — leaking a secret from adjacent heap memory, then recovering the hash to authenticate."
---

## Challenge

**Category:** Reverse Engineering · **Points:** 200 · **CTF:** picoCTF 2026

Connect to: `nc candy-mountain.picoctf.net 52501`

---

## Analysis

Running `strings system.out` immediately gave away key clues:

```
Please set a password for your account:
How many bytes in length is your password?
Enter your hash to access your account!
heartbleed.c
flag.txt
```

The source filename `heartbleed.c` signals the vulnerability type. Disassembling with `objdump -d system.out` identified three key functions:

- `main` — handles I/O and comparison logic
- `make_secret` — decodes the secret by XORing `obf_bytes` (in `.rodata`) with `0xAA`
- `hash` — djb2-style: seed `0x1505`, then `h = h * 33 + char` for each byte

---

## Exploit

**Step 1 — Trigger the memory leak**

The server asks for a password, then asks how many bytes long it is. The bug: it uses the user-supplied length to read memory with **no validation**.

Entering `abc` as the password but claiming it was `500` bytes long leaked far beyond the buffer:

```
97 98 99 10 0 0 0 0 0 0 0 0 ... 105 85 98 104 56 49 33 106 42 104 110 33 -86 0 0 0 ...
```

The first bytes (`97 98 99`) are just `a b c`. But buried further in, a cluster of non-zero values appeared — the secret leaked from adjacent heap memory.

**Step 2 — Decode the secret**

From the `make_secret` disassembly, `obf_bytes` in `.rodata` are XORed with `0xAA`. The `-86` (which is `0xAA` as a signed byte) at the end of the leaked cluster is the XOR key leaking out — a giveaway.

```python
leaked = [105, 85, 98, 104, 56, 49, 33, 106, 42, 104, 110, 33]
secret = bytes(b ^ 0xaa for b in leaked)
# b'iUbh81!j*hn!'
```

**Step 3 — Hash the secret**

The program doesn't compare your input directly to the secret — it hashes the secret with `hash()`, then calls `strtoul()` on your input and compares as integers.

```python
def hash_djb2(s):
    h = 0x1505
    for c in s:
        h = (h * 33 + c) & 0xFFFFFFFFFFFFFFFF
    return h

print(hash_djb2(b'iUbh81!j*hn!'))
# 15237662580160011234
```

**Step 4 — Submit the hash**

Enter `abc` as the password, claim `500` bytes, then when prompted for the hash enter `15237662580160011234`. The server authenticates and prints the flag.

---

## Key Takeaway

Always validate user-supplied lengths before using them to read memory. This is the exact same class of bug as the real Heartbleed (CVE-2014-0160) — a missing bounds check leaking sensitive heap data.

---

## Flag

```
picoCTF{d0nt_trust_us3rs}
```
