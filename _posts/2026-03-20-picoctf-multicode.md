---
layout: post
title: "MultiCode"
date: 2026-03-20
categories: [picoctf2026, misc]
ctf: picoctf2026
difficulty: Medium
points: 200
author: "matcha / Ding Jie"
description: "A string encoded through four nested layers — Base64, Hex, URL encoding, and ROT13. CyberChef Magic sniffs out the sequence."
---

## Challenge

**Category:** General Skills · **Points:** 200 · **CTF:** picoCTF 2026

We're given a single encoded string and told it has gone through multiple layers of encoding. No hints on which ones or in what order.

---

## Analysis

The string looks like Base64 at first glance — but decoding it once doesn't give plaintext. It's clearly nested. Rather than guessing the order manually, throwing it into **CyberChef's Magic operation** lets it auto-detect the encoding stack.

Magic identified the sequence as:

1. **From Base64**
2. **From Hex**
3. **URL Decode**
4. **ROT13**

---

## Approach

Replicate the chain in CyberChef with these operations in order:

| Step | Operation | Settings |
|------|-----------|----------|
| 1 | From Base64 | Alphabet: `A-Za-z0-9+/=`, Remove non-alphabet chars: ✓ |
| 2 | From Hex | Delimiter: Auto |
| 3 | URL Decode | Treat `+` as space: ✓ |
| 4 | ROT13 | Rotate upper + lower case, Amount: 13 |

Pasting the input string and running the chain outputs the flag directly.

---

## Key Takeaway

When a challenge says "multiple encodings" with no further hints, CyberChef's Magic operation is the fastest way to fingerprint the stack. Each encoding layer on its own is trivially reversible — the only challenge is identifying the correct order.

---

## Flag

```
picoCTF{nested_enc0ding_66b54257}
```