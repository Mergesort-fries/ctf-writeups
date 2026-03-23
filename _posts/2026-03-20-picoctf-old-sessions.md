---
layout: post
title: "Old Sessions"
date: 2026-03-20
categories: [picoctf2026, web]
ctf: picoctf2026
difficulty: Medium
points: 100
author: "matcha / Ding Jie"
description: "Forging a Flask client-side session token to escalate privileges to admin."
---

## Challenge

**Category:** Web · **Points:** 100 · **CTF:** picoCTF 2026

URL: `http://dolphin-cove.picoctf.net:52077/sessions`

We are given a sessions endpoint and an example session:

```
session:VccD5CnHjooYuqCNfiWBvI-kybC0pays_8QFBWE3wjs
{'_permanent': True, 'key': 'admin'}
```

---

## Analysis

The session data suggests:

- The application uses **client-side session storage**
- The session contains `_permanent: True` and `key: admin`
- This is a strong indicator the session is encoded (likely Base64) and **not securely signed**

If the server trusts this session value directly, we can **forge our own session**.

![Session cookie in browser devtools]({{ '/assets/images/old-sessions-cookie.png' | relative_url }})

---

## Approach

**Goal:** Modify the session so that `key = admin`

1. Inspect how the session is stored (cookie or URL)
2. Decode the session value
3. Modify the JSON data
4. Re-encode it
5. Send it back to the server

---

## Exploit

If the session is Base64 encoded:

```python
import base64

data = b"{'_permanent': True, 'key': 'admin'}"
encoded = base64.b64encode(data)
print(encoded)
```

Swap the session cookie with the forged value and reload the page.

---

## Flag

```
picoCTF{s3t_s3ss10n_3xp1rat10n5_53a328ed}
```
