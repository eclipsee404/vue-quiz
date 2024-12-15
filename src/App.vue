<template>
  <div>
    <ScoreBoard :winCount="winCount" :loseCount="loseCount" />
    <template v-if="this.answers">
      <h1 v-html="this.question"></h1>
      <div v-for="(answer, index) in this.answers" :key="index">
        <input
          type="radio"
          name="options"
          :value="answer"
          v-model="this.chosen_answer"
          :disabled="this.answerSubmitted"
        />
        <label v-html="answer"></label><br />
      </div>
      <button
        class="send"
        type="button"
        @click="this.submitAnswer()"
        v-if="!this.answerSubmitted"
      >
        Send
      </button>
      <section class="result" v-else>
        <h4 v-if="this.chosen_answer == this.correctAnswer">
          &#9989; Congratulations, the answer "{{ this.chosen_answer }}" is
          correct
        </h4>
        <h4 v-else>
          &#10060; I'm sorry, you picked the wrong answer, The correct is "{{
            this.correctAnswer
          }}"
        </h4>
        <button class="send" type="button" @click="getNewQuestion()">
          Want a next question?
        </button>
      </section>
    </template>
  </div>
</template>

<script>
import ScoreBoard from '@/components/ScoreBoard.vue';
export default {
  name: 'App',
  components: {
    ScoreBoard,
  },
  data() {
    return {
      question: undefined,
      incorrectAnswer: undefined,
      correctAnswer: undefined,
      chosen_answer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0,
    };
  },
  computed: {
    answers() {
      if (this.incorrectAnswer && this.correctAnswer) {
        var answers = JSON.parse(JSON.stringify(this.incorrectAnswer));
        answers.splice(
          Math.round(Math.random() * answers.length),
          0,
          this.correctAnswer
        );
        return answers;
      }
      return [];
    },
  },
  methods: {
    submitAnswer() {
      if (!this.chosen_answer) {
        alert('please choose the answer first');
      } else {
        this.answerSubmitted = true;
        if (this.chosen_answer == this.correctAnswer) {
          this.winCount++;
          console.log('your answer is correct');
        } else {
          this.loseCount++;
          console.log('your answer is wrong');
        }
      }
    },

    getNewQuestion() {
      this.answerSubmitted = false;
      this.chosen_answer = undefined;
      this.axios
        .get('https://opentdb.com/api.php?amount=1&category=18')
        .then((response) => {
          this.question = response.data.results[0].question;
          this.correctAnswer = response.data.results[0].correct_answer;
          this.incorrectAnswer = response.data.results[0].incorrect_answers;
          console.log(response.data.results[0]);
        });
    },
  },
  created() {
    this.axios
      .get('https://opentdb.com/api.php?amount=1&category=18')
      .then((response) => {
        this.question = response.data.results[0].question;
        this.correctAnswer = response.data.results[0].correct_answer;
        this.incorrectAnswer = response.data.results[0].incorrect_answers;
        console.log(response.data.results[0]);
      });
  },
};
// https://opentdb.com/api.php?amount=1&category=18
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;
  input[type='radio'] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1b67c0;
    border: 1px solid #1b67c0;
    cursor: pointer;
  }
}
</style>
