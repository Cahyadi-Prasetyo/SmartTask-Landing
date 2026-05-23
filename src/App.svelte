<script>
  import { onMount } from 'svelte';
  import Header from './lib/components/Header.svelte';
  import Hero from './lib/components/Hero.svelte';
  import InteractiveFeatures from './lib/components/InteractiveFeatures.svelte';
  import TechStack from './lib/components/TechStack.svelte';
  import Footer from './lib/components/Footer.svelte';

  // ── Animated 80% counter ──
  let displayedNum = 0;
  let statRef;
  let statCounted = false;

  function startCount(target = 80, duration = 1600) {
    if (statCounted) return;
    statCounted = true;
    const start = performance.now();
    function frame(now) {
      const progress = Math.min((now - start) / duration, 1);
      // Ease out cubic
      const eased = 1 - Math.pow(1 - progress, 3);
      displayedNum = Math.round(eased * target);
      if (progress < 1) requestAnimationFrame(frame);
    }
    requestAnimationFrame(frame);
  }

  onMount(() => {
    // Global scroll reveal
    const io = new IntersectionObserver((entries) => {
      entries.forEach(e => {
        if (e.isIntersecting) {
          e.target.classList.add('in');
          // Glow rule lines on entry
          const rule = e.target.querySelector?.('.sec-rule-target');
          if (rule) rule.classList.add('sec-rule-glow');
          io.unobserve(e.target);
        }
      });
    }, { threshold: 0.08, rootMargin: '0px 0px -40px 0px' });

    // Stat counter observer (80%)
    const statIo = new IntersectionObserver((entries) => {
      entries.forEach(e => {
        if (e.isIntersecting) { startCount(); statIo.disconnect(); }
      });
    }, { threshold: 0.3 });

    requestAnimationFrame(() => {
      document.querySelectorAll('.reveal').forEach(el => io.observe(el));
      if (statRef) statIo.observe(statRef);
    });

    return () => { io.disconnect(); statIo.disconnect(); };
  });
</script>

<Header />

<main>
  <Hero />

  <!-- ══ PROBLEM STAT SECTION ══
       One big number, no cards. Forces reader to stop.
  ═══════════════════════════════ -->
  <section class="stat-section section-rule">
    <div class="container stat-inner">
      <div class="stat-num-col reveal" bind:this={statRef}>
        <span class="big-num mono">{displayedNum}<span class="pct-sign">%</span></span>
        <span class="big-label">mahasiswa aktif menunda tugas akademik hingga H-1 deadline.*</span>
        <span class="footnote mono">* Survei internal tim, 40 responden mahasiswa aktif, 2025</span>
      </div>
      <div class="stat-text-col reveal d2">
        <h2 class="stat-headline">Pengingat bawaan HP tidak cukup.</h2>
        <p class="stat-body">
          SmarTask tidak hanya mengingatkan — ia <em>menghitung ulang prioritas</em> setiap kali ada perubahan konteks: tugas baru ditambahkan, deadline digeser, anggota tim bergabung. Frekuensi notifikasi eskalasi otomatis seiring deadline mendekat.
        </p>
        <div class="reminder-phases">
          {#each [
            ['H-5', 'Rendah','1× per hari'],
            ['H-3', 'Sedang','2× per hari'],
            ['H-1', 'Tinggi','4× per hari'],
            ['2 jam','Kritis','Setiap 15 mnt']
          ] as [phase, level, freq], i}
            <div class="phase-item reveal" class:d1={i===1} class:d2={i===2} class:d3={i===3}>
              <div class="phase-top">
                <span class="phase-label mono">{phase}</span>
                <span class="phase-badge {level.toLowerCase()}">{level}</span>
              </div>
              <span class="phase-freq">{freq}</span>
            </div>
          {/each}
        </div>
      </div>
    </div>
  </section>

  <InteractiveFeatures />
  <TechStack />
</main>

<Footer />

<style>
  main { background: var(--canvas); }

  /* ── Stat Section ── */
  .stat-section {
    background: var(--canvas);
  }
  .stat-inner {
    display: grid;
    grid-template-columns: 1fr 1.1fr;
    gap: 80px;
    align-items: start;
    padding: var(--s10) 0 var(--s10);
  }

  .stat-num-col {
    display: flex;
    flex-direction: column;
    gap: var(--s3);
    padding-top: var(--s8);
  }
  .big-num {
    font-size: clamp(4.5rem, 9vw, 7.5rem);
    font-weight: 700;
    color: var(--ink);
    line-height: 0.9;
    letter-spacing: -0.04em;
    display: block;
  }
  .pct-sign {
    font-size: 0.55em;
    color: var(--mute);
    vertical-align: super;
    font-weight: 400;
  }
  .big-label {
    font-size: 15px;
    color: var(--body-c);
    line-height: 1.55;
    max-width: 320px;
  }
  .footnote {
    font-size: 11px;
    color: var(--ash);
    margin-top: var(--s2);
  }

  .stat-text-col {
    display: flex;
    flex-direction: column;
    gap: var(--s5);
    padding-top: var(--s8);
    border-left: 1px solid var(--hair);
    padding-left: 80px;
  }

  .stat-headline {
    font-size: clamp(1.4rem, 2.5vw, 2rem);
    font-weight: 600;
    color: var(--ink);
    letter-spacing: -0.025em;
    line-height: 1.2;
  }
  .stat-body {
    font-size: 15px;
    color: var(--body-c);
    line-height: 1.7;
  }
  .stat-body em {
    font-style: normal;
    color: var(--ink);
  }

  /* Reminder phase row — horizontal, not cards */
  .reminder-phases {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    border: 1px solid var(--hair);
    border-radius: var(--r-md);
    overflow: hidden;
    margin-top: var(--s2);
  }
  .phase-item {
    display: flex;
    flex-direction: column;
    gap: 5px;
    padding: var(--s4) var(--s3);
    border-right: 1px solid var(--hair);
    transition: background 0.15s;
  }
  .phase-item:last-child { border-right: none; }
  .phase-item:hover { background: var(--surface); }

  .phase-top {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .phase-label {
    font-size: 13px;
    font-weight: 600;
    color: var(--ink);
  }
  .phase-badge {
    font-size: 10px;
    font-weight: 600;
    padding: 1px 6px;
    border-radius: var(--r-xs);
  }
  .phase-badge.rendah  { background: rgba(89,212,153,0.12); color: var(--success); }
  .phase-badge.sedang  { background: rgba(255,197,51,0.12);  color: var(--warn); }
  .phase-badge.tinggi  { background: rgba(255,97,97,0.12);   color: var(--error); }
  .phase-badge.kritis  { background: rgba(255,97,97,0.18);   color: var(--error); animation: stripeShimmer 1.5s infinite; }
  .phase-freq {
    font-size: 12px;
    color: var(--mute);
    margin-top: 2px;
  }

  @media (max-width: 1024px) {
    .stat-inner { grid-template-columns: 1fr; gap: 40px; }
    .stat-text-col { border-left: none; padding-left: 0; border-top: 1px solid var(--hair); padding-top: var(--s5); }
    .reminder-phases { grid-template-columns: repeat(2,1fr); }
  }
  @media (max-width: 600px) {
    .reminder-phases { grid-template-columns: 1fr; }
  }
</style>
