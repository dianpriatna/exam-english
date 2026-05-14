<script>
  import ExamEngine from '$lib/ExamEngine.svelte';

  const passage = `The Amazon rainforest, often called the "lungs of the Earth," produces about 20% of the world's oxygen. Covering over 5.5 million square kilometers, it is home to an extraordinary variety of plant and animal species. However, deforestation threatens this vital ecosystem. Each year, large areas are cleared for farming and logging, contributing to climate change and the loss of biodiversity.`;

  const questions = [
    {
      id: 0,
      passage,
      question: "Why is the Amazon rainforest called the 'lungs of the Earth'?",
      options: [
        "Because it is very large.",
        "Because it produces about 20% of the world's oxygen.",
        "Because many animals live there.",
        "Because it rains a lot there.",
      ],
      answer: "Because it produces about 20% of the world's oxygen.",
    },
    {
      id: 1,
      passage,
      question: "How large is the Amazon rainforest?",
      options: [
        "Over 2 million square kilometers",
        "Over 10 million square kilometers",
        "Over 5.5 million square kilometers",
        "Over 1 million square kilometers",
      ],
      answer: "Over 5.5 million square kilometers",
    },
    {
      id: 2,
      passage,
      question: "What is one cause of deforestation mentioned in the passage?",
      options: [
        "Tourism and recreation",
        "Farming and logging",
        "Natural disasters",
        "Animal migration",
      ],
      answer: "Farming and logging",
    },
    {
      id: 3,
      passage,
      question: "What does deforestation contribute to, according to the passage?",
      options: [
        "More rainfall",
        "Climate change and loss of biodiversity",
        "Cleaner air",
        "More animal species",
      ],
      answer: "Climate change and loss of biodiversity",
    },
    {
      id: 4,
      passage,
      question: "What is the main topic of this passage?",
      options: [
        "The animals of the Amazon",
        "Importance and threats to the Amazon rainforest",
        "How to stop climate change",
        "Types of trees in the rainforest",
      ],
      answer: "Importance and threats to the Amazon rainforest",
    },
  ];
</script>

<ExamEngine {questions} title="Reading Exam" color="#9333ea" />
