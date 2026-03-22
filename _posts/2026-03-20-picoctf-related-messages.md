---
layout: post
title: "Related Messages"
date: 2026-03-20
categories: [picoctf2026, crypto]
ctf: picoctf2026
difficulty: Medium
points: 200
author: "matcha / Ding Jie"
description: "Franklin-Reiter related message attack on RSA — exploiting two ciphertexts encrypted under the same key where plaintexts differ by a known constant."
---

## Challenge

**Category:** Crypto · **Points:** 200 · **CTF:** picoCTF 2026

The sender encrypted a message, made a typo, and re-encrypted the corrected version under the same RSA key. We are given both ciphertexts.

---

## Analysis

The typo was `z` (`0x7a`) instead of `}` (`0x7d`), a difference of exactly **3**. This means:

```
m1 = m2 - 3
```

Both plaintexts were encrypted under the same key, leaking a linear relationship — the perfect setup for a Franklin-Reiter attack.

---

## The Attack

Franklin-Reiter's related message attack exploits the fact that if two plaintexts satisfy a known linear relation, both are roots of polynomials that share a common factor:

```
f1(x) = x^e - c1
f2(x) = (x + 3)^e - c2
```

Both share the root `m1`, so:

```
gcd(f1, f2)  over ℤ_N[x]  →  (x - m1)
```

This directly reveals the plaintext — **no factoring of N required**.

```python
from sage.all import *

# Given values
N = # paste N here
e = # paste e here
c1 = # paste c1 here
c2 = # paste c2 here

P.<x> = PolynomialRing(Zmod(N))
f1 = x^e - c1
f2 = (x + 3)^e - c2

# GCD gives (x - m1)
g = gcd(f1, f2)
m1 = -g.monic()[0]
print(long_to_bytes(int(m1)))
```

---

## Key Takeaway

Never reuse an RSA key to encrypt related messages. Even a 3-byte difference is enough to completely break the encryption with Franklin-Reiter. Use hybrid encryption (RSA + AES) or randomised padding (OAEP) instead.

---

## Flag

```
picoCTF{m3ssage_w1th_typ0}
```
