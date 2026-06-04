# Fulmini v2 — Task 6: Canvas — Hero, Charge, Formation

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Implementare le 3 funzioni canvas per S0 (hero-canvas), S1 (charge-canvas) e S2-P1 (formation-canvas), sostituendo i relativi stub nel blocco `<script>`.

**Architecture:** Ogni funzione è autonoma. Usa `canvas.width = canvas.offsetWidth` per dimensionare. Le animazioni girano con `requestAnimationFrame`. Ogni funzione viene chiamata una sola volta da `onSlideEnter` (protetto da `Set triggered`).

**Tech Stack:** Canvas API 2D, vanilla JS

**Prerequisito:** Task 5 completato (stubs `initHeroCanvas`, `initCloudCharge`, `initFormationCanvas` già dichiarati)

**Spec di riferimento:** `docs/superpowers/specs/2026-06-04-fulmini-rebuild-v2-design.md` — sezione 6, canvas table

---

### Task 1: Implementa initHeroCanvas

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Trova `function initHeroCanvas() {}` e sostituisci con:**

```js
function initHeroCanvas() {
  const canvas = document.getElementById('hero-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  let W, H;
  function resize() {
    W = canvas.width = canvas.offsetWidth;
    H = canvas.height = canvas.offsetHeight;
  }
  resize();
  window.addEventListener('resize', resize);

  let frame = 0;
  let nextBolt = 120 + Math.random() * 120;

  function drawBolt(x1, y1, x2, y2, depth) {
    if (depth === 0) return;
    const mx = (x1 + x2) / 2 + (Math.random() - 0.5) * (Math.abs(x2 - x1) + Math.abs(y2 - y1)) * 0.4;
    const my = (y1 + y2) / 2;
    ctx.moveTo(x1, y1);
    ctx.lineTo(mx, my);
    ctx.lineTo(x2, y2);
    if (Math.random() < 0.4 && depth > 1) {
      const bx = mx + (Math.random() - 0.5) * 80;
      const by = my + Math.random() * 80;
      ctx.moveTo(mx, my);
      ctx.lineTo(bx, by);
    }
    drawBolt(x1, y1, mx, my, depth - 1);
    drawBolt(mx, my, x2, y2, depth - 1);
  }

  let bolts = [];

  function spawnBolt() {
    const x = Math.random() * W;
    bolts.push({ x, alpha: 1.0 });
    ctx.save();
    ctx.strokeStyle = `rgba(240,192,64,1)`;
    ctx.lineWidth = 1.5;
    ctx.shadowColor = '#f0c040';
    ctx.shadowBlur = 12;
    ctx.beginPath();
    drawBolt(x, 0, x + (Math.random() - 0.5) * 120, H * 0.7, 5);
    ctx.stroke();
    ctx.restore();
    nextBolt = 120 + Math.random() * 120;
  }

  function loop() {
    ctx.fillStyle = 'rgba(5,7,15,0.18)';
    ctx.fillRect(0, 0, W, H);
    frame++;
    if (frame >= nextBolt) spawnBolt();
    requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 2: Verifica**

Apri `index.html`. S0 hero: i fulmini procedurali dorati devono apparire ogni ~2-4 secondi con effetto fade. Console: nessun errore.

---

### Task 2: Implementa initCloudCharge

- [ ] **Step 1: Trova `function initCloudCharge() {}` e sostituisci con:**

```js
function initCloudCharge() {
  const canvas = document.getElementById('charge-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth || 340;
  canvas.height = canvas.offsetHeight || 300;
  const W = canvas.width, H = canvas.height;

  let particles = [];
  let tension = 0;
  let flashing = false;
  let flashAlpha = 0;

  function reset() {
    particles = [];
    tension = 0;
    flashing = false;
    flashAlpha = 0;
    for (let i = 0; i < 50; i++) {
      const pos = Math.random() < 0.5 ? 'top' : 'bottom';
      particles.push({
        x: 30 + Math.random() * (W - 60),
        y: pos === 'top' ? 20 + Math.random() * (H * 0.35) : H * 0.55 + Math.random() * (H * 0.35),
        vx: (Math.random() - 0.5) * 0.5,
        vy: pos === 'top' ? -0.3 - Math.random() * 0.3 : 0.3 + Math.random() * 0.3,
        type: pos === 'top' ? '+' : '-',
        size: 3 + Math.random() * 2
      });
    }
  }
  reset();
  canvas.addEventListener('click', reset);

  function loop() {
    ctx.clearRect(0, 0, W, H);

    // Ground
    ctx.fillStyle = 'rgba(122,128,153,0.15)';
    ctx.fillRect(0, H - 18, W, 18);
    ctx.fillStyle = 'rgba(240,192,64,0.4)';
    for (let x = 15; x < W; x += 30) {
      ctx.font = '10px sans-serif';
      ctx.fillText('+', x, H - 5);
    }

    // Tension bar
    tension = Math.min(1, tension + 0.003);
    const barH = (H - 36) * tension;
    const barGrad = ctx.createLinearGradient(W - 18, H - 18 - barH, W - 18, H - 18);
    barGrad.addColorStop(0, 'rgba(240,192,64,.9)');
    barGrad.addColorStop(1, 'rgba(240,192,64,.1)');
    ctx.fillStyle = barGrad;
    ctx.fillRect(W - 14, H - 18 - barH, 10, barH);
    ctx.strokeStyle = 'rgba(240,192,64,.3)';
    ctx.strokeRect(W - 14, 8, 10, H - 26);
    ctx.fillStyle = 'rgba(240,192,64,.5)';
    ctx.font = '7px Bebas Neue, sans-serif';
    ctx.fillText('V', W - 12, 6);

    // Flash
    if (tension >= 1 && !flashing) {
      flashing = true; flashAlpha = 1;
      tension = 0;
    }
    if (flashing) {
      ctx.strokeStyle = `rgba(255,255,180,${flashAlpha})`;
      ctx.lineWidth = 2;
      ctx.shadowColor = '#ffffb4';
      ctx.shadowBlur = 20;
      ctx.beginPath();
      ctx.moveTo(W / 2, 0);
      ctx.lineTo(W / 2 + 20, H * 0.4);
      ctx.lineTo(W / 2 - 10, H * 0.4);
      ctx.lineTo(W / 2 + 10, H - 18);
      ctx.stroke();
      ctx.shadowBlur = 0;
      flashAlpha -= 0.06;
      if (flashAlpha <= 0) flashing = false;
    }

    // Particles
    particles.forEach(p => {
      p.x += p.vx; p.y += p.vy;
      if (p.x < 10 || p.x > W - 20) p.vx *= -1;
      if (p.type === '+' && p.y < 5) p.vy *= -1;
      if (p.type === '+' && p.y > H * 0.45) p.vy = -Math.abs(p.vy);
      if (p.type === '-' && p.y > H - 25) p.vy *= -1;
      if (p.type === '-' && p.y < H * 0.5) p.vy = Math.abs(p.vy);

      ctx.beginPath();
      ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
      ctx.fillStyle = p.type === '+' ? 'rgba(240,192,64,.85)' : 'rgba(58,143,255,.85)';
      ctx.fill();
      ctx.fillStyle = p.type === '+' ? '#f0c040' : '#3a8fff';
      ctx.font = `bold ${p.size * 2 + 4}px sans-serif`;
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      ctx.fillText(p.type, p.x, p.y);
    });

    requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 2: Verifica**

Naviga a S1-P1 (scroll snap porta alla slide, poi VC porta al pannello 1). Il canvas charge deve mostrare ioni +/- in movimento con barra tensione che sale e flash periodico. Click = reset.

---

### Task 3: Implementa initFormationCanvas

- [ ] **Step 1: Trova `function initFormationCanvas() {}` e sostituisci con:**

```js
function initFormationCanvas() {
  const canvas = document.getElementById('formation-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth || 320;
  canvas.height = canvas.offsetHeight || 320;
  const W = canvas.width, H = canvas.height;

  const crystals = [];
  const graupel = [];
  for (let i = 0; i < 20; i++) {
    crystals.push({ x: 20 + Math.random() * (W - 40), y: H * 0.4 + Math.random() * H * 0.3, vy: -0.4 - Math.random() * 0.4 });
    graupel.push({ x: 20 + Math.random() * (W - 40), y: H * 0.3 + Math.random() * H * 0.2, vy: 0.5 + Math.random() * 0.5 });
  }

  function loop() {
    ctx.clearRect(0, 0, W, H);

    // Cloud body
    ctx.beginPath();
    ctx.ellipse(W / 2, 55, 80, 38, 0, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(15,21,37,0.9)';
    ctx.fill();
    ctx.strokeStyle = 'rgba(122,128,153,0.3)';
    ctx.lineWidth = 1;
    ctx.stroke();

    // Labels
    ctx.font = '9px Bebas Neue, sans-serif';
    ctx.letterSpacing = '2px';
    ctx.fillStyle = 'rgba(240,192,64,.5)';
    ctx.textAlign = 'center';
    ctx.fillText('ZONA +', W / 2, 30);
    ctx.fillStyle = 'rgba(58,143,255,.5)';
    ctx.fillText('ZONA −', W / 2, H * 0.55);
    ctx.fillStyle = 'rgba(240,192,64,.4)';
    ctx.fillText('SUOLO +', W / 2, H - 6);

    // Ground
    ctx.fillStyle = 'rgba(122,128,153,0.12)';
    ctx.fillRect(0, H - 18, W, 18);

    // Ground + signs
    ctx.fillStyle = 'rgba(240,192,64,0.5)';
    ctx.font = '10px sans-serif';
    for (let x = 15; x < W; x += 28) ctx.fillText('+', x, H - 4);

    // Crystals (+, gold, move up)
    crystals.forEach(c => {
      c.y += c.vy;
      if (c.y < 20) c.y = H * 0.45;
      ctx.beginPath();
      ctx.arc(c.x, c.y, 3, 0, Math.PI * 2);
      ctx.fillStyle = 'rgba(240,192,64,0.8)';
      ctx.fill();
      ctx.fillStyle = '#f0c040';
      ctx.font = 'bold 9px sans-serif';
      ctx.textAlign = 'center';
      ctx.fillText('+', c.x, c.y + 4);
    });

    // Graupel (-, blue, move down)
    graupel.forEach(g => {
      g.y += g.vy;
      if (g.y > H - 20) g.y = H * 0.2;
      ctx.beginPath();
      ctx.arc(g.x, g.y, 5, 0, Math.PI * 2);
      ctx.fillStyle = 'rgba(58,143,255,0.6)';
      ctx.fill();
      ctx.fillStyle = '#3a8fff';
      ctx.font = 'bold 9px sans-serif';
      ctx.textAlign = 'center';
      ctx.fillText('−', g.x, g.y + 4);
    });

    requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 2: Verifica**

Naviga a S2-P1. Il canvas deve mostrare: nuvola grigia in cima, cristalli gold + che salgono, graupel blue - che scendono, segni + a terra. Label ZONA+/ZONA-/SUOLO+.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: implement canvas hero, charge, formation animations"
```
