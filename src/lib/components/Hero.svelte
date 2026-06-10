<script>
  import { onMount } from 'svelte';

  /* ── Countdown with flip animation ── */
  const TARGET = new Date('2026-06-11T00:00:00+07:00').getTime();
  const initialDiff = Math.max(0, TARGET - Date.now());
  let d = Math.floor(initialDiff / 86400000);
  let h = Math.floor((initialDiff % 86400000) / 3600000);
  let m = Math.floor((initialDiff % 3600000) / 60000);
  let s = Math.floor((initialDiff % 60000) / 1000);
  let prevS = -1, flipS = false;

  function tick() {
    const diff = TARGET - Date.now();
    if (diff < 0) return;
    const ns = Math.floor((diff % 60000) / 1000);
    // Flip animation trigger when seconds change
    if (ns !== prevS && prevS !== -1) {
      flipS = false;
      requestAnimationFrame(() => { flipS = true; });
      setTimeout(() => { flipS = false; }, 250);
    }
    prevS = ns;
    d = Math.floor(diff / 86400000);
    h = Math.floor((diff % 86400000) / 3600000);
    m = Math.floor((diff % 3600000) / 60000);
    s = ns;
  }

  /* ── Command palette ── */
  const ALL_TASKS = [
    { pri: 'urgent', pLabel: '⚠',  name: 'Laporan Jarkom — Bab IV',    dead: 'Hari ini', score: 9.2, by: 'Cahyadi' },
    { pri: 'high',   pLabel: '●',  name: 'Implementasi Priority Go',    dead: 'Besok',    score: 7.4, by: 'Oktaviandra' },
    { pri: 'medium', pLabel: '◐',  name: 'Wireframe Landing Page',      dead: 'H-3',      score: 5.1, by: 'Thesar' },
    { pri: 'low',    pLabel: '○',  name: 'Review IEEE Paper Skripsi',   dead: 'H-7',      score: 3.0, by: 'Syawal' },
  ];

  /** @type {any[]} */
  let visible = [];
  let active  = 0;
  let cursor  = true;
  let searchQ = '';
  let searchDone = false;
  let scoreVisible = false;

  // Ambient glow color based on active task priority
  $: glowClr = ALL_TASKS[active]?.pri === 'urgent' ? '#ff6161'
             : ALL_TASKS[active]?.pri === 'high'   ? '#fb923c'
             : ALL_TASKS[active]?.pri === 'medium' ? '#ffc533'
             : '#6366f1';

  /* ── Magnetic CTA ── */
  /** @type {any} */
  let magRef;
  /** @param {any} e */
  function onMagMove(e) {
    if (!magRef) return;
    const r = magRef.getBoundingClientRect();
    const x = (e.clientX - r.left - r.width  / 2) * 0.25;
    const y = (e.clientY - r.top  - r.height / 2) * 0.25;
    magRef.style.transform = `translate(${x}px, ${y}px)`;
  }
  function onMagLeave() {
    if (!magRef) return;
    magRef.style.transform = '';
    magRef.style.transition = 'transform 0.55s cubic-bezier(0.16,1,0.3,1)';
    setTimeout(() => { if (magRef) magRef.style.transition = ''; }, 560);
  }

  /* ── Palette parallax on scroll ── */
  let palRef;
  let palOffset = 0;
  function onScroll() {
    palOffset = window.scrollY * 0.06;
  }

  onMount(() => {
    tick();
    const clock = setInterval(tick, 1000);
    window.addEventListener('scroll', onScroll, { passive: true });

    // Reveal tasks staggered
    ALL_TASKS.forEach((t, i) => {
      setTimeout(() => { visible = [...visible, t]; }, 350 + i * 160);
    });

    // Typewriter search
    const q = 'Jarkom Bab IV';
    let qi = 0;
    const typer = setInterval(() => {
      if (qi < q.length) { searchQ = q.slice(0, ++qi); }
      else { clearInterval(typer); searchDone = true; setTimeout(() => { searchQ = ''; searchDone = false; }, 2000); }
    }, 80);

    // Cycle active row
    const cycle = setInterval(() => {
      active = (active + 1) % ALL_TASKS.length;
      scoreVisible = true;
    }, 2800);

    // Cursor blink
    const blinker = setInterval(() => { cursor = !cursor; }, 500);

    return () => {
      clearInterval(clock); clearInterval(cycle);
      clearInterval(blinker); clearInterval(typer);
      window.removeEventListener('scroll', onScroll);
    };
  });

  /** @param {any} n */
  function p2(n) { return String(n).padStart(2, '0'); }


</script>

<section class="hero">
  <!-- Diagonal stripes — SmarTask signature -->
  <div class="stripes" aria-hidden="true">
    <div class="stripe s1"></div>
    <div class="stripe s2"></div>
    <div class="stripe s3"></div>
  </div>

  <div class="container hero-layout">

    <!-- ── Left: Copy (hero entrance stagger) ── -->
    <div class="hero-copy">
      <div class="eyebrow-row hero-enter he-0">
        <span class="badge badge-brand">
          <span class="live-dot"></span>
          Segera Rilis
        </span>
        <span class="badge" style="border-color:transparent;background:transparent;color:var(--mute)">
          11 Juni 2026
        </span>
      </div>

      <h1 class="headline hero-enter he-1">
        <span class="h-dim">Kelola Tugas Kuliah</span>
        <br>
        <span class="h-white">dengan Cerdas.</span>
      </h1>

      <p class="sub hero-enter he-2">
        SmarTask menghitung prioritas tugas secara otomatis dari peladen Go, mengirim pengingat adaptif, dan menyinkronkan tim mahasiswa via WebSocket — tanpa polling.
      </p>

      <!-- CTA Row with magnetic effect -->
      <div class="cta-row hero-enter he-3">
        <!-- svelte-ignore a11y-mouse-events-have-key-events -->
        <span
          class="magnetic"
          bind:this={magRef}
          on:mousemove={onMagMove}
          on:mouseleave={onMagLeave}
        >
          <a href="/smartask.apk" download class="btn btn-primary cta-main" id="hero-download">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" style="margin-right:2px">
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/>
              <polyline points="7 10 12 15 17 10"/>
              <line x1="12" y1="15" x2="12" y2="3"/>
            </svg>
            Download APK
          </a>
        </span>
        <a href="#how" class="btn btn-ghost">Cara kerjanya →</a>
      </div>

      <!-- Countdown — raw mono digits, flip on seconds change -->
      <div class="countdown hero-enter he-4">
        <span class="cd-label mono">Rilis dalam</span>
        <div class="cd-nums">
          {#each [[d,'hr',false],[h,'jam',false],[m,'mnt',false],[s,'dtk',flipS]] as [val,lbl,doFlip], i}
            {#if i > 0}<span class="cd-sep mono">:</span>{/if}
            <div class="cd-unit">
              <span class="cd-num mono" class:flip-anim={doFlip}>
                {i === 0 ? val : p2(val)}
              </span>
              <span class="cd-lbl">{lbl}</span>
            </div>
          {/each}
        </div>
      </div>
    </div>

    <!-- ── Right: Command Palette Mockup ── -->
    <div class="palette-wrap hero-enter he-5" bind:this={palRef} style="transform: translateY({palOffset}px)">

      <!-- Ambient glow — changes with active priority -->
      <div class="ambient-glow" style="background: radial-gradient({glowClr}, transparent 70%); bottom:-60px; right:-60px; transition: background 0.8s ease;"></div>

      <div class="palette">
        <!-- Title bar -->
        <div class="pal-bar">
          <div class="traffic">
            <span class="dot red"></span>
            <span class="dot amber"></span>
            <span class="dot green"></span>
          </div>
          <span class="pal-title mono">SmarTask — Prioritas Aktif</span>
        </div>

        <!-- Search -->
        <div class="pal-search">
          <span class="search-icon">⌕</span>
          <span class="search-text">{searchQ}</span>
          <span class="search-cursor" style="opacity:{cursor?1:0}">|</span>
        </div>

        <div class="pal-divider"></div>
        <div class="pal-section-label mono">TUGAS AKTIF · {ALL_TASKS.length} item</div>

        <!-- Task rows -->
        <div class="pal-rows">
          {#each visible as t, i}
            <div
              class="pal-row {t.pri}"
              class:is-active={active === i}
              style="animation: slideIn 0.2s {i*25}ms var(--ease-out) both"
            >
              <span class="pri-icon {t.pri}">{t.pLabel}</span>
              <div class="row-mid">
                <span class="row-name">{t.name}</span>
                <div class="row-meta">
                  <span class="mono" style="font-size:11px;color:var(--ash)">@{t.by}</span>
                  {#if active === i && scoreVisible}
                    <span class="score-pill mono">{t.score}</span>
                  {/if}
                </div>
              </div>
              <div class="row-right">
                <span class="keycap">{t.dead}</span>
                {#if active === i}
                  <span class="keycap" style="margin-left:3px">⏎</span>
                {/if}
              </div>
            </div>
          {/each}
          {#if visible.length < ALL_TASKS.length}
            <div class="pal-loading mono">Memuat{cursor?'...':'...'}</div>
          {/if}
        </div>

        <!-- Footer keycaps -->
        <div class="pal-footer">
          {#each [['⌘ K','buka'],['↑ ↓','navigasi'],['Esc','tutup']] as [k,v]}
            <div class="key-hint">
              <span class="keycap">{k}</span>
              <span>{v}</span>
            </div>
          {/each}
        </div>
      </div>

      <!-- Annotation -->
      <div class="annotation">
        <span class="dot-brand"></span>
        <span class="mono" style="font-size:11px;color:var(--mute)">Skor dihitung peladen Go — bukan di browser</span>
      </div>
    </div>
  </div>

</section>

<style>
  .hero {
    position: relative;
    padding-top: calc(64px + 72px);
    overflow: hidden;
    background: var(--canvas);
  }

  /* Diagonal stripes */
  .stripes {
    position: absolute;
    top: 0; right: -60px;
    width: 680px; height: 420px;
    pointer-events: none;
    z-index: 0;
    transform: skewX(-18deg) skewY(-4deg);
    transform-origin: top right;
  }
  .stripe {
    position: absolute;
    width: 100%; height: 100%;
    top: 0;
    animation: stripeShimmer 6s ease-in-out infinite;
  }
  .s1 { background: linear-gradient(135deg, var(--stripe-1) 0%, transparent 60%); animation-delay: 0s; }
  .s2 { background: linear-gradient(135deg, transparent 15%, var(--stripe-2) 35%, transparent 65%); animation-delay: 1.5s; }
  .s3 { background: linear-gradient(135deg, transparent 35%, var(--stripe-3) 55%, transparent 80%); animation-delay: 3s; }

  /* Layout */
  .hero-layout {
    position: relative;
    z-index: 1;
    display: grid;
    grid-template-columns: 1.05fr 0.95fr;
    align-items: center;
    gap: 60px;
    padding-bottom: 72px;
  }

  /* Copy */
  .hero-copy {
    display: flex;
    flex-direction: column;
    gap: 24px;
  }

  .eyebrow-row {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .live-dot {
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--success);
    animation: pulseGreen 2s infinite;
    flex-shrink: 0;
  }

  .headline {
    font-size: clamp(2.4rem, 4vw + 0.6rem, 3.75rem);
    font-weight: 400;
    line-height: 1.08;
    letter-spacing: -0.02em;
  }
  .h-dim   { color: var(--mute); font-weight: 300; }
  .h-white { color: var(--ink);  font-weight: 600; }

  .sub {
    color: var(--body-c);
    font-size: 15px;
    line-height: 1.65;
    max-width: 480px;
  }

  /* CTA */
  .cta-row {
    display: flex;
    align-items: center;
    gap: 12px;
    flex-wrap: wrap;
  }

  /* Magnetic wrapper */
  .magnetic {
    display: inline-block;
    will-change: transform;
  }
  /* Primary CTA subtle glow on hover */
  .cta-main {
    position: relative;
    overflow: visible;
  }
  .cta-main::after {
    content: '';
    position: absolute;
    inset: -4px;
    border-radius: calc(var(--r-md) + 4px);
    background: rgba(255,255,255,0.08);
    opacity: 0;
    transition: opacity 0.2s;
    pointer-events: none;
  }
  .cta-main:hover::after { opacity: 1; }

  /* Countdown */
  .countdown {
    display: flex;
    flex-direction: column;
    gap: 6px;
    padding-top: 4px;
  }
  .cd-label {
    font-size: 11px;
    color: var(--ash);
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }
  .cd-nums {
    display: flex;
    align-items: baseline;
    gap: 4px;
  }
  .cd-unit {
    display: flex;
    flex-direction: column;
    align-items: center;
    min-width: 42px;
  }
  .cd-num {
    font-size: 1.75rem;
    font-weight: 500;
    color: var(--ink);
    line-height: 1;
    display: block;
  }
  .cd-lbl {
    font-size: 10px;
    color: var(--ash);
    margin-top: 3px;
    letter-spacing: 0.3px;
  }
  .cd-sep {
    color: var(--hair-strong);
    font-size: 1.4rem;
    padding-bottom: 14px;
  }

  /* Palette */
  .palette-wrap {
    position: relative;
    display: flex;
    flex-direction: column;
    gap: 10px;
    transition: transform 0.1s linear;
    will-change: transform;
  }

  .ambient-glow {
    position: absolute;
    width: 260px; height: 260px;
    border-radius: 50%;
    filter: blur(60px);
    pointer-events: none;
    animation: ambientDrift 7s ease-in-out infinite;
    z-index: 0;
  }

  .palette {
    position: relative;
    z-index: 1;
    background: var(--surface);
    border: 1px solid var(--hair);
    border-radius: var(--r-xl);
    overflow: hidden;
    box-shadow: 0 0 0 1px var(--hair-soft) inset, 0 24px 60px rgba(0,0,0,0.55);
  }

  .pal-bar {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 10px 14px;
    border-bottom: 1px solid var(--hair);
    background: var(--surface-el);
  }
  .traffic { display: flex; gap: 5px; }
  .dot { width: 10px; height: 10px; border-radius: 50%; flex-shrink: 0; }
  .dot.red   { background: #ff5f57; }
  .dot.amber { background: #ffbd2e; }
  .dot.green { background: #28c940; }
  .pal-title { font-size: 12px; color: var(--mute); }

  .pal-search {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 10px 14px;
    background: var(--surface-el);
    border-bottom: 1px solid var(--hair);
  }
  .search-icon { color: var(--ash); font-size: 15px; }
  .search-text { font-size: 14px; color: var(--ink); min-width: 6ch; }
  .search-cursor { font-size: 14px; color: var(--brand); font-weight: 300; }

  .pal-section-label {
    padding: 6px 14px 4px;
    font-size: 10px;
    color: var(--ash);
    letter-spacing: 0.6px;
    text-transform: uppercase;
  }
  .pal-divider { height: 1px; background: var(--hair); }

  .pal-rows { display: flex; flex-direction: column; padding: 2px 0; }

  .pal-row {
    display: grid;
    grid-template-columns: 22px 1fr auto;
    align-items: center;
    gap: 8px;
    padding: 7px 14px;
    border-radius: var(--r-sm);
    margin: 0 4px;
    cursor: default;
    transition: background 0.2s var(--ease-out);
  }
  .pal-row.is-active { background: var(--surface-cd); }

  .pri-icon { font-size: 13px; text-align: center; flex-shrink: 0; transition: transform 0.2s; }
  .pal-row.is-active .pri-icon { transform: scale(1.15); }
  .pri-icon.urgent { color: #ff6161; }
  .pri-icon.high   { color: #fb923c; }
  .pri-icon.medium { color: #ffc533; }
  .pri-icon.low    { color: #6366f1; }

  .row-mid { display: flex; flex-direction: column; gap: 2px; min-width: 0; overflow: hidden; }
  .row-name {
    font-size: 13px; font-weight: 500; color: var(--ink);
    white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
  }
  .row-meta { display: flex; align-items: center; gap: 6px; }
  .score-pill {
    font-size: 10px;
    background: var(--brand-muted);
    color: var(--brand);
    border: 1px solid rgba(99,102,241,0.3);
    padding: 0 5px;
    border-radius: var(--r-xs);
    animation: fadeUp 0.2s var(--ease-out);
  }

  .row-right { display: flex; align-items: center; gap: 3px; flex-shrink: 0; }

  .pal-loading { font-size: 12px; color: var(--ash); padding: 6px 14px; animation: blink 1s infinite; }

  .pal-footer {
    display: flex; gap: 16px;
    padding: 8px 14px;
    border-top: 1px solid var(--hair);
    background: var(--surface-el);
  }
  .key-hint { display: flex; align-items: center; gap: 5px; font-size: 11px; color: var(--ash); }

  .annotation { display: flex; align-items: center; gap: 7px; padding-left: 4px; }
  .dot-brand { width: 5px; height: 5px; border-radius: 50%; background: var(--brand); flex-shrink: 0; }

  /* Subscribe bar */
  .sub-bar {
    display: flex;
    align-items: center;
    gap: 16px;
    flex-wrap: wrap;
    padding-top: 20px;
    padding-bottom: 20px;
    border-top: 1px solid var(--hair);
  }
  .sub-label { font-size: 13px; color: var(--mute); white-space: nowrap; }
  .sub-form {
    display: flex;
    align-items: center;
    background: var(--surface-el);
    border: 1px solid var(--hair);
    border-radius: var(--r-md);
    overflow: hidden;
    flex: 1;
    max-width: 440px;
    transition: border-color 0.2s, box-shadow 0.2s;
  }
  .sub-form:focus-within {
    border-color: rgba(99,102,241,0.5);
    box-shadow: 0 0 0 3px rgba(99,102,241,0.08);
  }
  .sub-input {
    flex: 1; background: none; border: none; outline: none;
    color: var(--ink); font-size: 13px; font-family: inherit;
    padding: 0 12px; height: 36px; min-width: 0;
  }
  .sub-input::placeholder { color: var(--ash); }
  .sub-submit { border-radius: 0; border-left: 1px solid var(--hair); height: 36px; flex-shrink: 0; }
  .sub-err { font-size: 12px; color: var(--error); font-family: monospace; }
  .sub-success { display: flex; align-items: center; gap: 8px; }
  .success-icon {
    width: 20px; height: 20px; border-radius: 50%;
    background: var(--success); color: #000;
    font-size: 11px; font-weight: 700;
    display: flex; align-items: center; justify-content: center;
  }

  @media (max-width: 1024px) {
    .hero-layout { grid-template-columns: 1fr; gap: 48px; }
    .headline { font-size: clamp(2rem, 6vw, 3rem); }
  }
  @media (max-width: 600px) {
    .hero { padding-top: calc(64px + 48px); }
    .stripes { display: none; }
    .sub-bar { flex-direction: column; align-items: flex-start; }
    .sub-form { max-width: 100%; width: 100%; }
  }
</style>
