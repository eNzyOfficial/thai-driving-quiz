<template>
  <div id="app" class="container">
    <div class="row">
      <div class="col-12">
        <div class="box">
          <div v-if="stage === 'score'">
            <h2>Score</h2>

            <p>Correct: {{ currentScore.correct }}</p>
            <p>Incorrect: {{ currentScore.incorrect }}</p>

            <a href="#" @click.prevent="reset()" class="btn btn-primary">Reset</a>
          </div>

          <div v-else-if="stage === 'sections'">
            <h2>Choose a section</h2>

            <ul class="list-unstyled">
              <li v-for="(section, index) in questions" :key="index">
                <a href="#" @click.prevent="chooseSection(index)">{{ section.title }}</a>
              </li>
            </ul>
          </div>

          <div v-else>
            <h2>{{ currentSectionTitle }} ({{ this.currentQuestionIndex + 1}}/{{ this.currentSectionQuestions.length }})</h2>

            <h6>{{ currentQuestion }}</h6>

            <img v-if="currentImage" :src="currentImage" class="img-fluid" />

            <ol type="A">
              <li v-for="(answer, index) of currentAnswers" :key="index">
                <a href="#" @click.prevent="submitAnswer(index)">{{ answer }}</a>
              </li>
            </ol>

            <div v-if="showAnswer">
              <p class="correct-answer">
                <strong>{{ currentCorrectAnswerText }}</strong>
              </p>

              <a
                href="#"
                class="btn btn-secondary"
                @click.prevent="nextQuestion()"
                style="margin-bottom:40px;"
              >Next Question</a>
            </div>

            <div class="row">
              <div class="col-6" style="text-align:left;align-self: flex-end;">
                Correct: {{ currentScore.correct }}
                <br />
                Incorrect: {{ currentScore.incorrect }}
              </div>

              <div class="col-6" style="text-align:right;align-self: flex-end;">
                <a href="#" @click.prevent="reset()" class>Back to sections</a>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import questions from "./questions.json";

export default {
  name: "app",

  data: () => ({
    questions,

    stage: "sections",

    showAnswer: false,

    currentSectionIndex: 0,
    currentQuestionIndex: 0,

    currentScore: {
      correct: 0,
      incorrect: 0
    }
  }),

  computed: {
    currentSection() {
      return this.questions[this.currentSectionIndex];
    },

    currentSectionQuestions() {
      return this.currentSection.questionList;
    },

    currentSectionTitle() {
      return this.currentSection.title;
    },

    questionObject() {
      return this.currentSectionQuestions[this.currentQuestionIndex];
    },

    currentQuestion() {
      return this.questionObject.question;
    },

    currentImage() {
      if (!this.questionObject.image) {
        return false;
      }

      return `images/${this.questionObject.image}`;
    },

    currentAnswers() {
      return this.questionObject.answers;
    },

    currentCorrectAnswer() {
      return this.questionObject.correct;
    },

    currentCorrectAnswerText() {
      return this.currentAnswers[this.currentCorrectAnswer];
    }
  },

  methods: {
    reset() {
      this.showAnswer = false;

      this.currentScore = {
        correct: 0,
        incorrect: 0
      };

      this.stage = "sections";
      this.currentSectionIndex = 0;
      this.currentQuestionIndex = 0;
    },

    chooseSection(sectionIndex) {
      this.currentSectionIndex = sectionIndex;
      this.currentQuestionIndex = 0;

      this.stage = "questions";
    },

    submitAnswer(index) {
      if (this.showAnswer) {
        return;
      }

      if (this.currentCorrectAnswer === index) {
        this.currentScore.correct++;

        this.nextQuestion();
      } else {
        this.currentScore.incorrect++;
        this.showAnswer = true;
      }
    },

    nextQuestion() {
      this.showAnswer = false;

      if (
        this.currentQuestionIndex !==
        this.currentSectionQuestions.length - 1
      ) {
        this.currentQuestionIndex++;
      } else {
        this.stage = "score";
      }
    }
  }
};
</script>

<style lang="scss">
html,
body {
  background: #f0f0f0;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}

a,
a:visited,
a:active {
  color: inherit;
}

h2 {
  border-bottom: 1px solid black;
  padding-bottom: 5px;
}

h6 {
  margin: 20px 0;
}

.box {
  background: white;
  border-radius: 10px;
  padding: 20px;
  margin: auto;
  max-width: 800px;
}

img {
  margin-bottom: 20px;
}

ol {
  text-align: initial;
}

li a {
  font-size: 16px;
  background: #f8f8f8;
  padding: 10px;
  display: block;
  border-radius: 5px;
  margin-bottom: 5px;
}

li a:hover {
  text-decoration: none;
  color: black;
}

@media (hover: hover) and (pointer: fine) {
  li a:hover {
    text-decoration: none;
    background: #a0a0a0;
    color: white;
  }
}

.correct-answer {
  background: #f87070;
  color: white;
  padding: 10px;
  font-size: 14px;
  margin-bottom: 10px;
}
</style>
