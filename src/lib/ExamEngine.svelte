<script>
  /**
   * @typedef {object} Question
   * @property {number | string} id
   * @property {string} question
   * @property {string[]} options
   * @property {string} answer
   * @property {string} [passage]
   */

  /** @type {{ questions?: Question[], title?: string, color?: string }} */
  let { questions = [], title = 'Exam', color = '#2563eb' } = $props();

  let current = $state(0);
  /** @type {Record<string | number, string>} */
  let selected = $state({});
  let submitted = $state(false);

  let total = $derived(questions.length);
  let q = $derived(questions[current]);
  let answered = $derived(questions.filter((question) => selected[question.id] !== undefined).length);
  let score = $derived(
    submitted ? questions.filter((question) => selected[question.id] === question.answer).length : 0
  );
  let progress = $derived(total ? ((current + 1) / total) * 100 : 0);

  /**
   * @param {Question['id']} id
   * @param {string} opt
   */
  function pick(id, opt) {
    if (!submitted) {
      selected = { ...selected, [id]: opt };
    }
  }

  /** @param {number} index */
  function goTo(index) {
    current = Math.min(Math.max(index, 0), total - 1);
  }

  function submit() {
    if (answered !== total) return;
    submitted = true;
    current = 0;
  }

  function reset() {
    selected = {};
    submitted = false;
    current = 0;
  }

  function resultMsg() {
    const ratio = total ? score / total : 0;

    if (ratio === 1) return 'Perfect score';
    if (ratio >= 0.8) return 'Excellent work';
    if (ratio >= 0.6) return 'Good progress';
    return 'Keep practicing';
  }
</script>

<svelte:head>
  <title>{title}</title>
</svelte:head>

<div class="engine" style="--color: {color}">
  <header class="top">
    <a href="/" class="back" aria-label="Back to exam categories">&larr; Back</a>
    <div>
      <p class="eyebrow">English Exam</p>
      <h1>{title}</h1>
    </div>
    <span class="pill" class:done={submitted}>
      {#if submitted}
        {score}/{total}
      {:else}
        {answered}/{total}
      {/if}
    </span>
  </header>

  {#if !total}
    <section class="empty">
      <h2>No questions available</h2>
      <p>Please choose another exam category.</p>
    </section>
  {:else}
    <div class="progress-wrap" aria-label="Exam progress">
      <div class="progress-bar" style="width: {progress}%"></div>
    </div>

    <nav class="dots" aria-label="Question navigation">
      {#each questions as question, i}
        {@const isAnswered = selected[question.id] !== undefined}
        <button
          class="dot"
          class:active={i === current}
          class:answered={isAnswered}
          class:correct={submitted && selected[question.id] === question.answer}
          class:wrong={submitted && isAnswered && selected[question.id] !== question.answer}
          aria-label="Go to question {i + 1}"
          aria-current={i === current ? 'step' : undefined}
          onclick={() => goTo(i)}
        >
          {i + 1}
        </button>
      {/each}
    </nav>

    {#key current}
      <section class="card" aria-labelledby="question-title">
        <span class="q-label">Question {current + 1} of {total}</span>
        <h2 id="question-title" class="q-text">{q.question}</h2>

        {#if q.passage}
          <blockquote class="passage">{q.passage}</blockquote>
        {/if}

        <div class="options">
          {#each q.options as opt, i}
            {@const isSelected = selected[q.id] === opt}
            {@const isCorrect = submitted && opt === q.answer}
            {@const isWrong = submitted && isSelected && opt !== q.answer}
            <button
              class="option"
              class:selected={isSelected}
              class:correct={isCorrect}
              class:wrong={isWrong}
              aria-pressed={isSelected}
              onclick={() => pick(q.id, opt)}
              disabled={submitted}
            >
              <span class="opt-key">{String.fromCharCode(65 + i)}</span>
              <span class="opt-text">{opt}</span>
              {#if isCorrect}
                <span class="mark" aria-label="Correct">Correct</span>
              {:else if isWrong}
                <span class="mark" aria-label="Wrong">Wrong</span>
              {/if}
            </button>
          {/each}
        </div>

        {#if submitted}
          <div class="feedback" class:ok={selected[q.id] === q.answer}>
            {#if selected[q.id] === q.answer}
              Correct.
            {:else if selected[q.id] === undefined}
              Not answered. Answer: <strong>{q.answer}</strong>
            {:else}
              Wrong. Correct answer: <strong>{q.answer}</strong>
            {/if}
          </div>
        {/if}
      </section>
    {/key}

    <div class="nav">
      <button class="btn-ghost" onclick={() => goTo(current - 1)} disabled={current === 0}>
        &larr; Prev
      </button>

      {#if !submitted}
        {#if current === total - 1}
          <button class="btn-main" onclick={submit} disabled={answered < total}>
            Submit
          </button>
        {:else}
          <button class="btn-main" onclick={() => goTo(current + 1)}>
            Next &rarr;
          </button>
        {/if}
      {:else}
        <button class="btn-main secondary" onclick={reset}>Try Again</button>
      {/if}
    </div>

    {#if submitted}
      <div class="result" class:pass={score / total >= 0.6}>
        <strong>{resultMsg()}.</strong> You scored {score} out of {total}.
      </div>
    {:else}
      <p class="hint">
        {#if answered < total}
          Answer all {total} questions to submit.
        {:else}
          All answered. Ready to submit.
        {/if}
      </p>
    {/if}
  {/if}
</div>

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

  button,
  a {
    -webkit-tap-highlight-color: transparent;
  }

  .engine {
    width: min(100%, 720px);
    margin: 0 auto;
    padding: 2rem 1rem 3rem;
  }

  .top {
    display: grid;
    grid-template-columns: auto minmax(0, 1fr) auto;
    align-items: center;
    gap: 1rem;
    margin-bottom: 1.25rem;
  }

  .back {
    color: #737373;
    font-size: 0.9rem;
    font-weight: 600;
    text-decoration: none;
    white-space: nowrap;
  }

  .back:hover,
  .back:focus-visible {
    color: #171717;
  }

  .eyebrow {
    margin: 0 0 0.15rem;
    color: #737373;
    font-size: 0.72rem;
    font-weight: 700;
    letter-spacing: 0.08em;
    text-transform: uppercase;
  }

  h1,
  h2,
  p {
    margin: 0;
  }

  h1 {
    color: var(--color);
    font-family: 'Playfair Display', serif;
    font-size: clamp(1.45rem, 5vw, 2rem);
    line-height: 1.1;
  }

  .pill {
    min-width: 58px;
    border-radius: 999px;
    background: #e5e7eb;
    color: #525252;
    font-size: 0.84rem;
    font-weight: 700;
    padding: 0.38rem 0.75rem;
    text-align: center;
  }

  .pill.done {
    background: var(--color);
    color: #fff;
  }

  .progress-wrap {
    height: 0.45rem;
    margin-bottom: 0.75rem;
    overflow: hidden;
    border-radius: 999px;
    background: #e5e7eb;
  }

  .progress-bar {
    height: 100%;
    border-radius: inherit;
    background: var(--color);
    transition: width 0.28s ease;
  }

  .dots {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(2.1rem, 1fr));
    gap: 0.45rem;
    margin-bottom: 1.25rem;
  }

  .dot {
    min-height: 2.1rem;
    border: 2px solid #deded8;
    border-radius: 0.6rem;
    background: #fff;
    color: #737373;
    cursor: pointer;
    font: inherit;
    font-size: 0.8rem;
    font-weight: 700;
    transition:
      background 0.18s ease,
      border-color 0.18s ease,
      color 0.18s ease,
      transform 0.18s ease;
  }

  .dot:hover,
  .dot:focus-visible {
    transform: translateY(-1px);
    border-color: var(--color);
  }

  .dot.active {
    border-color: var(--color);
    color: var(--color);
  }

  .dot.answered {
    background: #eeeeea;
    border-color: #c9c9c2;
    color: #404040;
  }

  .dot.correct {
    background: #dcfce7;
    border-color: #16a34a;
    color: #15803d;
  }

  .dot.wrong {
    background: #fee2e2;
    border-color: #dc2626;
    color: #b91c1c;
  }

  .card,
  .empty {
    margin-bottom: 1rem;
    border: 1px solid #ecece6;
    border-radius: 8px;
    background: #fff;
    box-shadow: 0 12px 32px rgba(23, 23, 23, 0.06);
  }

  .card {
    padding: clamp(1.15rem, 4vw, 2rem);
    animation: enter 0.22s ease;
  }

  .empty {
    padding: 2rem;
    text-align: center;
  }

  .empty h2 {
    margin-bottom: 0.35rem;
    font-size: 1.1rem;
  }

  .empty p {
    color: #737373;
  }

  @keyframes enter {
    from {
      opacity: 0;
      transform: translateY(0.5rem);
    }

    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  .q-label {
    display: block;
    margin-bottom: 0.7rem;
    color: var(--color);
    font-size: 0.74rem;
    font-weight: 700;
    letter-spacing: 0.08em;
    text-transform: uppercase;
  }

  .q-text {
    margin-bottom: 1.1rem;
    color: #171717;
    font-family: 'Playfair Display', serif;
    font-size: clamp(1.08rem, 3vw, 1.3rem);
    line-height: 1.55;
  }

  .passage {
    margin: 0 0 1.1rem;
    border-left: 4px solid var(--color);
    border-radius: 0 8px 8px 0;
    background: #f7f7f3;
    color: #404040;
    font-size: 0.95rem;
    line-height: 1.7;
    padding: 0.9rem 1rem;
  }

  .options {
    display: grid;
    gap: 0.65rem;
  }

  .option {
    display: grid;
    grid-template-columns: auto minmax(0, 1fr) auto;
    align-items: center;
    gap: 0.75rem;
    width: 100%;
    min-height: 3.4rem;
    border: 2px solid #e5e7eb;
    border-radius: 8px;
    background: #fbfbf8;
    color: #262626;
    cursor: pointer;
    font: inherit;
    font-size: 0.96rem;
    line-height: 1.35;
    padding: 0.75rem 0.9rem;
    text-align: left;
    transition:
      background 0.18s ease,
      border-color 0.18s ease,
      color 0.18s ease,
      transform 0.18s ease;
  }

  .option:hover:not(:disabled):not(.selected),
  .option:focus-visible:not(:disabled):not(.selected) {
    border-color: #a3a3a3;
    background: #f4f4ef;
    transform: translateY(-1px);
  }

  .option.selected {
    border-color: var(--color);
    background: var(--color);
    color: #fff;
  }

  .option.correct {
    border-color: #16a34a;
    background: #dcfce7;
    color: #15803d;
  }

  .option.wrong {
    border-color: #dc2626;
    background: #fee2e2;
    color: #b91c1c;
  }

  .option:disabled {
    cursor: default;
  }

  .opt-key {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 1.7rem;
    height: 1.7rem;
    border: 1.5px solid currentColor;
    border-radius: 50%;
    flex-shrink: 0;
    font-size: 0.76rem;
    font-weight: 700;
  }

  .opt-text {
    min-width: 0;
  }

  .mark {
    justify-self: end;
    font-size: 0.76rem;
    font-weight: 700;
    text-transform: uppercase;
  }

  .feedback {
    margin-top: 1rem;
    border-radius: 8px;
    background: #fee2e2;
    color: #991b1b;
    font-size: 0.9rem;
    line-height: 1.5;
    padding: 0.8rem 0.9rem;
  }

  .feedback.ok {
    background: #dcfce7;
    color: #166534;
  }

  .nav {
    display: grid;
    grid-template-columns: auto minmax(0, 1fr);
    gap: 0.75rem;
    margin-bottom: 1rem;
  }

  .btn-ghost,
  .btn-main {
    min-height: 2.8rem;
    border-radius: 8px;
    cursor: pointer;
    font: inherit;
    font-weight: 700;
    transition:
      opacity 0.18s ease,
      border-color 0.18s ease,
      transform 0.18s ease;
  }

  .btn-ghost {
    border: 2px solid #e5e7eb;
    background: #fff;
    color: #525252;
    padding: 0 1rem;
  }

  .btn-main {
    border: 0;
    background: var(--color);
    color: #fff;
    padding: 0 1rem;
  }

  .btn-main.secondary {
    background: #525252;
  }

  .btn-ghost:hover:not(:disabled),
  .btn-main:hover:not(:disabled),
  .btn-ghost:focus-visible:not(:disabled),
  .btn-main:focus-visible:not(:disabled) {
    transform: translateY(-1px);
  }

  .btn-ghost:hover:not(:disabled),
  .btn-ghost:focus-visible:not(:disabled) {
    border-color: #a3a3a3;
  }

  .btn-ghost:disabled,
  .btn-main:disabled {
    cursor: not-allowed;
    opacity: 0.42;
  }

  .result {
    border-radius: 8px;
    background: #fee2e2;
    color: #991b1b;
    line-height: 1.5;
    padding: 1rem;
    text-align: center;
  }

  .result.pass {
    background: #dcfce7;
    color: #166534;
  }

  .hint {
    color: #737373;
    font-size: 0.88rem;
    line-height: 1.5;
    text-align: center;
  }

  @media (max-width: 560px) {
    .engine {
      padding-top: 1rem;
    }

    .top {
      grid-template-columns: minmax(0, 1fr) auto;
    }

    .back {
      grid-column: 1 / -1;
      width: max-content;
    }

    .option {
      align-items: start;
    }
  }
</style>
