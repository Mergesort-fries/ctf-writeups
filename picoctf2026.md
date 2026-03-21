---
layout: default
title: picoCTF 2026
permalink: /picoctf2026/
---

<div class="event-header">
  <div class="event-badge">pico<br>CTF</div>
  <div class="event-info">
    <h1>picoCTF 2026</h1>
    <p>Carnegie Mellon University · March 2026</p>
  </div>
</div>

<div class="section-label">writeups</div>

<ul class="post-list">
  {% assign pico_posts = site.categories.picoctf2026 %}
  {% for post in pico_posts %}
    {% assign cat = post.categories[1] | default: "misc" %}
    <li class="post-item" style="--cat-color:{% if cat == 'web' %}var(--cyan){% elsif cat == 'crypto' %}var(--accent){% elsif cat == 'pwn' %}var(--red){% else %}var(--green){% endif %}">
      <a href="{{ post.url | relative_url }}">
        <div class="post-accent"></div>
        <div class="post-body">
          <div class="post-meta-row">
            <span class="post-date">{{ post.date | date: "%Y-%m-%d" }}</span>
            <span class="post-tag tag-{{ cat }}">{{ cat }}</span>
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
      // writeups coming soon
    </li>
  {% endfor %}
</ul>
