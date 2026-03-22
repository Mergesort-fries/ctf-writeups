---
layout: post
title: "Small Trouble"
date: 2026-03-20
categories: [picoctf2026, crypto]
ctf: picoctf2026
difficulty: Medium
points: 200
author: "matcha / Ding Jie"
description: "Wiener's attack on RSA — recovering a 256-bit private exponent from a 2095-bit modulus using continued fractions."
---

## Challenge

**Category:** Crypto · **Points:** 200 · **CTF:** picoCTF 2026

---

## Analysis

The source reveals the key generation flaw immediately:

```python
d = getPrime(256)       # d is only 256 bits
e = inverse(d, phi)     # e is derived from d, not the other way around
```

Standard RSA picks a small `e` (like 65537) and computes `d`. Here it's **inverted** — `d` is chosen small and `e` is derived. This gives:

```
δ = log(d) / log(n) ≈ 256 / 2095 ≈ 0.122
```

Wiener's theorem states that if `d < n^0.25`, the private key can be recovered in polynomial time. Since **0.122 < 0.25**, Wiener fires easily.

---

## The Attack

Since `ed ≡ 1 (mod φ(n))`, we have `e/n ≈ k/d` for some small integer `k`. The convergents of the continued fraction expansion of `e/n` enumerate rational approximations — one will equal `k/d`.

For each convergent `k/d_cand`, verify by checking whether `(ed - 1)/k` yields a valid `φ(n)` — i.e., whether `x² - (n - φ + 1)x + n = 0` has integer roots.

```python
from Crypto.Util.number import long_to_bytes
from fractions import Fraction

def continued_fraction(n, d):
    while d:
        yield n // d
        n, d = d, n % d

def convergents(cf):
    n0, n1 = 1, 0
    d0, d1 = 0, 1
    for q in cf:
        n0, n1 = q * n0 + n1, n0
        d0, d1 = q * d0 + d1, d0
        yield n0, d0

def wiener(e, n):
    for k, d in convergents(continued_fraction(e, n)):
        if k == 0:
            continue
        if (e * d - 1) % k != 0:
            continue
        phi = (e * d - 1) // k
        # check if phi gives integer roots for (p-1)(q-1)
        b = n - phi + 1
        disc = b * b - 4 * n
        if disc >= 0:
            sq = int(disc ** 0.5)
            if sq * sq == disc:
                return d
    return None

# Given values
n = # paste n here
e = # paste e here
c = # paste c here

d = wiener(e, n)
print(long_to_bytes(pow(c, d, n)))
```

At `δ ≈ 0.122`, Wiener recovers `d` after scanning just a few hundred convergents.

---

## Key Takeaway

Never choose `d` directly. Always pick `e` first (e.g. 65537), then compute `d = e⁻¹ mod φ(n)`. A small `d` sacrifices the entire security of RSA regardless of how large `n` is.

---

## Flag

```
picoCTF{placeholder_small_trouble}
```
