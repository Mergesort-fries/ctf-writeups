---
layout: default
title: Forensics
permalink: /forensics/
---

<div class="page-hero">
  <h1 style="color:var(--green)">[forensics]</h1>
  <p>Disk images, memory dumps, network captures, steganography, and file carving.</p>
</div>

<div class="section-label">forensics writeups</div>

<ul class="post-list">
  {% assign forensics_posts = site.categories.forensics %}
  {% for post in forensics_posts %}
    <li class="post-item" style="--cat-color:var(--green)">
      <a href="{{ post.url | relative_url }}">
        <div class="post-accent"></div>
        <div class="post-body">
          <div class="post-meta-row">
            <span class="post-date">{{ post.date | date: "%Y-%m-%d" }}</span>
            <span class="post-tag tag-rev">forensics</span>
            {% if post.difficulty %}<span class="post-tag" style="background:rgba(255,255,255,0.06);color:var(--muted)">{{ post.difficulty }}</span>{% endif %}
            {% if post.ctf %}<span class="post-tag" style="background:rgba(255,255,255,0.06);color:var(--muted)">{{ post.ctf }}</span>{% endif %}
          </div>
          <div class="post-title">{{ post.title }}</div>
          {% if post.description %}<div class="post-excerpt">{{ post.description | truncate: 100 }}</div>{% elsif post.excerpt %}<div class="post-excerpt">{{ post.excerpt | strip_html | truncate: 100 }}</div>{% endif %}
        </div>
        <div class="post-arrow">→</div>
      </a>
    </li>
  {% else %}
    <li style="padding:2rem;text-align:center;color:var(--muted);font-family:var(--mono);font-size:0.85rem;">
      // no forensics writeups yet
    </li>
  {% endfor %}
</ul>