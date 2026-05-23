<script>
  import { onMount } from 'svelte';
  let scrolled = false;
  let menuOpen = false;
  let progress = 0;

  onMount(() => {
    const onScroll = () => {
      scrolled = window.scrollY > 10;
      const max = document.documentElement.scrollHeight - window.innerHeight;
      progress = max > 0 ? (window.scrollY / max) * 100 : 0;
    };
    window.addEventListener('scroll', onScroll, { passive: true });
    return () => window.removeEventListener('scroll', onScroll);
  });
</script>

<!-- Scroll progress bar -->
<div id="scroll-progress" style="width: {progress}%"></div>

<header class:scrolled>
  <div class="container nav-row">
    <!-- Logo -->
    <a href="/" class="logo" on:click={() => menuOpen = false}>
      <img src="/logo.webp" alt="SmarTask" width="42" height="42" class="logo-img" />
      <span class="logo-text mono">SMARTASK</span>
    </a>

    <!-- Center links -->
    <nav class="nav-links">
      <a href="#how" class="nav-link">Cara Kerja</a>
      <a href="#demo" class="nav-link">Demo</a>
      <a href="#stack" class="nav-link">Teknologi</a>
    </nav>

    <!-- Right cluster -->
    <div class="nav-right">
      <a href="#join" class="btn btn-primary">Download Soon</a>
      <button class="burger" aria-label="menu" on:click={() => menuOpen = !menuOpen} class:open={menuOpen}>
        <span></span><span></span><span></span>
      </button>
    </div>
  </div>

  <!-- Mobile overlay -->
  {#if menuOpen}
    <div class="mob-menu">
      {#each [['#how','Cara Kerja'],['#demo','Demo'],['#stack','Teknologi'],['#join','Daftar Akses Awal']] as [href,label], i}
        <a {href} class="mob-link" style="animation-delay:{i*40}ms" on:click={() => menuOpen = false}>{label}</a>
      {/each}
    </div>
  {/if}
</header>

<style>
  header {
    position: fixed;
    top: 0; left: 0;
    width: 100%;
    height: 64px;
    z-index: 300;
    background: rgba(7,8,10,0.7);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    border-bottom: 1px solid transparent;
    transition: border-color 0.2s, background 0.2s;
  }
  header.scrolled {
    border-bottom-color: var(--hair);
    background: rgba(7,8,10,0.92);
  }

  .nav-row {
    height: 64px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: var(--s6);
  }

  /* Logo */
  .logo {
    display: flex;
    align-items: center;
    gap: 10px;
    text-decoration: none;
    color: var(--ink);
    flex-shrink: 0;
  }
  .logo-img {
    border-radius: 8px;
    width: 42px;
    height: 42px;
    object-fit: cover;
    flex-shrink: 0;
  }
  .logo-text {
    font-size: 16px;
    font-weight: 600;
    letter-spacing: 0.8px;
    color: var(--ink);
  }

  /* Center nav */
  .nav-links {
    display: flex;
    align-items: center;
    gap: 2px;
    flex: 1;
    justify-content: center;
  }
  .nav-link {
    text-decoration: none;
    color: var(--body-c);
    font-size: 14px;
    font-weight: 400;
    padding: 5px 10px;
    border-radius: var(--r-pill);
    transition: color 0.12s, background 0.12s;
    letter-spacing: 0;
  }
  .nav-link:hover { color: var(--ink); background: var(--surface-el); }

  /* Right */
  .nav-right {
    display: flex;
    align-items: center;
    gap: var(--s2);
    flex-shrink: 0;
  }

  /* Burger */
  .burger {
    display: none;
    flex-direction: column;
    justify-content: center;
    gap: 4px;
    width: 28px;
    height: 28px;
    background: none;
    border: none;
    cursor: pointer;
    padding: 4px;
  }
  .burger span {
    display: block;
    height: 1.5px;
    background: var(--body-c);
    border-radius: var(--r-pill);
    transform-origin: center;
    transition: transform 0.2s var(--ease-out), opacity 0.2s;
  }
  .burger.open span:nth-child(1) { transform: translateY(5.5px) rotate(45deg); }
  .burger.open span:nth-child(2) { opacity: 0; }
  .burger.open span:nth-child(3) { transform: translateY(-5.5px) rotate(-45deg); }

  /* Mobile menu */
  .mob-menu {
    position: fixed;
    top: 64px; left: 0;
    width: 100%;
    background: var(--surface);
    border-bottom: 1px solid var(--hair);
    padding: var(--s4) var(--s6);
    display: flex;
    flex-direction: column;
    gap: 2px;
  }
  .mob-link {
    text-decoration: none;
    color: var(--body-c);
    font-size: 15px;
    padding: var(--s3) var(--s3);
    border-radius: var(--r-sm);
    transition: color 0.1s, background 0.1s;
    animation: fadeUp 0.2s var(--ease-out) both;
  }
  .mob-link:hover { color: var(--ink); background: var(--surface-el); }

  @media (max-width: 768px) {
    .nav-links { display: none; }
    .burger { display: flex; }
  }
  @media (max-width: 480px) {
    .nav-right .btn-primary { display: none; }
  }
</style>
