<template>
  <div class="wrapper">
    <!-- welcome page -->
    <div v-show="!startQuiz" class="landingPage">
      <div class="landingPage-contents">
        <div class="landingPage-img">
          <img src="../assets/img/icon6.png" alt="icon" />
        </div>
        <div class="landingPage-text">
          <h1>QUIZ LARAVEL</h1>
          <p>
            Anda disajikan dengan pertanyaan dan empat pilihan, Anda memiliki waktu 30 detik untuk menjawab setiap pertanyaan setelah itu Anda akan secara otomatis dipindahkan ke pertanyaan berikutnya. Kegagalan memilih jawaban dalam batas waktu akan mengakibatkan hilangnya poin. Di akhir kuis, skor total Anda akan ditampilkan. Semoga beruntung!!
          </p>
          <button @click="handleStartQuiz()">Start Quiz</button>
        </div>
      </div>
    </div>

    <!-- start quiz page -->
    <div class="question_container" v-show="startQuiz">
      <div class="question-contents">
        <p class="question-no">
          Question {{ currentQuestion }} of {{ questions.length }}
        </p>
        <h3 :class="countDown < 10 ? 'warning' : 'count-down'">
          {{ countDown }}
        </h3>
        <p class="question">
          {{ questions[currentQuestion - 1].question }}
        </p>
        <div class="options-container">
          <button
            type="button"
            v-for="(item, index) in options"
            :key="index"
            @click="correctAnswer(item.isCorrect, item.answer)"
          >
            {{ item.answer }}
          </button>
        </div>
        <div class="btn">
          <button
            class="next-btn"
            v-show="currentQuestion < questions.length"
            @click="handleNextQuestion()"
          >
            Next
          </button>
          <button
            class="submit-btn"
            v-show="currentQuestion === questions.length"
            @click="displayResult()"
          >
            Submit
          </button>
        </div>
      </div>
    </div>

    <!-- display result -->
    <TotalPoints
      v-show="showResult"
      :totalPoints="points"
      :totalQuestions="questions.length"
    />
  </div>
</template>

<script>
import TotalPoints from "./TotalPoints.vue";
import QuestionData from "../assets/questionData.json";
import { shuffle } from 'lodash';

export default {
  name: "QuizApp",
  props: ["totalPoints", "totalQuestions", "countDownTimerFn"],
  data() {
    return {
      currentQuestion: 1,
      points: null,
      answersArray: [],
      arr: null,
      countDown: 30,
      timer: null,
      startQuiz: false,
      showResult: false,
      questions: QuestionData,
      options: shuffle(QuestionData[0].options)
    };
  },

  methods: {
    handleStartQuiz() {
      this.startQuiz = true;
      this.countDownTimer();
    },

    correctAnswer(isCorrect, answer) {
      if (isCorrect) {
        this.answersArray.push(answer);
        this.arr = new Set(this.answersArray);
      }
    },

    countDownTimer() {
      if (this.countDown > 0) {
        this.timer = setTimeout(() => {
          this.countDown--;
          this.countDownTimer();
        }, 1000);
      } else if (this.countDown === 0) {
        if (this.currentQuestion === this.questions.length) {
          this.displayResult();
        } else {
          this.handleNextQuestion();
        }
      }
    },

    handleNextQuestion() {
      clearTimeout(this.timer);
      this.options = this.questions[this.currentQuestion].options;
      this.options = shuffle(this.options);
      this.currentQuestion += 1;
      this.countDown = 30;
      this.countDownTimer();
    },

    displayResult() {
      this.showResult = true;
      this.countDown = 1;
      this.points = this.arr.size;
    },
  },

  components: {
    TotalPoints,
  },
};
</script>

<style scoped>
@import "../assets/quizQuestionStyle.css";
</style>
