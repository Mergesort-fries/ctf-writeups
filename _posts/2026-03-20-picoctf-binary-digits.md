---
layout: post
title: "Binary Digits"
date: 2026-03-20
categories: [picoctf2026, forensics]
ctf: picoctf2026
difficulty: Medium
points: 100
author: "matcha / Ding Jie"
description: "Extracting hidden data encoded in binary from an image or file."
---

## Challenge

**Category:** Forensics · **Points:** 100 · **CTF:** picoCTF 2026

We are given a long string of binary digits and need to extract the flag from it.

---

## Analysis

The file contains a raw binary string — a long sequence of `0`s and `1`s. The key insight is that every 8 bits maps to one byte, meaning the whole string is just binary-encoded file data.

---

## Approach

Convert the binary string to bytes, write it out, and check what kind of file it is:

```python
import sys

input_file = sys.argv[1]
output_file = sys.argv[2] if len(sys.argv) > 2 else "output.bin"

with open(input_file, 'r') as f:
    bits = f.read().strip().replace('\n', '').replace(' ', '')

data = bytearray(int(bits[i:i+8], 2) for i in range(0, len(bits), 8))

with open(output_file, 'wb') as f:
    f.write(data)

print(f"Done: {len(data)} bytes written to {output_file}")
```

Running this produces a `.jpg` image file. Opening it reveals the flag printed in the image.

---

## Key Takeaway

Binary encoding isn't encryption — it's trivial to reverse. Any file can be represented as a binary string and decoded back in one line. Always check file headers (`file output.bin`) to identify the format before assuming it's plain text.

---

## Flag

```
picoCTF{h1dd3n_1n_th3_b1n4ry_2f96e9a1}
```
