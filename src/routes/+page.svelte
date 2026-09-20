<script lang="ts">
  import { onMount, onDestroy } from 'svelte';

  // --- Configuration ---
  const VALID_TOKEN = 'INGGRIS11';
  const EXAM_DURATION_MINUTES = 40;
  const INITIAL_TIME_SECONDS = EXAM_DURATION_MINUTES * 60; // 2400 seconds
  const GOOGLE_FORM_URL = 'https://docs.google.com/forms/d/e/1FAIpQLSe7Py8G9l9XMAo-O4H4cZA9O9iQ_2oVAl_7HD0-kU7No2hDPQ/viewform?embedded=true';

  // --- Reactive State (Svelte 5 Runes) ---
  let isAuthenticated = $state(false);
  let timeLeft = $state(INITIAL_TIME_SECONDS);
  let tokenInput = $state('');
  let errorMessage = $state('');
  let isTimeExpired = $state(false);

  // Interval reference for timer cleanup
  let timerInterval: ReturnType<typeof setInterval> | null = null;

  // --- Formatted Time Helper (HH:MM:SS or MM:SS) ---
  const formattedTime = $derived.by(() => {
    const hours = Math.floor(timeLeft / 3600);
    const minutes = Math.floor((timeLeft % 3600) / 60);
    const seconds = timeLeft % 60;

    const pad = (n: number) => n.toString().padStart(2, '0');

    if (hours > 0) {
      return `${pad(hours)}:${pad(minutes)}:${pad(seconds)}`;
    }
    return `${pad(minutes)}:${pad(seconds)}`;
  });

  // --- Timer Controls ---
  function startTimer() {
    if (timerInterval) clearInterval(timerInterval);

    timerInterval = setInterval(() => {
      if (timeLeft > 0) {
        timeLeft -= 1;
      } else {
        stopTimer();
        isTimeExpired = true;
      }
    }, 1000);
  }

  function stopTimer() {
    if (timerInterval) {
      clearInterval(timerInterval);
      timerInterval = null;
    }
  }

  // --- Token Validation ---
  function handleTokenSubmit(event: SubmitEvent) {
    event.preventDefault();
    errorMessage = '';

    const cleanInput = tokenInput.trim().toUpperCase();

    if (cleanInput === VALID_TOKEN) {
      isAuthenticated = true;
      startTimer();
    } else {
      errorMessage = 'Token yang Anda masukkan salah. Silakan coba lagi.';
    }
  }

  // --- Security & Event Listeners ---
  function preventContextMenu(e: MouseEvent) {
    e.preventDefault();
  }

  onMount(() => {
    // Disable right-click context menu globally
    window.addEventListener('contextmenu', preventContextMenu);
  });

  onDestroy(() => {
    stopTimer();
    if (typeof window !== 'undefined') {
      window.removeEventListener('contextmenu', preventContextMenu);
    }
  });
</script>

<div class="exam-app">
  {#if !isAuthenticated}
    <!-- LOGIN / TOKEN FORM VIEW -->
    <div class="login-container">
      <div class="login-card">
        <div class="icon-badge">🔒</div>
        <h1>Portal Ujian Online</h1>
        <p class="subtitle">Silakan masukkan token ujian untuk memulai sesi Anda (Durasi: {EXAM_DURATION_MINUTES} Menit).</p>

        <form onsubmit={handleTokenSubmit} class="token-form">
          <div class="field-group">
            <label for="token">Token Ujian</label>
            <input
              id="token"
              type="text"
              bind:value={tokenInput}
              placeholder="Contoh: INGGRIS11"
              autocomplete="off"
              required
            />
          </div>

          {#if errorMessage}
            <div class="error-banner">
              ⚠️ {errorMessage}
            </div>
          {/if}

          <button type="submit" class="btn-submit">
            Mulai Ujian
          </button>
        </form>
      </div>
    </div>
  {:else if isTimeExpired}
    <!-- TIME EXPIRED VIEW -->
    <div class="expired-container">
      <div class="expired-card">
        <div class="expired-icon">⏳</div>
        <h2>Waktu Ujian Telah Habis</h2>
        <p>Sesi ujian Anda telah berakhir secara otomatis. Terima kasih telah mengikuti ujian ini.</p>
      </div>
    </div>
  {:else}
    <!-- EXAM VIEW (AUTHENTICATED) -->
    <div class="exam-wrapper">
      <!-- TOP TIMER BAR -->
      <header class="top-bar">
        <div class="exam-title">
          <span class="pulse-indicator"></span>
          <span>Sesi Ujian Aktif</span>
        </div>
        <div class="timer-badge" class:warning={timeLeft < 300}>
          <span class="timer-label">Sisa Waktu:</span>
          <span class="timer-value">{formattedTime}</span>
        </div>
      </header>

      <!-- FULL-SCREEN GOOGLE FORM IFRAME -->
      <main class="iframe-container">
        <iframe
          src={GOOGLE_FORM_URL}
          title="Google Form Exam"
          frameborder="0"
          marginheight="0"
          marginwidth="0"
        >
          Memuat halaman ujian...
        </iframe>
      </main>
    </div>
  {/if}
</div>

<style>
  :global(body, html) {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
    background-color: #f8fafc;
    color: #0f172a;
    user-select: none; /* Disables text selection for added exam security */
  }

  .exam-app {
    width: 100vw;
    height: 100vh;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  /* --- LOGIN STYLES --- */
  .login-container {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 1.5rem;
    background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%);
  }

  .login-card {
    background: #ffffff;
    width: 100%;
    max-width: 420px;
    padding: 2.5rem;
    border-radius: 16px;
    box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.2), 0 8px 10px -6px rgba(0, 0, 0, 0.2);
    text-align: center;
  }

  .icon-badge {
    font-size: 2.5rem;
    margin-bottom: 0.75rem;
  }

  .login-card h1 {
    font-size: 1.65rem;
    font-weight: 700;
    margin: 0 0 0.5rem 0;
    color: #0f172a;
  }

  .subtitle {
    font-size: 0.9rem;
    color: #64748b;
    margin-bottom: 1.75rem;
    line-height: 1.5;
  }

  .token-form {
    display: flex;
    flex-direction: column;
    gap: 1.25rem;
    text-align: left;
  }

  .field-group {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .field-group label {
    font-size: 0.85rem;
    font-weight: 600;
    color: #334155;
    text-transform: uppercase;
    letter-spacing: 0.05em;
  }

  .field-group input {
    padding: 0.85rem 1rem;
    font-size: 1rem;
    border: 2px solid #e2e8f0;
    border-radius: 8px;
    outline: none;
    transition: border-color 0.2s;
    text-align: center;
    letter-spacing: 0.1em;
    font-weight: 600;
    text-transform: uppercase;
  }

  .field-group input:focus {
    border-color: #2563eb;
  }

  .error-banner {
    background-color: #fef2f2;
    color: #dc2626;
    border: 1px solid #fecaca;
    padding: 0.75rem;
    border-radius: 8px;
    font-size: 0.875rem;
    text-align: center;
  }

  .btn-submit {
    background-color: #2563eb;
    color: #ffffff;
    font-size: 1rem;
    font-weight: 600;
    padding: 0.85rem;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.2s, transform 0.1s;
  }

  .btn-submit:hover {
    background-color: #1d4ed8;
  }

  .btn-submit:active {
    transform: scale(0.99);
  }

  /* --- EXAM WRAPPER STYLES --- */
  .exam-wrapper {
    display: flex;
    flex-direction: column;
    width: 100%;
    height: 100vh;
  }

  .top-bar {
    height: 56px;
    background-color: #0f172a;
    color: #ffffff;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 1.5rem;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    z-index: 10;
  }

  .exam-title {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    font-size: 0.95rem;
    font-weight: 600;
  }

  .pulse-indicator {
    width: 10px;
    height: 10px;
    background-color: #22c55e;
    border-radius: 50%;
    box-shadow: 0 0 8px #22c55e;
    animation: pulse 2s infinite;
  }

  @keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.4; }
    100% { opacity: 1; }
  }

  .timer-badge {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    background-color: #1e293b;
    padding: 0.4rem 1rem;
    border-radius: 999px;
    border: 1px solid #334155;
    transition: background-color 0.3s, border-color 0.3s;
  }

  .timer-badge.warning {
    background-color: #7f1d1d;
    border-color: #ef4444;
    color: #fecaca;
    animation: alertPulse 1s infinite alternate;
  }

  @keyframes alertPulse {
    from { box-shadow: 0 0 0px #ef4444; }
    to { box-shadow: 0 0 10px #ef4444; }
  }

  .timer-label {
    font-size: 0.8rem;
    text-transform: uppercase;
    color: #94a3b8;
  }

  .warning .timer-label {
    color: #fca5a5;
  }

  .timer-value {
    font-family: monospace;
    font-size: 1.15rem;
    font-weight: 700;
    letter-spacing: 0.05em;
  }

  .iframe-container {
    flex: 1;
    width: 100%;
    background-color: #ffffff;
    position: relative;
  }

  .iframe-container iframe {
    width: 100%;
    height: 100%;
    border: none;
    display: block;
  }

  /* --- EXPIRED VIEW STYLES --- */
  .expired-container {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 1.5rem;
    background-color: #0f172a;
  }

  .expired-card {
    background: #ffffff;
    width: 100%;
    max-width: 450px;
    padding: 3rem 2rem;
    border-radius: 16px;
    text-align: center;
    box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
  }

  .expired-icon {
    font-size: 3.5rem;
    margin-bottom: 1rem;
  }

  .expired-card h2 {
    font-size: 1.75rem;
    color: #dc2626;
    margin: 0 0 0.75rem 0;
  }

  .expired-card p {
    font-size: 0.95rem;
    color: #475569;
    line-height: 1.6;
    margin: 0;
  }
</style>
