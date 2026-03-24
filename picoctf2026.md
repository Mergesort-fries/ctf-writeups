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

<div class="scoreboard-img">
  <img src="{{ '/assets/images/picoctf2026/picoCTF2026.png' | relative_url }}" alt="picoCTF 2026 scoreboard">
</div>

<div class="score-banner">
  <div class="score-banner-left">
    <div class="score-label">team</div>
    <div class="score-team">Mergesort(fries)</div>
  </div>
  <div class="score-banner-right">
    <div class="score-label">team score</div>
    <div class="score-total">8300</div>
  </div>
  <div class="score-members">
    <div class="score-row">
      <span class="score-handle">jrxx</span>
      <div class="score-bar-wrap">
        <div class="score-bar" style="--pct:57%;--bar-color:#f7a26a"></div>
      </div>
      <span class="score-pts">4700 pts</span>
    </div>
    <div class="score-row">
      <span class="score-handle">matcha24</span>
      <div class="score-bar-wrap">
        <div class="score-bar" style="--pct:30%;--bar-color:#00ff88"></div>
      </div>
      <span class="score-pts">2500 pts</span>
    </div>
    <div class="score-row">
      <span class="score-handle">itsKen</span>
      <div class="score-bar-wrap">
        <div class="score-bar" style="--pct:13%;--bar-color:#7c6af7"></div>
      </div>
      <span class="score-pts">1100 pts</span>
    </div>
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

<style>
.score-banner {
  background: var(--bg2);
  border: 1px solid var(--border);
  border-radius: var(--radius-lg);
  padding: 1.5rem;
  margin-bottom: 2rem;
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: auto auto;
  gap: 1.25rem 1rem;
}
.score-banner-left, .score-banner-right { display: flex; flex-direction: column; gap: 0.25rem; }
.score-banner-right { text-align: right; align-items: flex-end; }
.score-label { font-family: var(--mono); font-size: 0.65rem; font-weight: 700; color: var(--muted); letter-spacing: 0.1em; text-transform: uppercase; }
.score-team { font-family: var(--mono); font-size: 1rem; font-weight: 700; color: #fff; }
.score-total { font-family: var(--mono); font-size: 1.8rem; font-weight: 700; color: var(--green); text-shadow: 0 0 20px rgba(0,255,136,0.25); line-height: 1; }
.score-members { grid-column: 1 / -1; display: flex; flex-direction: column; gap: 0.65rem; border-top: 1px solid var(--border); padding-top: 1.25rem; }
.score-row { display: grid; grid-template-columns: 90px 1fr 70px; align-items: center; gap: 0.75rem; }
.score-handle { font-family: var(--mono); font-size: 0.78rem; color: var(--text-dim); }
.score-handle::before { content: '@'; color: var(--green-dk); }
.score-bar-wrap { background: var(--bg3); border-radius: 999px; height: 6px; overflow: hidden; }
.score-bar { height: 100%; width: var(--pct); background: var(--bar-color); border-radius: 999px; }
.score-pts { font-family: var(--mono); font-size: 0.72rem; color: var(--muted); text-align: right; }
.scoreboard-img { margin-bottom: 1.5rem; }
.scoreboard-img img { width: 100%; border-radius: var(--radius-lg); border: 1px solid var(--border); }
</style>
