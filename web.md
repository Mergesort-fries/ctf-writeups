---
layout: default
title: Web
permalink: /web/
---

<div class="page-hero">
  <h1 style="color:var(--cyan)">[web]</h1>
  <p>XSS, SQLi, SSRF, session attacks, auth bypass, IDOR, and more.</p>
</div>

<div class="section-label">web writeups</div>

<ul class="post-list">
  {% assign web_posts = site.categories.web %}
  {% for post in web_posts %}
    <li class="post-item" style="--cat-color:var(--cyan)">
      <a href="{{ post.url | relative_url }}">
        <div class="post-accent"></div>
        <div class="post-body">
          <div class="post-meta-row">
            <span class="post-date">{{ post.date | date: "%Y-%m-%d" }}</span>
            <span class="post-tag tag-web">web</span>
            {% if post.difficulty %}<span class="post-tag" style="background:rgba(255,255,255,0.06);color:var(--muted)">{{ post.difficulty }}</span>{% endif %}
            {% if post.ctf %}<span class="post-tag" style="background:rgba(255,255,255,0.06);color:var(--muted)">{{ post.ctf }}</span>{% endif %}
          </div>
          <div class="post-title">{{ post.title }}</div>
          {% if post.excerpt %}<div class="post-excerpt">{{ post.excerpt | strip_html | truncate: 100 }}</div>{% endif %}
        </div>
        <div class="post-arrow">→</div>
      </a>
    </li>
  {% else %}
    <li style="padding:2rem;text-align:center;color:var(--muted);font-family:var(--mono);font-size:0.85rem;">
      // no web writeups yet
    </li>
  {% endfor %}
</ul>
