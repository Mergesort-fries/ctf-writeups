---
layout: post
title: "ClusterRSA"
date: 2026-03-20
categories: [picoctf2026, crypto]
ctf: picoctf2026
difficulty: Medium
points: 400
author: "matcha / Ding Jie"
description: "Multi-prime RSA with four factors — factoring n via factordb then computing phi from all four primes."
---

## Challenge

**Category:** Crypto · **Points:** 400 · **CTF:** picoCTF 2026

---

## Analysis

We're given a large `n` and ciphertext `c`. The key observation is that `n` is a product of **four** primes rather than the usual two — making it weaker since each prime is smaller and factorisable.

---

## Approach

**Step 1 — Factorise n**

Since `n` is very large but composed of four smaller primes, paste it into [factordb.com](https://factordb.com). It returns:

```
n = p1 · p2 · p3 · p4

p1 = 9671406556917033397931773
p2 = 9671406556917033398314601
p3 = 9671406556917033398439721
p4 = 9671406556917033398454847
```

**Step 2 — Compute φ(n)**

For multi-prime RSA with factors p1, p2, p3, p4:

```
φ(n) = (p1 - 1)(p2 - 1)(p3 - 1)(p4 - 1)
```

**Step 3 — Recover d and decrypt**

```python
from Crypto.Util.number import long_to_bytes

p1 = 9671406556917033397931773
p2 = 9671406556917033398314601
p3 = 9671406556917033398439721
p4 = 9671406556917033398454847

n = p1 * p2 * p3 * p4
e = 65537  # standard public exponent
c = # paste c here

phi = (p1-1) * (p2-1) * (p3-1) * (p4-1)
d = pow(e, -1, phi)
m = pow(c, d, n)
print(long_to_bytes(m))
```

---

## Key Takeaway

Multi-prime RSA (more than 2 factors) makes individual primes smaller and easier to find in factorisation databases. Standard RSA should use exactly two large, independently generated primes.

---

## Flag

```
picoCTF{mul71_rsa_c5d0a11c}
```
