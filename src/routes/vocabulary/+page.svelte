<script>
  import ExamEngine from '$lib/ExamEngine.svelte';

  const questions = [
    {
      id: 0,
      question: "What is the synonym of 'abundant'?",
      options: ["Scarce", "Plentiful", "Empty", "Tiny"],
      answer: "Plentiful",
    },
    {
      id: 1,
      question: "What is the antonym of 'ancient'?",
      options: ["Old", "Historic", "Modern", "Classic"],
      answer: "Modern",
    },
    {
      id: 2,
      question: "Choose the correct meaning of 'eloquent'.",
      options: [
        "Speaking in a rude manner",
        "Expressing ideas clearly and effectively",
        "Being very quiet",
        "Having a bad temper",
      ],
      answer: "Expressing ideas clearly and effectively",
    },
    {
      id: 3,
      question: "Which word means 'to make something better'?",
      options: ["Worsen", "Improve", "Ignore", "Destroy"],
      answer: "Improve",
    },
    {
      id: 4,
      question: "What does the word 'benevolent' mean?",
      options: ["Cruel", "Indifferent", "Kind and generous", "Angry"],
      answer: "Kind and generous",
    },
  ];
</script>

<ExamEngine {questions} title="Vocabulary Exam" color="#16a34a" />
