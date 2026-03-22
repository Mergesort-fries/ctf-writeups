---
layout: post
title: "Forensics Git 1"
date: 2026-03-20
categories: [picoctf2026, forensics]
ctf: picoctf2026
difficulty: Medium
points: 300
author: "matcha / Ding Jie"
description: "Recovering deleted data from a Git repository's object store inside a disk image."
---

## Challenge

**Category:** Forensics · **Points:** 300 · **CTF:** picoCTF 2026

The challenge gives a `.gz` file containing a disk image with multiple partitions.

---

## Analysis

Decompress the image first:

```bash
gunzip diskimage.gz
```

Running `fdisk -l diskimage` reveals three partitions — two Linux partitions (`disk.img1` at 300M and `disk.img3` at 467M) and a swap partition. Standard mounting failed because the image contains partitions rather than a raw filesystem, so we used 7-Zip to extract the individual partition files.

---

## Approach

**Step 1 — Mount the first partition**

```bash
mkdir /tmp/disk
sudo mount -o loop 0.img /tmp/disk
```

Search for any `.git` directories:

```bash
sudo find /tmp/disk -name ".git" -type d 2>/dev/null
```

Nothing found, so move to the next partition.

**Step 2 — Mount the larger partition**

```bash
mkdir /tmp/disk
sudo mount -o loop 3.img /tmp/disk
sudo find /tmp/disk -name ".git" -type d 2>/dev/null
```

This revealed a repo at `/tmp/disk/home/ctf-player/Code/secrets.git`.

**Step 3 — Search Git history for the flag**

Git never truly deletes committed data — even after a file is removed, it stays in the object store. We searched all commit history:

```bash
cd /tmp/disk/home/ctf-player/Code/secrets.git
git log --all -p | grep "picoCTF{"
```

The flag was buried in an old commit.

---

## Key Takeaway

Even after `git rm`, data persists in the object store. Always scrub sensitive commits with `git filter-branch` or `git filter-repo` before pushing to a public repo.

---

## Flag

```
picoCTF{g17_r3m3mb3r5_d4ddf904}
```
