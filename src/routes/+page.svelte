<script>
  const exams = [
    {
      title: 'Grammar',
      desc: 'Test your knowledge of English grammar rules, tenses, and sentence structure.',
      icon: 'G',
      href: '/grammar',
      color: '#2563eb',
      bg: '#eff6ff'
    },
    {
      title: 'Vocabulary',
      desc: 'Expand your word power with synonyms, antonyms, and word meanings.',
      icon: 'V',
      href: '/vocabulary',
      color: '#16a34a',
      bg: '#f0fdf4'
    },
    {
      title: 'Reading',
      desc: 'Practice reading comprehension with short passages and questions.',
      icon: 'R',
      href: '/reading',
      color: '#9333ea',
      bg: '#faf5ff'
    }
  ];
</script>

<main>
  <section class="hero">
    <span class="badge">English Exam Center</span>
    <h1>Master Your <em>English Skills</em></h1>
    <p>Choose an exam category below to begin. Each exam has 5 questions and instant feedback.</p>
  </section>

  <section class="cards" aria-label="Exam categories">
    {#each exams as exam, i}
      <a
        href={exam.href}
        class="card"
        style="--color: {exam.color}; --bg: {exam.bg}; animation-delay: {i * 0.08}s"
      >
        <div class="card-icon" aria-hidden="true">{exam.icon}</div>
        <div class="card-body">
          <h2>{exam.title}</h2>
          <p>{exam.desc}</p>
        </div>
        <div class="card-footer">
          <span>5 Questions</span>
          <span class="arrow">Start &rarr;</span>
        </div>
      </a>
    {/each}
  </section>
</main>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,700;1,700&family=Outfit:wght@400;500;600;700&display=swap');

  :global(*, *::before, *::after) {
    box-sizing: border-box;
  }

  :global(body) {
    margin: 0;
    min-height: 100vh;
    background: #f7f7f3;
    color: #171717;
    font-family: 'Outfit', sans-serif;
  }

  main {
    width: min(100%, 760px);
    margin: 0 auto;
    padding: 4rem 1rem 3rem;
  }

  .hero {
    margin-bottom: 2.5rem;
    text-align: center;
  }

  .badge {
    display: inline-block;
    margin-bottom: 1.1rem;
    border-radius: 999px;
    background: #171717;
    color: #fff;
    font-size: 0.72rem;
    font-weight: 700;
    letter-spacing: 0.1em;
    padding: 0.35rem 0.9rem;
    text-transform: uppercase;
  }

  h1,
  h2,
  p {
    margin: 0;
  }

  h1 {
    margin-bottom: 1rem;
    color: #171717;
    font-family: 'Playfair Display', serif;
    font-size: clamp(2.2rem, 8vw, 4rem);
    line-height: 1;
  }

  h1 em {
    color: #2563eb;
    font-style: italic;
  }

  .hero p {
    max-width: 31rem;
    margin: 0 auto;
    color: #626262;
    font-size: 1rem;
    line-height: 1.7;
  }

  .cards {
    display: grid;
    gap: 1rem;
  }

  .card {
    display: grid;
    grid-template-columns: auto minmax(0, 1fr) auto;
    align-items: center;
    gap: 1rem;
    border: 2px solid transparent;
    border-radius: 8px;
    background: var(--bg);
    color: inherit;
    padding: 1.25rem;
    text-decoration: none;
    animation: fade-up 0.36s ease both;
    transition:
      border-color 0.2s ease,
      box-shadow 0.2s ease,
      transform 0.2s ease;
  }

  .card:hover,
  .card:focus-visible {
    border-color: var(--color);
    box-shadow: 0 12px 28px rgba(23, 23, 23, 0.08);
    transform: translateY(-2px);
  }

  @keyframes fade-up {
    from {
      opacity: 0;
      transform: translateY(0.8rem);
    }

    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  .card-icon {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 3.4rem;
    height: 3.4rem;
    border-radius: 8px;
    background: #fff;
    box-shadow: 0 2px 10px rgba(23, 23, 23, 0.06);
    color: var(--color);
    flex-shrink: 0;
    font-size: 1.35rem;
    font-weight: 800;
  }

  .card-body h2 {
    margin-bottom: 0.3rem;
    color: var(--color);
    font-family: 'Playfair Display', serif;
    font-size: 1.28rem;
  }

  .card-body p {
    color: #626262;
    font-size: 0.9rem;
    line-height: 1.5;
  }

  .card-footer {
    display: grid;
    justify-items: end;
    gap: 0.25rem;
    color: #737373;
    font-size: 0.8rem;
    white-space: nowrap;
  }

  .arrow {
    color: var(--color);
    font-size: 0.9rem;
    font-weight: 700;
  }

  @media (max-width: 640px) {
    main {
      padding-top: 2.5rem;
    }

    .card {
      grid-template-columns: auto minmax(0, 1fr);
    }

    .card-footer {
      grid-column: 2;
      justify-items: start;
    }
  }
</style>
