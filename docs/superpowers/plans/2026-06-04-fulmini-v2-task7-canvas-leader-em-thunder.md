# Fulmini v2 — Task 7: Canvas — Leader, EM Pulse, Thunder

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Implementare `initSteppedLeader` (S2-P2, sincronizzato con HC 4 fasi), `initEMPulse` (S3-P2), `initThunderCalc` (slider, già gestito da JS core) e `initThunderCanvas` (S4-P1).

**Architecture:** `initSteppedLeader` sincronizza la fase del canvas con l'HC `#leader-hc` attivo. Il bottone `#leader-play-btn` controlla play/pause. `initEMPulse` reagisce al click di `#em-play-btn`. `initThunderCanvas` loopa automaticamente.

**Tech Stack:** Canvas API 2D, vanilla JS

**Prerequisito:** Task 5-6 completati

**Spec di riferimento:** `docs/superpowers/specs/2026-06-04-fulmini-rebuild-v2-design.md` — sezione 6 canvas table

---

### Task 1: Implementa initSteppedLeader

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Trova `function initSteppedLeader() {}` e sostituisci con:**

```js
function initSteppedLeader() {
  const canvas = document.getElementById('leader-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth || 300;
  canvas.height = canvas.offsetHeight || 400;
  const W = canvas.width, H = canvas.height;

  const hcWrap = document.getElementById('leader-hc');
  const hcObj = initHorizontalCarousel(hcWrap);
  const playBtn = document.getElementById('leader-play-btn');
  let playing = false;
  let phase = 0;
  let frameCount = 0;
  let autoTimer = null;

  if (playBtn) {
    playBtn.addEventListener('click', () => {
      playing = !playing;
      playBtn.textContent = playing ? '⏸ PAUSA' : '▶ PLAY';
    });
  }

  // Leader path: random stepped line top→bottom
  function steppedPath(x0, y0, x1, y1, steps) {
    const pts = [{x: x0, y: y0}];
    for (let i = 1; i < steps; i++) {
      const t = i / steps;
      pts.push({
        x: x0 + (x1 - x0) * t + (Math.random() - 0.5) * 30,
        y: y0 + (y1 - y0) * t
      });
    }
    pts.push({x: x1, y: y1});
    return pts;
  }

  const leaderPts = steppedPath(W / 2, 0, W / 2 + 10, H * 0.65, 18);
  const streamerPts = steppedPath(W / 2 + 10, H, W / 2 + 10, H * 0.7, 8);

  function drawPhase0() {
    // Stepped leader: dashed gold, partial
    const progress = Math.min(1, (frameCount % 120) / 80);
    const end = Math.floor(progress * (leaderPts.length - 1));
    ctx.setLineDash([4, 4]);
    ctx.strokeStyle = 'rgba(240,192,64,0.7)';
    ctx.lineWidth = 1.5;
    ctx.beginPath();
    for (let i = 0; i <= end; i++) {
      i === 0 ? ctx.moveTo(leaderPts[i].x, leaderPts[i].y) : ctx.lineTo(leaderPts[i].x, leaderPts[i].y);
    }
    ctx.stroke();
    ctx.setLineDash([]);
    // Cloud
    ctx.beginPath();
    ctx.ellipse(W / 2, 25, 60, 20, 0, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(15,21,37,0.9)';
    ctx.fill();
    ctx.strokeStyle = 'rgba(122,128,153,0.3)';
    ctx.stroke();
  }

  function drawPhase1() {
    // Stepped leader (full) + streamers (blue, rising)
    drawPhase0Full();
    const progress = Math.min(1, (frameCount % 80) / 50);
    const end = Math.floor(progress * (streamerPts.length - 1));
    ctx.strokeStyle = 'rgba(58,143,255,0.8)';
    ctx.lineWidth = 1.5;
    ctx.shadowColor = '#3a8fff';
    ctx.shadowBlur = 8;
    ctx.beginPath();
    for (let i = 0; i <= end; i++) {
      i === 0 ? ctx.moveTo(streamerPts[i].x, streamerPts[i].y) : ctx.lineTo(streamerPts[i].x, streamerPts[i].y);
    }
    ctx.stroke();
    ctx.shadowBlur = 0;
  }

  function drawPhase0Full() {
    ctx.setLineDash([4, 4]);
    ctx.strokeStyle = 'rgba(240,192,64,0.6)';
    ctx.lineWidth = 1.5;
    ctx.beginPath();
    leaderPts.forEach((p, i) => i === 0 ? ctx.moveTo(p.x, p.y) : ctx.lineTo(p.x, p.y));
    ctx.stroke();
    ctx.setLineDash([]);
    ctx.beginPath();
    ctx.ellipse(W / 2, 25, 60, 20, 0, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(15,21,37,0.9)';
    ctx.fill();
    ctx.strokeStyle = 'rgba(122,128,153,0.3)'; ctx.stroke();
  }

  function drawPhase2() {
    // Flash pulsante: canale chiuso
    const alpha = 0.5 + 0.5 * Math.sin(frameCount * 0.15);
    ctx.strokeStyle = `rgba(255,255,180,${alpha})`;
    ctx.lineWidth = 3;
    ctx.shadowColor = '#ffffb4';
    ctx.shadowBlur = 20;
    ctx.beginPath();
    leaderPts.forEach((p, i) => i === 0 ? ctx.moveTo(p.x, p.y) : ctx.lineTo(p.x, p.y));
    ctx.stroke();
    ctx.shadowBlur = 0;
    ctx.beginPath();
    ctx.ellipse(W / 2, 25, 60, 20, 0, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(15,21,37,0.9)'; ctx.fill();
  }

  function drawPhase3() {
    // Return stroke: bright, risale dal basso
    const progress = Math.min(1, (frameCount % 60) / 35);
    const end = Math.floor((1 - progress) * (leaderPts.length - 1));
    ctx.strokeStyle = 'rgba(255,255,180,0.95)';
    ctx.lineWidth = 4;
    ctx.shadowColor = '#ffffff';
    ctx.shadowBlur = 30;
    ctx.beginPath();
    for (let i = leaderPts.length - 1; i >= end; i--) {
      i === leaderPts.length - 1 ? ctx.moveTo(leaderPts[i].x, leaderPts[i].y) : ctx.lineTo(leaderPts[i].x, leaderPts[i].y);
    }
    ctx.stroke();
    ctx.shadowBlur = 0;
    ctx.beginPath();
    ctx.ellipse(W / 2, 25, 60, 20, 0, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(15,21,37,0.9)'; ctx.fill();
  }

  const drawFns = [drawPhase0, drawPhase1, drawPhase2, drawPhase3];

  function loop() {
    ctx.fillStyle = 'rgba(5,7,15,0.5)';
    ctx.fillRect(0, 0, W, H);
    // Ground
    ctx.fillStyle = 'rgba(122,128,153,0.15)';
    ctx.fillRect(0, H - 14, W, 14);
    frameCount++;
    if (playing && frameCount % 120 === 0) {
      phase = (phase + 1) % 4;
      hcObj?.go(phase);
    }
    // Sync with HC click
    if (hcWrap) {
      const tabs = [...hcWrap.querySelectorAll('.hc-tab')];
      const active = tabs.findIndex(t => t.classList.contains('active'));
      if (active >= 0 && active !== phase) { phase = active; frameCount = 0; }
    }
    drawFns[phase]?.();
    requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 2: Verifica**

Vai a S2-P2 (VC secondo pannello di s-formazione). Clicca [B. STREAMER]: il canvas deve cambiare per mostrare il filamento blu. Clicca PLAY: deve avanzare le fasi automaticamente ogni ~2s.

---

### Task 2: Implementa initEMPulse

- [ ] **Step 1: Trova `function initEMPulse() {}` e sostituisci con:**

```js
function initEMPulse() {
  const canvas = document.getElementById('em-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth || 500;
  canvas.height = canvas.offsetHeight || 180;
  const W = canvas.width, H = canvas.height;
  const btn = document.getElementById('em-play-btn');

  let circles = [];
  let running = false;

  function fire() {
    running = true;
    circles = [];
    if (btn) btn.textContent = 'AVVIATO…';
  }

  if (btn) btn.addEventListener('click', fire);

  function loop() {
    ctx.fillStyle = 'rgba(5,7,15,0.25)';
    ctx.fillRect(0, 0, W, H);

    if (running) {
      if (Math.random() < 0.3) {
        circles.push({ r: 2, alpha: 0.9, hue: Math.random() * 60 + 180 });
      }
    }

    // Bolt (center)
    if (running && circles.length > 0 && circles.length < 8) {
      ctx.strokeStyle = 'rgba(255,255,180,0.9)';
      ctx.lineWidth = 2;
      ctx.shadowColor = '#ffffb4';
      ctx.shadowBlur = 15;
      ctx.beginPath();
      ctx.moveTo(W / 2, 5);
      ctx.lineTo(W / 2 + 8, H * 0.45);
      ctx.lineTo(W / 2 - 5, H * 0.45);
      ctx.lineTo(W / 2 + 3, H - 5);
      ctx.stroke();
      ctx.shadowBlur = 0;
    }

    // Expanding EM rings (iterate in reverse to allow safe splice)
    for (let i = circles.length - 1; i >= 0; i--) {
      const c = circles[i];
      c.r += 3.5;
      c.alpha -= 0.012;
      if (c.alpha <= 0) { circles.splice(i, 1); continue; }
      ctx.beginPath();
      ctx.arc(W / 2, H / 2, c.r, 0, Math.PI * 2);
      ctx.strokeStyle = `hsla(${c.hue},80%,65%,${c.alpha})`;
      ctx.lineWidth = 1.5;
      ctx.stroke();
    }

    if (running && circles.length === 0 && btn) {
      running = false;
      btn.textContent = 'AVVIA IMPULSO';
    }

    requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 2: Verifica**

Vai a S3-P2. Clicca "AVVIA IMPULSO": deve apparire un fulmine al centro con cerchi concentrici colorati che si espandono.

---

### Task 3: Implementa initThunderCanvas

- [ ] **Step 1: Trova `function initThunderCanvas() {}` e sostituisci con:**

```js
function initThunderCanvas() {
  const canvas = document.getElementById('thunder-canvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth || 340;
  canvas.height = canvas.offsetHeight || 260;
  const W = canvas.width, H = canvas.height;

  let waves = [];
  let waveTimer = 0;

  function spawnWave() {
    waves.push({ r: 5, alpha: 0.8, speed: 2 + Math.random() });
  }

  function loop() {
    ctx.fillStyle = 'rgba(5,7,15,0.3)';
    ctx.fillRect(0, 0, W, H);
    waveTimer++;
    if (waveTimer % 90 === 0) {
      // Spawn bolt + waves
      waves = [];
      for (let i = 0; i < 5; i++) setTimeout(spawnWave, i * 80);
    }

    // Bolt (left side)
    if (waveTimer % 90 < 15) {
      const alpha = 1 - (waveTimer % 90) / 15;
      ctx.strokeStyle = `rgba(255,255,180,${alpha})`;
      ctx.lineWidth = 2;
      ctx.shadowColor = '#ffffb4';
      ctx.shadowBlur = 12;
      ctx.beginPath();
      ctx.moveTo(40, 10); ctx.lineTo(55, 80); ctx.lineTo(35, 80);
      ctx.lineTo(52, H * 0.75); ctx.stroke();
      ctx.shadowBlur = 0;
    }

    // Waves (arcs expanding right)
    for (let i = waves.length - 1; i >= 0; i--) {
      const w = waves[i];
      w.r += w.speed;
      w.alpha -= 0.008;
      if (w.alpha <= 0 || w.r > W) { waves.splice(i, 1); continue; }
      ctx.beginPath();
      ctx.arc(45, H / 2, w.r, -Math.PI * 0.6, Math.PI * 0.6);
      ctx.strokeStyle = `rgba(232,234,240,${w.alpha})`;
      ctx.lineWidth = 1.5;
      ctx.stroke();
    }

    // Labels
    ctx.font = '8px Bebas Neue, sans-serif';
    ctx.fillStyle = 'rgba(240,192,64,.4)';
    ctx.fillText('ONDA D\'URTO', 90, 30);
    ctx.fillStyle = 'rgba(122,128,153,.4)';
    ctx.fillText('ONDA ACUSTICA', 140, 80);

    requestAnimationFrame(loop);
  }
  loop();
}
```

- [ ] **Step 2: Verifica**

Vai a S4-P1. Il canvas tuono deve mostrare un fulmine a sinistra e archi-onda che si espandono verso destra in loop ogni ~3 secondi.

- [ ] **Step 3: Implementa initThunderCalc (già funzionante da Task 5, solo aggiungi stub vuoto)**

Il `thunder-slider` è già gestito nel JS core (Task 5). La funzione stub `initThunderCalc(){}` rimane vuota — nessuna modifica necessaria.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: implement canvas leader, EM pulse, thunder animations"
```
