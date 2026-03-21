---
layout: default
title: Crypto
permalink: /crypto/
---

<div class="page-hero">
  <h1 style="color:var(--accent)">[crypto]</h1>
  <p>RSA attacks, cipher breaks, hash collisions, and cryptographic weaknesses.</p>
</div>

<div class="section-label">crypto writeups</div>

<ul class="post-list">
  {% assign crypto_posts = site.categories.crypto %}
  {% for post in crypto_posts %}
    <li class="post-item" style="--cat-color:var(--accent)">
      <a href="{{ post.url | relative_url }}">
        <div class="post-accent"></div>
        <div class="post-body">
          <div class="post-meta-row">
            <span class="post-date">{{ post.date | date: "%Y-%m-%d" }}</span>
            <span class="post-tag tag-crypto">crypto</span>
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
      // no crypto writeups yet
    </li>
  {% endfor %}
</ul>
