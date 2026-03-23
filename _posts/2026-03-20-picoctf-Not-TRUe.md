---
layout: post
title: "Not TRUe"
date: 2026-03-20
categories: [picoctf2026, crypto]
ctf: picoctf2026
difficulty: Medium
points: 200
author: "matcha / Ding Jie"
description: "Lattice attack on NTRU — recovering the private key (f, g) from the public key h using LLL reduction on the NTRU lattice, then decrypting 6 ciphertexts to recover a binary-encoded flag."
---

## Challenge

**Category:** Crypto · **Points:** 200 · **CTF:** picoCTF 2026

The challenge title is a pun: **Not TRUe** = **NTRU**, the lattice-based public-key cryptosystem. We are given the NTRU public key `h`, six ciphertexts `ct`, and the system parameters `N=48`, `p=3`, `q=509`.

---

## Analysis

NTRU key generation works as follows:

1. Pick two small ternary polynomials `f`, `g` with coefficients in `{-1, 0, 1}` in the ring `R = Z[x]/(x^N - 1)`.
2. Compute the public key `h = p · g · f⁻¹ (mod q)`.

Encryption of a binary message polynomial `m` with a random ternary blinding polynomial `r`:

```
c = r * h + m  (mod q)
```

Decryption recovers `m` via:

```
a = f * c  (mod q)   [center-lifted to (-q/2, q/2)]
m = f_p⁻¹ * a  (mod p)
```

The security of NTRU rests on the hardness of finding the short vector `(f, g)` from `h`. With `N = 48`, this instance is **very small** — making it directly vulnerable to the classic NTRU lattice attack via LLL reduction.

The ratio `δ = N / log₂(q) ≈ 48 / 9 ≈ 5.3` is far below the threshold at which LLL starts to struggle, so the attack is trivial.

---

## The Attack

### Step 1 — Build the NTRU Lattice

Construct the 2N × 2N matrix:

```
M = [ I   H ]
    [ 0  qI ]
```

where `H` is the **circulant matrix** of `h` (each row is a cyclic shift of the previous), and `I` is the N×N identity matrix.

Any vector of the form `(f, g)` satisfies `f·H ≡ g (mod q)`, so `(f, g)` lies in the row space of `M`. Because `f` and `g` have small ternary coefficients, `(f, g)` is an unusually **short** lattice vector — exactly the kind LLL is designed to find.

```python
def circulant(h):
    return [h[-i:] + h[:-i] for i in range(N)]

H = circulant(h_list)

M = [[0]*(2*N) for _ in range(2*N)]
for i in range(N):   M[i][i]     = 1        # top-left: I
for i in range(N):
    for j in range(N): M[i][j+N] = H[i][j]  # top-right: H
for i in range(N):   M[i+N][i+N] = q        # bottom-right: qI
```

### Step 2 — LLL Reduction

Run LLL on `M` using `fpylll`:

```python
from fpylll import IntegerMatrix, LLL

A = IntegerMatrix(2*N, 2*N)
# ... populate A from M ...
LLL.reduction(A)
```

After reduction, the short rows of the basis are candidates for `(f, g)`.

### Step 3 — Extracting (f, g)

A subtle but important observation: the recovered vector has `g`-coefficients that are **multiples of 3** (i.e. `p`). This happens because LLL found `(f, p·g)` rather than `(f, g)` directly — a known artifact when the lattice has multiple equally short vectors. Dividing through by `p` gives the true `g`:

```python
for row in reduced_basis:
    f_cand = row[:N]
    g_cand = row[N:]
    if all(x % 3 == 0 for x in g_cand) and not all(x == 0 for x in g_cand):
        g_real = [x // 3 for x in g_cand]
        # Verify: f * h ≡ p*g (mod q)
        if poly_mul_mod(f_cand, h_list, q, N) == [p*x % q for x in g_real]:
            f, g = f_cand, g_real
            break
```

### Step 4 — Decrypt

With `f` recovered, decrypt each ciphertext:

```python
def decrypt(c, f, f_p_inv):
    a = poly_mul_mod(f, c, q, N)
    a = center_lift(a, q)           # map to (-q/2, q/2)
    a_modp = [x % p for x in a]
    return poly_mul_mod(f_p_inv, a_modp, p, N)
```

Each of the 6 ciphertexts decrypts to a 48-element binary polynomial (coefficients in `{0, 1}`). Concatenating all 6 × 48 = 288 bits and decoding as 8-bit ASCII gives the flag.

---

## Full Solve Script

```python
from fpylll import IntegerMatrix, LLL

N = 48
p = 3
q = 509

h_list = [311, 273, 153, 392, 105, 426, 45, 159, 421, 30, 404, 38, 313, 480, 37, 195,
          82, 382, 236, 230, 340, 66, 199, 121, 279, 273, 271, 22, 270, 227, 26, 253,
          213, 408, 323, 263, 283, 168, 88, 164, 383, 247, 5, 313, 150, 170, 89, 235]

ct = [
    [231, 6, 348, 205, 488, 156, 57, 466, 295, 240, 87, 285, 165, 208, 343, 426,
     410, 36, 190, 110, 187, 28, 237, 262, 508, 111, 451, 311, 128, 449, 476, 434,
     159, 98, 488, 399, 314, 499, 427, 325, 299, 250, 457, 8, 64, 423, 210, 271],
    [177, 217, 101, 452, 354, 388, 158, 437, 435, 484, 452, 0, 142, 47, 66, 229,
     131, 325, 423, 394, 337, 356, 8, 56, 197, 12, 456, 186, 430, 286, 68, 106,
     271, 257, 351, 76, 508, 383, 360, 318, 426, 113, 84, 451, 436, 206, 63, 151],
    [212, 400, 162, 356, 300, 494, 60, 333, 230, 477, 484, 174, 26, 65, 175, 107,
     158, 496, 168, 318, 350, 497, 261, 281, 388, 193, 16, 294, 180, 390, 116, 252,
     169, 411, 69, 257, 496, 302, 86, 320, 405, 436, 156, 462, 219, 486, 349, 494],
    [275, 256, 300, 153, 467, 412, 380, 353, 435, 232, 450, 490, 136, 136, 86, 326,
     93, 19, 208, 89, 474, 163, 70, 30, 56, 261, 145, 499, 49, 403, 331, 315, 49,
     472, 66, 156, 26, 481, 412, 258, 503, 44, 275, 222, 164, 356, 212, 57],
    [354, 310, 412, 400, 67, 433, 363, 209, 80, 244, 473, 239, 409, 446, 356, 193,
     191, 15, 443, 79, 371, 63, 444, 285, 316, 488, 176, 44, 393, 401, 504, 106,
     111, 5, 491, 208, 279, 403, 83, 226, 271, 244, 358, 473, 436, 208, 457, 81],
    [246, 291, 361, 460, 247, 229, 400, 3, 38, 189, 460, 347, 384, 327, 246, 33,
     7, 141, 135, 182, 496, 160, 259, 424, 496, 137, 28, 6, 169, 97, 251, 269,
     316, 68, 360, 426, 22, 150, 498, 398, 270, 130, 447, 36, 500, 32, 48, 114],
]

# --- Polynomial arithmetic mod (x^N - 1) ---

def poly_mul_mod(a, b, mod, N):
    result = [0] * N
    for i in range(N):
        if a[i] == 0: continue
        for j in range(N):
            result[(i+j) % N] = (result[(i+j) % N] + a[i]*b[j]) % mod
    return result

def poly_inv_mod(f, mod, N):
    """Invert f in Z_mod[x]/(x^N - 1) via extended Euclidean algorithm."""
    def deg(p):
        d = len(p) - 1
        while d > 0 and p[d] == 0: d -= 1
        return d
    def divmod_poly(a, b, m):
        a, b = list(a), list(b)
        da, db = deg(a), deg(b)
        if da < db: return [0], a
        q = [0] * (da - db + 1)
        bi = pow(int(b[db]), -1, m)
        for i in range(da - db, -1, -1):
            if a[i+db] == 0: continue
            c = a[i+db] * bi % m
            q[i] = c
            for j in range(db+1): a[i+j] = (a[i+j] - c*b[j]) % m
        return q, a
    def mul_poly(a, b, m):
        r = [0] * (deg(a) + deg(b) + 1)
        for i in range(deg(a)+1):
            for j in range(deg(b)+1): r[i+j] = (r[i+j] + a[i]*b[j]) % m
        return r
    def sub_poly(a, b, m, L):
        r = [0]*L
        for i in range(min(len(a),L)): r[i] = a[i] % m
        for i in range(min(len(b),L)): r[i] = (r[i] - b[i]) % m
        return r

    mpoly = [(-1) % mod] + [0]*(N-1) + [1]
    fp = [int(c) % mod for c in f] + [0]
    old_r, r = fp[:], mpoly[:]
    old_s, s = [1]+[0]*(N+1), [0]*(N+2)
    for _ in range(500):
        if all(c == 0 for c in r): break
        if deg(old_r) == 0: break
        qp, nr = divmod_poly(old_r, r, mod)
        ns = sub_poly(old_s, mul_poly(qp, s, mod), mod, N+2)
        old_r, r = r, nr
        old_s, s = s, ns
    if deg(old_r) != 0 or old_r[0] == 0: return None
    gi = pow(int(old_r[0]), -1, mod)
    inv = [(c*gi) % mod for c in old_s[:N]]
    # verify
    if poly_mul_mod(f[:N], inv, mod, N)[0] != 1: return None
    return inv

def center_lift(coeffs, q):
    return [c if c <= q//2 else c - q for c in [x % q for x in coeffs]]

def decrypt(c, f, fp_inv):
    a = center_lift(poly_mul_mod(f, c, q, N), q)
    return poly_mul_mod(fp_inv, [x % p for x in a], p, N)

# --- Build and reduce the NTRU lattice ---

def circulant(h):
    return [h[-i:] + h[:-i] for i in range(N)]

H = circulant(h_list)
A = IntegerMatrix(2*N, 2*N)
for i in range(N): A[i, i] = 1
for i in range(N):
    for j in range(N): A[i, j+N] = H[i][j]
for i in range(N): A[i+N, i+N] = q

LLL.reduction(A)

# --- Extract (f, g): look for rows where g is divisible by p ---

f_key = None
for row_idx in range(2*N):
    row = [A[row_idx, j] for j in range(2*N)]
    f_cand, g_cand = row[:N], row[N:]
    if all(x == 0 for x in f_cand): continue
    if not all(abs(x) <= 3 for x in f_cand): continue
    if not all(x % 3 == 0 for x in g_cand): continue
    g_real = [x // 3 for x in g_cand]
    if all(x == 0 for x in g_real): continue
    if poly_mul_mod(f_cand, h_list, q, N) == [p*x % q for x in g_real]:
        f_key = f_cand
        break

# Compute f inverse mod p
f_modp = [x % p for x in f_key]
fp_inv = poly_inv_mod(f_modp, p, N)

# --- Decrypt and decode ---

bits = []
for c in ct:
    bits.extend(decrypt(c, f_key, fp_inv))

flag = ''.join(
    chr(int(''.join(str(b) for b in bits[i:i+8]), 2))
    for i in range(0, len(bits), 8)
    if len(bits[i:i+8]) == 8
)
print("[+] FLAG:", flag)
```

---

## Output

```
[+] FLAG: picoCTF{th4ts_s0_N0t_TRU3_38a83032}
```

---

## Key Takeaways

**Why the attack works at N=48:** The Gaussian heuristic predicts the shortest vector in this lattice has norm ≈ `√(2N) · q^(1/2)` ≈ `√96 · 22.6 ≈ 222`. The real `(f, g)` has norm `√(‖f‖² + ‖g‖²)` ≈ `√(2 · N · 0.5)` ≈ `√48 ≈ 7` (for ternary polynomials of weight ~N/3). This is dramatically shorter than the Gaussian heuristic — LLL finds it immediately.

**The p·g artifact:** LLL returned `g`-coefficients that were multiples of 3. This is because the circulant structure of the lattice introduces short linear combinations of `(f, g)` and `p`-multiples thereof. Always verify the candidate using `f·h ≡ p·g (mod q)` rather than just checking coefficient magnitudes.

**Secure NTRU parameters** require N in the range of 509–1277, not 48. At N=48, this is a toy instance.

---

## Flag

```
picoCTF{th4ts_s0_N0t_TRU3_38a83032}
```