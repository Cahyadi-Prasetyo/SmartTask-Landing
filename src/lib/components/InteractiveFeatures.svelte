<script>
  import { onMount } from 'svelte';

  /* ── 01. Priority Engine ── */
  let urgency = 8, duration = 5, diff = 6;
  $: score = +((0.5 * urgency) + (0.2 * duration) + (0.3 * diff)).toFixed(2);
  $: level = score >= 8 ? { name:'Urgent', cls:'urgent', bg:'rgba(255,97,97,0.06)', br:'rgba(255,97,97,0.2)' }
           : score >= 6 ? { name:'High',   cls:'high',   bg:'rgba(251,146,60,0.06)', br:'rgba(251,146,60,0.2)' }
           : score >= 4 ? { name:'Medium', cls:'medium', bg:'rgba(255,197,51,0.06)', br:'rgba(255,197,51,0.2)' }
           :               { name:'Low',    cls:'low',    bg:'rgba(99,102,241,0.06)', br:'rgba(99,102,241,0.2)' };

  /* ── 02. Activity Feed / WebSocket simulation ── */
  const BASE_FEED = [
    { id:1, who:'Cahyadi',    action: 'memperbarui', task: 'Laporan Jarkom Bab IV',       pct: 88, kind: 'update' },
    { id:2, who:'Oktaviandra', action: 'mulai',      task: 'Priority Engine — Go service', pct: 15, kind: 'join'   },
    { id:3, who:'Thesar',     action: 'selesai',     task: 'ERD Diagram Database',         pct:100, kind: 'done'   },
    { id:4, who:'Syawal',     action: 'bergabung ke room', task: 'smartask-ta-2026',       pct: null, kind:'join'  },
  ];

  let feed = [];
  let syncing = false;
  let syncDone = false;
  let logItems = [];

  function addLog(msg, kind = 'info') {
    const t = new Date().toLocaleTimeString('id-ID',{hour:'2-digit',minute:'2-digit',second:'2-digit'});
    logItems = [{ t, msg, kind }, ...logItems.slice(0, 6)];
  }

  /* ── Scroll reveal ── */
  onMount(() => {
    feed = BASE_FEED;
    addLog('WebSocket server aktif di :8080', 'system');
    addLog('Room smartask-ta-2026 dibuka', 'system');

    const io = new IntersectionObserver((entries) => {
      entries.forEach(e => {
        if (e.isIntersecting) { e.target.classList.add('in'); io.unobserve(e.target); }
      });
    }, { threshold: 0.1 });
    document.querySelectorAll('.reveal').forEach(el => io.observe(el));
    return () => io.disconnect();
  });

  function runSync() {
    if (syncing) return;
    syncing = true; syncDone = false;
    addLog('Oktaviandra: koneksi baru → channel "update"', 'join');
    setTimeout(() => {
      feed[1] = { ...feed[1], pct: 62 };
      feed = [...feed];
      addLog('server → broadcast: task#2 pct=62%', 'update');
    }, 900);
    setTimeout(() => {
      feed[1] = { ...feed[1], pct: 100, kind: 'done' };
      feed = [...feed];
      addLog('server → broadcast: task#2 DONE ✓', 'done');
      syncing = false; syncDone = true;
    }, 2200);
  }
</script>

<!-- ══════════════════════════════════════════
  01 — HOW IT WORKS  (How priority is calculated)
═══════════════════════════════════════════ -->
<section id="how" class="section-rule">
  <div class="container">

    <!-- Section number + label -->
    <div class="sec-header reveal">
      <span class="mono" style="font-size:11px;color:var(--ash);letter-spacing:0.5px">01 — ALGORITMA PRIORITAS</span>
      <div class="sec-rule"></div>
    </div>

    <div class="how-layout">

      <!-- Left: editorial explanation -->
      <div class="how-left">
        <h2 class="sec-headline reveal">
          Bukan manual.<br>
          <span style="color:var(--mute);font-weight:300">Dihitung dari formula.</span>
        </h2>
        <p class="sec-body reveal d1">
          Peladen Go menghitung skor 0–10 untuk setiap tugas dari tiga faktor. Hasilnya dikirim ke semua perangkat anggota tim dalam milidetik — bukan dari browser, dari backend.
        </p>

        <!-- Formula block — not a card, just raw text -->
        <div class="formula reveal d2">
          <span class="mono formula-label">formula</span>
          <code class="formula-code mono">
            score = (0.5 × urgency)<br>
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;+ (0.2 × est_time)<br>
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;+ (0.3 × difficulty)
          </code>
          <div class="formula-legend">
            {#each [['urgency','Kedekatan deadline, 1–10'],['est_time','Estimasi waktu kerja, jam'],['difficulty','Kompleksitas tugas, 1–10']] as [k,v]}
              <div class="legend-row">
                <span class="mono legend-key">{k}</span>
                <span class="legend-val">{v}</span>
              </div>
            {/each}
          </div>
        </div>
      </div>

      <!-- Right: live calculator — NOT a slider card, but inline controls -->
      <div class="how-right reveal d1">

        <div class="calc-box">
          <div class="calc-head">
            <span class="mono" style="font-size:11px;color:var(--mute)">— Kalkulator Langsung</span>
            <span class="keycap">Live</span>
          </div>

          <!-- Sliders inline, minimal -->
          {#each [
            ['urgency',   urgency,   v => urgency = v,   'Urgency'],
            ['est_time',  duration,  v => duration = v,  'Est. Time'],
            ['difficulty',diff,      v => diff = v,      'Difficulty'],
          ] as [name, val, setter, lbl]}
            <div class="param-row">
              <div class="param-meta">
                <span class="mono param-key">{name}</span>
                <span class="param-num mono">{val}</span>
              </div>
              <input
                type="range" min="1" max="10" value={val}
                on:input={e => setter(+e.target.value)}
                class="range-input"
              />
            </div>
          {/each}

          <!-- Result -->
          <div class="calc-result" style="background:{level.bg};border-color:{level.br}">
            <div class="result-left">
              <span class="mono result-score" class:c-urgent={level.cls==='urgent'} class:c-high={level.cls==='high'} class:c-medium={level.cls==='medium'} class:c-low={level.cls==='low'}>{score}</span>
              <span class="result-label mono">/ 10</span>
            </div>
            <span class="result-badge mono" class:c-urgent={level.cls==='urgent'} class:c-high={level.cls==='high'} class:c-medium={level.cls==='medium'} class:c-low={level.cls==='low'}>
              {level.name}
            </span>
          </div>

          <!-- Equation echo -->
          <div class="eq-echo mono">
            ({0.5} × {urgency}) + ({0.2} × {duration}) + ({0.3} × {diff}) = <b>{score}</b>
          </div>
        </div>
      </div>

    </div>
  </div>
</section>

<!-- ══════════════════════════════════════════
  02 — REAL-TIME SYNC (WebSocket board)
═══════════════════════════════════════════ -->
<section id="demo" class="section-rule">
  <div class="container">

    <div class="sec-header reveal">
      <span class="mono" style="font-size:11px;color:var(--ash);letter-spacing:0.5px">02 — SINKRONISASI TIM</span>
      <div class="sec-rule"></div>
    </div>

    <div class="sync-layout">

      <!-- Left: Text -->
      <div class="sync-left">
        <h2 class="sec-headline reveal">
          Semua orang<br>
          <span style="color:var(--mute);font-weight:300">melihat hal yang sama,</span><br>
          saat itu juga.
        </h2>
        <p class="sec-body reveal d1">
          Golang WebSocket mempertahankan kanal dua-arah persisten ke setiap anggota. Perubahan dari satu perangkat langsung di-broadcast — tanpa refresh, tanpa polling tiap 5 detik.
        </p>

        <div class="stat-row reveal d2">
          {#each [['< 20ms','Latency rata-rata'],['WebSocket','Protokol komunikasi'],['∞','Koneksi bersamaan']] as [v,l]}
            <div class="stat-item">
              <span class="stat-val mono">{v}</span>
              <span class="stat-lbl">{l}</span>
            </div>
          {/each}
        </div>

        <button class="btn btn-secondary sync-btn reveal d2" on:click={runSync} disabled={syncing}>
          {syncing ? '⟳ Simulasi berjalan...' : syncDone ? '✓ Ulangi simulasi' : '▶ Jalankan simulasi WebSocket'}
        </button>
      </div>

      <!-- Right: activity log + board -->
      <div class="sync-right reveal d1">

        <!-- Board header -->
        <div class="board-top">
          <div class="room-label mono">
            <span class="live-dot-green"></span>
            Room: <b>smartask-ta-2026</b>
          </div>
          <span class="member-count mono">{4} online</span>
        </div>

        <!-- Task rows -->
        <div class="board-rows">
          {#each feed as row}
            <div class="board-row" class:board-done={row.kind === 'done'}>
              <div class="board-row-left">
                <span class="board-name">{row.task}</span>
                <span class="board-who mono">@{row.who}</span>
              </div>
              {#if row.pct !== null}
                <div class="board-pct-wrap">
                  <div class="board-bar">
                    <div class="board-fill" style="width:{row.pct}%;background:{row.kind==='done'?'var(--success)':'var(--brand)'}"></div>
                  </div>
                  <span class="mono" style="font-size:11px;color:var(--mute);min-width:28px;text-align:right">{row.pct}%</span>
                </div>
              {:else}
                <span class="board-action mono">{row.action}</span>
              {/if}
            </div>
          {/each}
        </div>

        <!-- Log terminal -->
        <div class="log-terminal">
          <div class="log-head mono">
            <span class="live-dot-green"></span>
            WebSocket Log
          </div>
          <div class="log-body">
            {#each logItems as item}
              <div class="log-line mono {item.kind}">
                <span class="log-t">[{item.t}]</span>
                <span class="log-msg">{item.msg}</span>
              </div>
            {/each}
          </div>
        </div>

      </div>
    </div>
  </div>
</section>

<style>
  /* ── Shared section chrome ── */
  .container { position: relative; }

  .sec-header {
    display: flex;
    align-items: center;
    gap: var(--s4);
    padding: var(--s10) 0 var(--s8);
  }
  .sec-rule {
    flex: 1;
    height: 1px;
    background: var(--hair);
  }

  .sec-headline {
    font-size: clamp(1.8rem, 3vw + 0.4rem, 2.75rem);
    font-weight: 600;
    line-height: 1.1;
    letter-spacing: -0.025em;
    color: var(--ink);
    margin-bottom: var(--s5);
  }
  .sec-body {
    font-size: 15px;
    color: var(--body-c);
    line-height: 1.65;
    max-width: 420px;
  }

  /* ── 01 How ── */
  .how-layout {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 60px;
    align-items: start;
    padding-bottom: var(--s10);
  }
  .how-left { display: flex; flex-direction: column; gap: var(--s6); }
  .how-right {}

  /* Formula block */
  .formula {
    display: flex;
    flex-direction: column;
    gap: var(--s3);
  }
  .formula-label {
    font-size: 10px;
    color: var(--ash);
    letter-spacing: 0.6px;
    text-transform: uppercase;
  }
  .formula-code {
    font-size: 13px;
    color: var(--body-c);
    line-height: 1.8;
    background: var(--surface);
    border: 1px solid var(--hair);
    border-radius: var(--r-md);
    padding: var(--s4) var(--s5);
    display: block;
    white-space: pre;
  }
  .formula-legend {
    display: flex;
    flex-direction: column;
    gap: 6px;
  }
  .legend-row {
    display: flex;
    align-items: center;
    gap: var(--s4);
    font-size: 13px;
  }
  .legend-key {
    color: var(--brand);
    font-size: 12px;
    min-width: 90px;
  }
  .legend-val { color: var(--mute); }

  /* Calculator */
  .calc-box {
    background: var(--surface);
    border: 1px solid var(--hair);
    border-radius: var(--r-xl);
    padding: var(--s5) var(--s5);
    display: flex;
    flex-direction: column;
    gap: var(--s4);
  }
  .calc-head {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding-bottom: var(--s3);
    border-bottom: 1px solid var(--hair);
  }

  .param-row {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }
  .param-meta {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .param-key {
    font-size: 12px;
    color: var(--mute);
  }
  .param-num {
    font-size: 13px;
    color: var(--ink);
    font-weight: 500;
  }

  .range-input {
    -webkit-appearance: none;
    width: 100%;
    height: 2px;
    background: var(--surface-cd);
    border-radius: var(--r-pill);
    outline: none;
    cursor: pointer;
  }
  .range-input::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 13px; height: 13px;
    background: var(--ink);
    border-radius: 50%;
    cursor: pointer;
    transition: transform 0.1s;
  }
  .range-input::-webkit-slider-thumb:hover { transform: scale(1.25); }

  .calc-result {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border: 1px solid;
    border-radius: var(--r-md);
    padding: var(--s3) var(--s4);
    transition: background 0.25s, border-color 0.25s;
  }
  .result-left { display: flex; align-items: baseline; gap: 4px; }
  .result-score {
    font-size: 2rem;
    font-weight: 700;
    font-family: 'JetBrains Mono', monospace;
    transition: color 0.25s;
  }
  .result-label {
    font-size: 13px;
    color: var(--ash);
  }
  .result-badge {
    font-size: 12px;
    font-weight: 600;
    letter-spacing: 0.4px;
  }

  .eq-echo {
    font-size: 11px;
    color: var(--ash);
    padding: var(--s2) 0;
    border-top: 1px solid var(--hair);
    line-height: 1.4;
  }
  .eq-echo b { color: var(--body-c); }

  /* ── 02 Sync ── */
  .sync-layout {
    display: grid;
    grid-template-columns: 0.9fr 1.1fr;
    gap: 60px;
    align-items: start;
    padding-bottom: var(--s10);
  }
  .sync-left {
    display: flex;
    flex-direction: column;
    gap: var(--s5);
  }

  .stat-row {
    display: flex;
    gap: var(--s6);
    padding: var(--s4) 0;
    border-top: 1px solid var(--hair);
    border-bottom: 1px solid var(--hair);
  }
  .stat-item {
    display: flex;
    flex-direction: column;
    gap: 3px;
  }
  .stat-val {
    font-size: 1.25rem;
    font-weight: 600;
    color: var(--ink);
  }
  .stat-lbl {
    font-size: 12px;
    color: var(--mute);
  }

  .sync-btn { width: 100%; justify-content: center; }

  /* Board */
  .sync-right {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  .board-top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: var(--surface-el);
    border: 1px solid var(--hair);
    border-radius: var(--r-md) var(--r-md) 0 0;
    padding: 8px 14px;
  }
  .room-label {
    display: flex;
    align-items: center;
    gap: 7px;
    font-size: 12px;
    color: var(--mute);
  }
  .room-label b { color: var(--ink); }
  .member-count {
    font-size: 11px;
    color: var(--success);
    background: rgba(89,212,153,0.08);
    border: 1px solid rgba(89,212,153,0.2);
    padding: 1px 7px;
    border-radius: var(--r-pill);
  }

  .live-dot-green {
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--success);
    box-shadow: 0 0 0 3px rgba(89,212,153,0.15);
    display: inline-block;
    animation: pulseGreen 2s infinite;
  }

  .board-rows {
    display: flex;
    flex-direction: column;
    background: var(--surface);
    border: 1px solid var(--hair);
    border-top: none;
    margin-top: 0;
  }
  .board-row {
    display: grid;
    grid-template-columns: 1fr auto;
    gap: var(--s4);
    align-items: center;
    padding: 9px 14px;
    border-bottom: 1px solid var(--hair);
    transition: background 0.2s;
  }
  .board-row:last-child { border-bottom: none; }
  .board-row.board-done { background: rgba(89,212,153,0.04); }
  .board-row-left { display: flex; flex-direction: column; gap: 2px; }
  .board-name {
    font-size: 13px;
    font-weight: 500;
    color: var(--ink);
  }
  .board-who {
    font-size: 11px;
    color: var(--ash);
  }
  .board-pct-wrap {
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .board-bar {
    width: 80px; height: 2px;
    background: var(--hair);
    border-radius: var(--r-pill);
    overflow: hidden;
  }
  .board-fill {
    height: 100%;
    border-radius: var(--r-pill);
    transition: width 0.6s var(--ease-out);
  }
  .board-action {
    font-size: 11px;
    color: var(--brand);
  }

  /* Log terminal */
  .log-terminal {
    background: var(--canvas);
    border: 1px solid var(--hair);
    border-radius: var(--r-md);
    overflow: hidden;
  }
  .log-head {
    display: flex;
    align-items: center;
    gap: 8px;
    background: var(--surface-el);
    border-bottom: 1px solid var(--hair);
    padding: 7px 14px;
    font-size: 11px;
    color: var(--mute);
  }
  .log-body {
    padding: var(--s3) var(--s4);
    min-height: 100px;
    display: flex;
    flex-direction: column;
    gap: 4px;
  }
  .log-line {
    display: flex;
    gap: 8px;
    font-size: 11px;
    line-height: 1.5;
    animation: slideIn 0.2s var(--ease-out);
  }
  .log-t { color: var(--ash); flex-shrink: 0; }
  .log-line.done .log-msg   { color: var(--success); }
  .log-line.join .log-msg   { color: var(--brand); }
  .log-line.update .log-msg { color: var(--body-c); }
  .log-line.system .log-msg { color: var(--ash); }
  .log-msg { color: var(--body-c); }

  /* ── Responsive ── */
  @media (max-width: 1024px) {
    .how-layout, .sync-layout { grid-template-columns: 1fr; gap: 40px; }
    .sec-body { max-width: 100%; }
  }
  @media (max-width: 600px) {
    .how-layout, .sync-layout { gap: 28px; overflow: hidden; }
    .formula-code { font-size: 12px; white-space: pre-wrap; word-break: break-word; }
    .calc-box { padding: var(--s4); }
    .stat-row { flex-wrap: wrap; }
    .board-bar { width: 60px; }
    .sec-headline { font-size: clamp(1.4rem, 5vw, 2rem); }
  }
</style>
