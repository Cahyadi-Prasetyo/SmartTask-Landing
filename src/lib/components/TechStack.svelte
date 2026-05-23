<script>
  const stack = [
    { name: 'Flutter',     sub: 'Mobile UI',          note: 'Dart SDK · Android' },
    { name: 'Riverpod',    sub: 'State Management',   note: 'Dart · Reactive' },
    { name: 'Golang',      sub: 'REST + WebSocket',   note: 'Backend core' },
    { name: 'PostgreSQL',  sub: 'Relational DB',      note: 'Primary storage' },
    { name: 'Redis',       sub: 'In-memory cache',    note: 'Priority scoring' },
    { name: 'Drift',       sub: 'Local DB',           note: 'Offline-first' },
  ];
</script>

<section id="stack" class="section-rule">
  <div class="container">

    <div class="sec-header reveal">
      <span class="mono" style="font-size:11px;color:var(--ash);letter-spacing:0.5px">03 — INFRASTRUKTUR</span>
      <div class="sec-rule-line"></div>
    </div>

    <!-- Single-line headline, then horizontal tech list -->
    <div class="tech-head reveal">
      <h2 class="tech-title">Stack yang dipilih dengan alasan, bukan tren.</h2>
    </div>

    <!-- Horizontal strip — not cards -->
    <div class="tech-strip">
      {#each stack as t, i}
        <div class="tech-item reveal" class:d1={i===1} class:d2={i===2} class:d3={i===3} class:d4={i===4}>
          <span class="tech-name">{t.name}</span>
          <span class="tech-sub">{t.sub}</span>
          <span class="tech-note mono">{t.note}</span>
        </div>
        {#if i < stack.length - 1}
          <div class="tech-sep"></div>
        {/if}
      {/each}
    </div>

    <!-- Bottom note -->
    <div class="tech-note-row reveal">
      <span class="mono" style="font-size:11px;color:var(--ash)">Semua layanan berjalan di mesin yang sama selama development. Arsitektur siap untuk deploy terpisah di production.</span>
    </div>

  </div>
</section>

<style>
  .sec-header {
    display: flex;
    align-items: center;
    gap: var(--s4);
    padding: var(--s10) 0 var(--s8);
  }
  .sec-rule-line {
    flex: 1;
    height: 1px;
    background: var(--hair);
  }

  .tech-head {
    padding-bottom: var(--s8);
  }
  .tech-title {
    font-size: clamp(1.5rem, 2.5vw + 0.4rem, 2.25rem);
    font-weight: 500;
    color: var(--ink);
    letter-spacing: -0.02em;
    line-height: 1.15;
    max-width: 600px;
  }

  /* Horizontal strip — items separated by thin vertical lines */
  .tech-strip {
    display: flex;
    align-items: stretch;
    border-top: 1px solid var(--hair);
    border-bottom: 1px solid var(--hair);
    margin-bottom: var(--s6);
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
    scrollbar-width: none;
  }
  .tech-strip::-webkit-scrollbar { display: none; }

  .tech-item {
    display: flex;
    flex-direction: column;
    gap: 5px;
    padding: var(--s5) var(--s6);
    min-width: 140px;
    flex: 1;
  }
  .tech-item:hover { background: var(--surface); }
  .tech-item:hover .tech-name { color: var(--ink); }

  .tech-name {
    font-size: 15px;
    font-weight: 600;
    color: var(--body-c);
    letter-spacing: -0.01em;
    transition: color 0.15s;
  }
  .tech-sub {
    font-size: 12px;
    color: var(--mute);
  }
  .tech-note {
    font-size: 11px;
    color: var(--ash);
    margin-top: 2px;
  }

  .tech-sep {
    width: 1px;
    background: var(--hair);
    flex-shrink: 0;
    align-self: stretch;
  }

  .tech-note-row {
    padding-bottom: var(--s10);
  }
</style>
