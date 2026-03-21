---
layout: default
title: Home
---

<div class="hero">
  <div class="hero-prompt">cat /team/readme.md</div>
  <h1 class="hero-title">
    <span class="hi">Mergesort</span><span class="team">Fries</span>
  </h1>
  <p class="hero-subtitle">
    CTF team writeups, exploits, and solutions. We break things, learn stuff, document everything.
  </p>
  <div class="hero-stats">
    <span class="stat-pill"><span class="dot"></span> picoCTF 2026</span>
    <span class="stat-pill"><span class="dot"></span> Active</span>
    <span class="stat-pill"><span class="dot"></span> {{ site.posts | size }} writeup{% if site.posts.size != 1 %}s{% endif %}</span>
  </div>
</div>

<div class="section-label">categories</div>

<div class="category-grid mb2">
  <a href="{{ '/web' | relative_url }}" class="cat-card" style="--cat-color:var(--cyan)">
    <div class="cat-icon" style="color:var(--cyan)">[web]</div>
    <div class="cat-name">Web</div>
    <div class="cat-desc">XSS, SQLi, SSRF, auth bypass, session forgery</div>
  </a>
  <a href="{{ '/crypto' | relative_url }}" class="cat-card" style="--cat-color:var(--accent)">
    <div class="cat-icon" style="color:var(--accent)">[enc]</div>
    <div class="cat-name">Crypto</div>
    <div class="cat-desc">Cipher breaks, RSA attacks, hash collisions</div>
  </a>
  <a href="{{ '/pwn' | relative_url }}" class="cat-card" style="--cat-color:var(--red)">
    <div class="cat-icon" style="color:var(--red)">[pwn]</div>
    <div class="cat-name">Pwn</div>
    <div class="cat-desc">Buffer overflows, ROP chains, heap exploitation</div>
  </a>
  <a href="{{ '/picoctf2026' | relative_url }}" class="cat-card" style="--cat-color:var(--green)">
    <div class="cat-icon" style="color:var(--green)">[ctf]</div>
    <div class="cat-name">picoCTF 2026</div>
    <div class="cat-desc">All writeups from picoCTF 2026</div>
  </a>
</div>

<div class="section-label">latest writeups</div>

<ul class="post-list">
  {% for post in site.posts limit:20 %}
    {% assign cat = post.categories[1] | default: post.categories[0] %}
    <li class="post-item" style="--cat-color:{% if cat == 'web' %}var(--cyan){% elsif cat == 'crypto' %}var(--accent){% elsif cat == 'pwn' %}var(--red){% else %}var(--green){% endif %}">
      <a href="{{ post.url | relative_url }}">
        <div class="post-accent"></div>
        <div class="post-body">
          <div class="post-meta-row">
            <span class="post-date">{{ post.date | date: "%Y-%m-%d" }}</span>
            {% if cat %}<span class="post-tag tag-{{ cat }}">{{ cat }}</span>{% endif %}
            {% if post.difficulty %}<span class="post-tag" style="background:rgba(255,255,255,0.06);color:var(--muted)">{{ post.difficulty }}</span>{% endif %}
          </div>
          <div class="post-title">{{ post.title }}</div>
          {% if post.excerpt %}<div class="post-excerpt">{{ post.excerpt | strip_html | truncate: 100 }}</div>{% endif %}
        </div>
        <div class="post-arrow">→</div>
      </a>
    </li>
  {% else %}
    <li style="padding:2rem;text-align:center;color:var(--muted);font-family:var(--mono);font-size:0.85rem;">
      // no writeups yet — they're coming
    </li>
  {% endfor %}
</ul>
