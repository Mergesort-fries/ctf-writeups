---
layout: default
title: Pwn
permalink: /pwn/
---

<div class="page-hero">
  <h1 style="color:var(--red)">[pwn]</h1>
  <p>Buffer overflows, ROP chains, format strings, heap exploitation.</p>
</div>

<div class="section-label">pwn writeups</div>

<ul class="post-list">
  {% assign pwn_posts = site.categories.pwn %}
  {% for post in pwn_posts %}
    <li class="post-item" style="--cat-color:var(--red)">
      <a href="{{ post.url | relative_url }}">
        <div class="post-accent"></div>
        <div class="post-body">
          <div class="post-meta-row">
            <span class="post-date">{{ post.date | date: "%Y-%m-%d" }}</span>
            <span class="post-tag tag-pwn">pwn</span>
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
      // no pwn writeups yet
    </li>
  {% endfor %}
</ul>
