---
layout: post
title: "Shift Registers"
date: 2026-03-20
categories: [picoctf2026, crypto]
ctf: picoctf2026
difficulty: Medium
points: 200
author: "matcha / Ding Jie"
description: "Breaking an LFSR-based stream cipher with a 126-byte key that gets immediately masked to 8 bits — leaving only 256 possible states."
---

## Challenge

**Category:** Crypto · **Points:** 200 · **CTF:** picoCTF 2026

---

## Analysis

Reading the source, the key generation is immediately suspicious:

```python
key = os.urandom(126)   # 126 random bytes...
lfsr = key & 0xFF       # ...but masked to just 8 bits
```

Despite `key` being 126 bytes, it is immediately masked to `0xFF` — only the last byte matters. This means **only 256 possible initial LFSR states exist**, regardless of key size.

The LFSR steps with feedback from bits 7, 5, 4, and 3:

```python
feedback = b7 ^ b5 ^ b4 ^ b3
lfsr = (feedback << 7) | (lfsr >> 1)
```

Each plaintext byte is XORed with the current LFSR state to produce ciphertext.

---

## Exploit

Since the keyspace is just 8 bits, brute force all 256 possible initial states:

```python
def decrypt_lfsr(ct, key):
    lfsr = key & 0xFF
    out = []
    for byte in ct:
        out.append(byte ^ lfsr)
        b7 = (lfsr >> 7) & 1
        b5 = (lfsr >> 5) & 1
        b4 = (lfsr >> 4) & 1
        b3 = (lfsr >> 3) & 1
        feedback = b7 ^ b5 ^ b4 ^ b3
        lfsr = ((feedback << 7) | (lfsr >> 1)) & 0xFF
    return bytes(out)

for key in range(256):
    pt = decrypt_lfsr(ct_bytes, key)
    try:
        s = pt.decode('ascii')
        if 'picoCTF' in s or '{' in s:
            print(f"Key={key}: {s}")
    except:
        pass
```

Keys **162 and 163** both produce the correct flag — consecutive keys can yield the same output because the LFSR's first step may produce the same keystream byte from adjacent initial states.

---

## Key Takeaway

The 126-byte key is a complete red herring. Truncating with `& 0xFF` throws away all but 8 bits of entropy. Secure LFSR-based stream ciphers need large state (hundreds of bits) and nonlinear combination to resist brute force and algebraic attacks.

---

## Flag

```
picoCTF{l1n3ar_f33dback_sh1ft_r3g}
```
