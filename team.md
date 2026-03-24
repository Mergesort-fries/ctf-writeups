---
layout: default
title: Team
permalink: /team/
---

<div class="page-hero">
  <div class="hero-prompt">cat /team/members.json</div>
  <h1 style="margin-bottom:0.5rem">Mergesort(fries)</h1>
  <p style="color:var(--text-dim);font-size:0.9rem">CTF team · Singapore</p>
</div>

<div class="section-label">members</div>

<div class="team-grid">

  <div class="member-card">
    <div class="member-avatar" style="--av-color:#00ff88">DJ</div>
    <div class="member-info">
      <div class="member-handle">matcha</div>
      <div class="member-name">Ding Jie</div>
      <div class="member-tags">
        <span class="mtag tag-web">web</span>
        <span class="mtag tag-crypto">crypto</span>
      </div>
      <a href="https://github.com/Notmatcha" class="member-github" target="_blank" rel="noopener">
        <span class="gh-icon">⌥</span> Notmatcha
      </a>
      <a href="https://www.https://www.linkedin.com/in/ding-jie-ng/" class="member-linkedin" target="_blank" rel="noopener">
        <span class="li-icon">in</span> Ng Ding Jie
      </a>
    </div>
  </div>

  <div class="member-card">
    <div class="member-avatar" style="--av-color:#7c6af7">KE</div>
    <div class="member-info">
      <div class="member-handle">itsken</div>
      <div class="member-name">Kendrix</div>
      <div class="member-tags">
        <span class="mtag tag-pwn">pwn</span>
        <span class="mtag tag-misc">misc</span>
      </div>
      <a href="https://github.com/KenLZY" class="member-github" target="_blank" rel="noopener">
        <span class="gh-icon">⌥</span> KenLZY
      </a>
      <a href="https://www.https://www.linkedin.com/in/kendrix-lim/" class="member-linkedin" target="_blank" rel="noopener">
        <span class="li-icon">in</span> kendrix-lim
      </a>
    </div>
  </div>

  <div class="member-card">
    <div class="member-avatar" style="--av-color:#f7a26a">JR</div>
    <div class="member-info">
      <div class="member-handle">jrxx</div>
      <div class="member-name">Michael Jr Rasco</div>
      <div class="member-tags">
        <span class="mtag tag-pwn">pwn</span>
        <span class="mtag tag-web">web</span>
      </div>
      <a href="https://github.com/michaeljrr" class="member-github" target="_blank" rel="noopener">
        <span class="gh-icon">⌥</span> michaeljrr
      </a>
      <a href="https://www.https://www.linkedin.com/in/michaeljrr/" class="member-linkedin" target="_blank" rel="noopener">
        <span class="li-icon">in</span> Michael Jr Rasco Emata
      </a>
    </div>
  </div>

</div>

<style>
.team-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
  gap: 1rem;
  margin-bottom: 3rem;
}
.member-card {
  background: var(--bg2);
  border: 1px solid var(--border);
  border-radius: var(--radius-lg);
  padding: 1.5rem;
  display: flex;
  gap: 1.25rem;
  align-items: flex-start;
  transition: border-color .2s, background .2s;
}
.member-card:hover {
  border-color: var(--border2);
  background: var(--bg3);
}
.member-avatar {
  width: 52px;
  height: 52px;
  border-radius: 50%;
  background: rgba(0,0,0,0.3);
  border: 2px solid var(--av-color);
  color: var(--av-color);
  font-family: var(--mono);
  font-weight: 700;
  font-size: 1rem;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  box-shadow: 0 0 16px rgba(0,0,0,0.3);
}
.member-info { flex: 1; min-width: 0; }
.member-handle {
  font-family: var(--mono);
  font-size: 1rem;
  font-weight: 700;
  color: #fff;
  margin-bottom: 0.15rem;
}
.member-handle::before { content: '@'; color: var(--green); }
.member-name {
  font-size: 0.78rem;
  color: var(--muted);
  margin-bottom: 0.6rem;
}
.member-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.4rem;
  margin-bottom: 0.75rem;
}
.mtag {
  font-family: var(--mono);
  font-size: 0.62rem;
  font-weight: 700;
  padding: 0.1rem 0.5rem;
  border-radius: 999px;
  letter-spacing: 0.05em;
  text-transform: uppercase;
}
.member-github {
  font-family: var(--mono);
  font-size: 0.72rem;
  color: var(--muted);
  text-decoration: none;
  display: flex;
  align-items: center;
  gap: 0.4rem;
  transition: color .15s;
}
.member-github:hover { color: var(--green); text-decoration: none; }
.gh-icon { font-size: 0.85rem; }
.member-linkedin {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  text-decoration: none;
  color: #0A66C2;
  font-weight: 500;
}

.li-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 16px;
  height: 16px;
  font-size: 12px;
  font-weight: bold;
  background: #0A66C2;
  color: white;
  border-radius: 3px;
}
</style>
