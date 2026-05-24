<script>
  import { onMount } from 'svelte';
  let count = 31;
  onMount(() => {
    const t = setInterval(() => {
      try {
        const list = JSON.parse(localStorage.getItem('st_subs') || '[]');
        count = 31 + list.length;
      } catch {}
    }, 2000);
    return () => clearInterval(t);
  });
</script>

<footer>
  <!-- Echo of the hero stripe — top of footer -->
  <div class="footer-stripe" aria-hidden="true"></div>

  <div class="container footer-inner">

    <!-- Top row: brand + subscriber count -->
    <div class="foot-top">
      <div class="foot-brand">
        <img src="/logo.webp" alt="SmarTask" width="36" height="36" class="foot-logo-img" />
        <span class="mono foot-name">SMARTASK</span>
      </div>
      <div class="sub-counter mono">
        <span class="live-dot"></span>
        {count} mahasiswa terdaftar
      </div>
    </div>

    <div class="foot-divider"></div>

    <!-- Mid: two column link groups -->
    <div class="foot-links">
      <div class="link-col">
        <span class="col-head mono">Produk</span>
        <a href="#how"   class="foot-link">Cara Kerja Prioritas</a>
        <a href="#demo"  class="foot-link">Simulasi WebSocket</a>
        <a href="#stack" class="foot-link">Infrastruktur</a>
        <a href="#join"  class="foot-link">Daftar Akses Awal</a>
      </div>
      <div class="link-col">
        <span class="col-head mono">Tim</span>
        <span class="foot-static">Cahyadi Prasetyo — Flutter & UI</span>
        <span class="foot-static">Oktaviandra — Go Backend</span>
        <span class="foot-static">Muhammad Thesar — Algorithm</span>
        <span class="foot-static">Syawal Rizal Utama — Design</span>
      </div>
      <div class="link-col">
        <span class="col-head mono">Informasi</span>
        <span class="foot-static">Status: <span style="color:var(--success)">Dalam pengembangan</span></span>
        <span class="foot-static">Target rilis: 16 Juni 2026</span>
        <span class="foot-static">Platform: Android (awal)</span>
        <span class="foot-static">Backend: Go 1.22</span>
      </div>
    </div>

    <div class="foot-divider"></div>

    <!-- Bottom row -->
    <div class="foot-bottom">
      <span class="mono foot-copy">© 2026 Tim SmarTask</span>
    </div>

  </div>
</footer>

<style>
  footer {
    background: var(--canvas);
    border-top: 1px solid var(--hair);
    position: relative;
    overflow: hidden;
  }

  /* Small diagonal stripe echo at top of footer */
  .footer-stripe {
    position: absolute;
    top: 0; right: 0;
    width: 320px; height: 120px;
    background: linear-gradient(135deg, var(--stripe-1) 0%, transparent 60%);
    transform: skewX(-18deg);
    transform-origin: top right;
    opacity: 0.4;
    pointer-events: none;
  }

  .footer-inner {
    position: relative;
    z-index: 1;
    padding-top: var(--s12);
    padding-bottom: var(--s12);
  }

  /* Top row */
  .foot-top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: var(--s5);
  }
  .foot-brand {
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .foot-logo-img {
    border-radius: 6px;
    width: 60px;
    height: 60px;
    object-fit: cover;
    flex-shrink: 0;
  }
  .foot-name {
    font-size: 15px;
    font-weight: 600;
    color: var(--body-c);
    letter-spacing: 0.6px;
  }
  .sub-counter {
    display: flex;
    align-items: center;
    gap: 7px;
    font-size: 12px;
    color: var(--mute);
  }
  .live-dot {
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--success);
    box-shadow: 0 0 0 3px rgba(89,212,153,0.15);
    animation: pulseGreen 2s infinite;
  }

  .foot-divider {
    height: 1px;
    background: var(--hair);
    margin: var(--s5) 0;
  }

  /* Link columns */
  .foot-links {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: var(--s10);
    padding: var(--s5) 0;
  }
  .link-col {
    display: flex;
    flex-direction: column;
    gap: 9px;
  }
  .col-head {
    font-size: 11px;
    color: var(--ink);
    font-weight: 600;
    letter-spacing: 0.5px;
    text-transform: uppercase;
    margin-bottom: 4px;
  }
  .foot-link {
    text-decoration: none;
    font-size: 13px;
    color: var(--mute);
    transition: color 0.12s;
  }
  .foot-link:hover { color: var(--ink); }
  .foot-static {
    font-size: 13px;
    color: var(--mute);
    line-height: 1.4;
  }

  /* Bottom */
  .foot-bottom {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: var(--s2);
  }
  .foot-copy {
    font-size: 12px;
    color: var(--ash);
  }

  @media (max-width: 768px) {
    .foot-links { grid-template-columns: 1fr 1fr; }
    .foot-top { flex-direction: column; align-items: flex-start; gap: var(--s3); }
  }
  @media (max-width: 480px) {
    .foot-links { grid-template-columns: 1fr; }
    .foot-bottom { flex-direction: column; align-items: flex-start; }
  }
</style>
