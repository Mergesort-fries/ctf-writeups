---
layout: post
title: "Old Sessions"
date: 2026-03-20
categories: [picoctf2026, web]
ctf: picoctf2026
difficulty: Medium
author: yourname
description: "Forging a Flask session token to escalate privileges to admin."
---

## 🧩 Challenge
URL: http://dolphin-cove.picoctf.net:52077/sessions

We are given a sessions endpoint and an example session:
session:VccD5CnHjooYuqCNfiWBvI-kybC0pays_8QFBWE3wjs
{'_permanent': True, 'key': 'admin'}

---

## Analysis

The session data suggests that:
- The application is using **client-side session storage**
- The session contains:
  - `_permanent: True`
  - `key: admin`

This is a strong indicator that the session might be:
- encoded (likely Base64 or similar)
- possibly **not securely signed**

If the server trusts this session value directly, we may be able to **forge our own session**.

---

## Approach

Goal:
👉 Modify the session so that `key = admin`

Steps:
1. Inspect how the session is stored (cookie or URL)
2. Decode the session value
3. Modify the JSON data
4. Re-encode it
5. Send it back to the server

---
<img src="/ctf-writeups/assets/images/old-sessions-cookie.png" width="600">

## ⚙️ Exploit

If the session is Base64 encoded:

```python
import base64

data = b"{'_permanent': True, 'key': 'admin'}"
encoded = base64.b64encode(data)
print(encoded)