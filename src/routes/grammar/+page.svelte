<script>
  import ExamEngine from '$lib/ExamEngine.svelte';

  const questions = [
    {
      id: 0,
      question: "Choose the correct form: She ___ to the market yesterday.",
      options: ["go", "goes", "went", "going"],
      answer: "went",
    },
    {
      id: 1,
      question: "Which sentence is correct?",
      options: [
        "He don't know the answer.",
        "He doesn't knows the answer.",
        "He doesn't know the answer.",
        "He not know the answer.",
      ],
      answer: "He doesn't know the answer.",
    },
    {
      id: 2,
      question: "Fill in the blank: They ___ playing football when it started to rain.",
      options: ["was", "were", "are", "is"],
      answer: "were",
    },
    {
      id: 3,
      question: "Choose the correct passive form: 'The teacher corrected the exam.'",
      options: [
        "The exam corrected by the teacher.",
        "The exam was corrected by the teacher.",
        "The exam is corrected by the teacher.",
        "The exam were corrected by the teacher.",
      ],
      answer: "The exam was corrected by the teacher.",
    },
    {
      id: 4,
      question: "Which word correctly completes: 'If I ___ rich, I would travel the world.'",
      options: ["am", "was", "were", "be"],
      answer: "were",
    },
  ];
</script>

<ExamEngine {questions} title="Grammar Exam" color="#2563eb" />
