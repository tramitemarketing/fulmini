# Fulmini v2 — Task 9: Verifica Finale e Push GitHub

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Verificare che tutti i 10 slide funzionino correttamente, correggere eventuali bug, fare il commit finale e push su GitHub.

**Architecture:** Test manuale di ogni slide + verifica console JS. Poi `git push origin main`.

**Tech Stack:** Browser DevTools, git

**Prerequisito:** Task 1-8 completati

---

### Task 1: Verifica ogni slide

**Files:**
- Modify: `index.html` (solo fix bug se trovati)

- [ ] **Step 1: Apri index.html nel browser e verifica la checklist**

Apri `index.html` direttamente (file://) o con un server locale. Verifica ogni punto:

**UI Globale:**
- [ ] Cursore dorato personalizzato visibile
- [ ] Scroll snap: ogni scroll porta alla slide successiva
- [ ] Nav dots a destra si aggiornano
- [ ] Counter "01 / 10" aggiornato ad ogni slide
- [ ] Progress bar sinistra si riempie
- [ ] Scroll hint scompare al primo scroll
- [ ] Bottone fullscreen funzionante (tasto F o click)
- [ ] Nessun errore rosso in console (F12 → Console)

**S0 Hero:**
- [ ] Fulmini procedurali dorati appaiono ogni 2-4 secondi
- [ ] Titolo FULMINI con MI in gold visibile

**S1 Cos'è un Fulmine (VC 3 pannelli):**
- [ ] Scroll su S1: VC porta a P2 (Plasma) e P3 (Numeri)
- [ ] Dots VC a destra aggiornati
- [ ] Canvas charge-canvas: ioni +/- in movimento, barra tensione, flash
- [ ] Click sul canvas charge → reset animazione

**S2 Come si Forma (VC 2 pannelli):**
- [ ] P1: canvas formation-canvas animato (cristalli e graupel)
- [ ] P2: HC 4 fasi (A/B/C/D) funzionante
- [ ] Click tab HC → canvas leader-canvas cambia
- [ ] Bottone PLAY avanza le fasi automaticamente

**S3 Fisica Estrema (VC 3 pannelli):**
- [ ] P2: bottone "AVVIA IMPULSO" → em-canvas con cerchi concentrici
- [ ] P3: due colonne chimica/nucleare visibili

**S4 Tuono (VC 3 pannelli):**
- [ ] P1: thunder-canvas con archi-onda che si espandono
- [ ] P2: slider funzionante (trascina → km si aggiorna)
- [ ] P3: tabella dati fisici completa

**S5 Tipi di Fulmini (VC 3 pannelli):**
- [ ] P1: HC CG⁻/CG⁺/IC/CC → type-canvas cambia
- [ ] P2: HC BALL/SPRITE/ELVES/BLUE JET → tle-canvas cambia
- [ ] P3: diagramma quote atmosferiche visibile

**S6 Numeri & Record (VC 3 pannelli):**
- [ ] P1: grid 6 record-cell visibili
- [ ] P2: 3 curiosità rare
- [ ] P3: debunking con stat box Empire State

**S7 Franklin & Storia (VC 3 pannelli):**
- [ ] P1: timeline 6 eventi
- [ ] P2: SVG aquilone Franklin visibile con label
- [ ] P3: warn-pill Richmann visibile

**S8 Mitologia & Scienza (VC 3 pannelli):**
- [ ] P1: 4 deity-card (Zeus, Thor, Giove, Indra)
- [ ] P2: timeline scienza moderna + detect-list
- [ ] P3: 3 myth-item curiosità

**S9 Nel Mondo & Protezione (VC 3 pannelli):**
- [ ] P1: mappa Leaflet dark caricata con 4 marker dorati
- [ ] P1: click marker → popup con dati
- [ ] P1: contatore fulmini in aggiornamento
- [ ] P2: tabella Italia + mini mappa SVG zone
- [ ] P3: safety grid 6 scenari
- [ ] P3: click scenario → testo tip si aggiorna

---

### Task 2: Fix bug trovati

- [ ] **Se trovi errori in console:**

Leggi il messaggio di errore. I problemi più comuni:
- `canvas.offsetWidth` restituisce 0 → il canvas non è ancora nel DOM. Fix: avvolgi in `requestAnimationFrame(() => { canvas.width = canvas.offsetWidth || 340; ... })`.
- `Cannot read property of null` su un `querySelector` → verifica che l'ID esista nell'HTML dei task precedenti.
- Leaflet `_leaflet_id` già definito → il guard `if (mapDiv._leaflet_id) return` deve essere presente.

- [ ] **Se lo scroll snap non funziona:**

Verifica che in `<style>` sia presente:
```css
html { scroll-snap-type: y mandatory; }
body { overflow: visible; }
.slide { scroll-snap-align: start; scroll-snap-stop: always; }
```
NON deve esserci `overflow: hidden` su body o html.

- [ ] **Se i canvas HC non si sincronizzano:**

Verifica che `initHorizontalCarousel` sia chiamata PRIMA di `initTypeVisualizer`/`initTLEVisualizer`/`initSteppedLeader` con il wrapper corretto. Controlla che gli ID `cg-hc`, `tle-hc`, `leader-hc` esistano nell'HTML.

- [ ] **Commit fix (solo se hai fatto modifiche):**

```bash
git add index.html
git commit -m "fix: resolve canvas and scroll issues in final verification"
```

---

### Task 3: Push su GitHub

- [ ] **Step 1: Verifica stato git**

```bash
git status
git log --oneline -10
```

Expected: tutto committed, nessun file modificato non committato.

- [ ] **Step 2: Push**

```bash
git push origin main
```

Expected output:
```
Enumerating objects: ...
To https://github.com/tramitemarketing/fulmini.git
   xxxxxxx..yyyyyyy  main -> main
```

- [ ] **Step 3: Conferma deployment**

Il sito Cloudflare Pages si aggiorna automaticamente ad ogni push su `main`. Attendi 1-2 minuti, poi verifica su `terremoti.pages.dev` (o l'URL configurato in Cloudflare Pages per il repo fulmini).

- [ ] **Step 4: Commit finale (solo se necessario)**

Se durante la verifica del sito live trovi problemi, fixali e commit:

```bash
git add index.html
git commit -m "fix: post-deploy corrections"
git push origin main
```

---

### Checklist finale

Prima di dichiarare il task completato, conferma:
- [ ] 10 slide visibili e navigabili via scroll snap
- [ ] Tutti i canvas animati (non stubs vuoti)
- [ ] Tutti gli HC funzionanti (S2, S5×2)
- [ ] Mappa Leaflet caricata
- [ ] Safety simulator funzionante
- [ ] Push su GitHub completato
- [ ] Nessun errore in console
