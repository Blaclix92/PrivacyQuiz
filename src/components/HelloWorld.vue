
<template>
<div class="hello">
  <div v-if="!start">
    <h1>Welcome to Privacy Quiz</h1>
    <p>Explanation</p>
    <button type="button" class="btn btn-outline-primary"  v-on:click="startGame()">Start</button>     
  </div>
  <div class="row"  v-if="start">
    <div class="col-md-4"> </div>
    <div class="col-md-4">
      <h1>{{ quiz.title }}</h1>
      <div class="questionbox" v-if="question.text">{{ question.text }}</div>
        <ul>
          <li v-for='(response, index) in question.responses' :key='index'>
            <button type="button" class="answerbox" :disabled="showExplanation" v-on:click="validateQuestion(response.correct)">{{response.text}} </button>                       
          </li>
        </ul>
        <div v-if="showExplanation">
                <p>{{question.explanation}}</p>
                <button type="button" class="btn btn-outline-primary" v-on:click="continueGame()">Continue</button>     
        </div>
        <h1>Current winnings: &euro; {{userCorrectResponses}}</h1>
        <div v-if="this.gameFinish">
          <p v-if="userCorrectResponses>0">Congrats...</p>
          <p v-if="userCorrectResponses==0">Never give up...</p>
          <p>Explanation...</p>
        </div>
    </div>
    <div class="col-md-4 priceList" v-if="!gameFinish"> 
      <div><br /></div>
      <div v-for='(price, index) in filterPriceList' :key='index'>
        <button v-if="price.show" type="button" class="priceButton" :disabled="showExplanation" v-on:click="showQuestion(price.price)">&euro; {{price.price}} </button>    
      </div>
    </div>
  </div>
</div>
</template>



<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      quiz: {},
      question: {},
      questionsAmount: 0,
      questionIndex: 0,
      userCorrectResponses: 0,
      prices: [],
      filterPriceList: [],
      showExplanation: false,
      pleaseContinue: true,
      gameFinish: false,
      start: false
    };
  },
  methods: {
   startGame: function() {
     this.start = true;
    },
    continueGame: function() {
      this.pleaseContinue = true;
      this.showExplanation = false;
      this.removePrice(this.question.price);
    },
    quitGame: function() {
      this.pleaseContinue = false;
      this.showExplanation = false;
      this.question = {};
      this.gameFinish = true;
    },
    // Is use to show the question based on the selected price
    showQuestion: function(price) {
      var i = 0;
      for (i; i < this.quiz.questions.length; i++) {
        if (this.quiz.questions[i].price == price) {
          this.question = this.quiz.questions[i];
          break;
        }
      }
    },
    removePrice: function(questionPrice) {
      this.filterPriceList = this.prices;
      for (var i = 0; i < this.filterPriceList.length; i++) {
        if (this.filterPriceList[i].price == questionPrice) {
          this.filterPriceList[i].show = false;
        }
      }
      this.question = {};
      this.questionsAmount -= 1;
      this.checkIfFinished();
      this.prices = this.filterPriceList.filter(e => {
        return e.show.match(this.true);
      }); 
    },
    checkIfFinished: function(){
       if(this.questionsAmount == 0){
         this.quitGame();
       }
    },
    validateQuestion: function(correct) {
      if (correct) {
        this.userCorrectResponses += this.question.price;
        this.removePrice(this.question.price);
      } else {
        this.showExplanation = true;
        this.pleaseContinue = false;
      } 
    }
  },
  created: function() {
    // creates quiz list
    var quiz = {
      title: "Privacy Quiz",
      questions: [
        {
          text: "Question 1",
          price: 1000,
          explanation: " blbalbalbalba",
          responses: [
            { text: "This is a very long answer so we can see how big the answer box needs to be", correct: true },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
          ]
        },
        {
          text: "Very mucho questionerios sadfdsfkljsadfhdsk, fsadfasdjkhkjsalfklds, sdafasdfdsasdaaaaaaaaaaaaaaaaaaad shdajhfsda hjfdsagfjh hjfsda jhkf",
          price: 900,
          explanation: " blbalbalbalba",
          responses: [
            { text: "This is a very long answer so we can see how big the answer box needs to be", correct: true },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
          ]
        },
        {
          text: "Question 3",
          price: 800,
          explanation: " blbalbalbalba",
          responses: [
            { text: "This is a very long answer so we can see how big the answer box needs to be", correct: true },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
          ]
        },
        {
          text: "Question 4",
          price: 700,
          explanation: " blbalbalbalba",
          responses: [
            { text: "This is a very long answer so we can see how big the answer box needs to be", correct: true },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
            { text: "This is a very long answer so we can see how big the answer box needs to be!", correct: false },
          ]
        },
        {
          text: "Question 5",
          price: 600,
          explanation: " blbalbalbalba",
          responses: [
            { text: "Right answer", correct: true },
            { text: "Wrong answer", correct: false }
          ]
        },
        {
          text: "Question 6",
          price: 500,
          explanation: " blbalbalbalba",
          responses: [
            { text: "Right answer", correct: true },
            { text: "Wrong answer", correct: false }
          ]
        },
        {
          text: "Question 7",
          price: 400,
          explanation: " blbalbalbalba",
          responses: [
            { text: "Right answer", correct: true },
            { text: "Wrong answer", correct: false }
          ]
        },
        {
          text: "Question 8",
          price: 300,
          explanation: " blbalbalbalba",
          responses: [
            { text: "Right answer", correct: true },
            { text: "Wrong answer", correct: false }
          ]
        },
        {
          text: "Question 9",
          price: 200,
          explanation: " blbalbalbalba",
          responses: [
            { text: "Right answer", correct: true },
            { text: "Wrong answer", correct: false }
          ]
        },
        {
          text: "Question 10",
          price: 100,
          explanation: " blbalbalbalba",
          responses: [
            { text: "Right answer", correct: true },
            { text: "Wrong answer", correct: false }
          ]
        }
      ]
    };

    // create price list
    var quizPriceList = [];
    var i = 0;
    for (i; i < quiz.questions.length; i++) {
      var price = { price: quiz.questions[i].price, show: true };
      quizPriceList.push(price);
    }

    // binds quiz list and price list with VUEJS variables
    this.quiz = quiz;
    this.prices = quizPriceList;
    this.filterPriceList = this.prices;
    this.questionsAmount = this.prices.length;
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 0px;
}
a {
  color: #42b983;
}

.questionbox {
  background:linear-gradient(to right,black, blue, black);
  color: white;
  border-color: orange;
  border-width: 5px;
  border-style: ridge;
	width: 590px;
	margin-left: 15px;
	padding: 8px;
  font-size: 1em;
}

.answerbox {
  background:linear-gradient(to right,black, blue, black);
  color: white;
  border-color: orange;
  border-width: 4px;
	width: 290px;
	margin: 5px;
	padding: 8px;
  font-size: 1em;
}

.priceList {
  background: linear-gradient(black, blue, black);
  border-color: gray;
  border-width: 15px;
  height: 400px;
  border-style: ridge;
  max-width: 330px;
}

.priceButton {
  background:transparent;
  color: orange;
  border-color: transparent;
  width: 250px;
}

</style>
