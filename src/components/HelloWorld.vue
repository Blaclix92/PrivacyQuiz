
<template>
  <div class="hello">
    <h1>{{ quiz.title }}</h1>
    <!-- index is used to check with current question index -->
    <div v-if='question'>
      <!-- Hide all questions, show only the one with index === to current question index -->
        <h2>{{ question.text }}</h2>
        <ul>
          <li v-for='(response, index) in question.responses' :key='index'>
             <button type="button" class="btn btn-outline-primary" v-on:click="validateQuestion(response.correct)">{{response.text}} </button>          
          </li>
        </ul>
        <!-- Fix show explanation for user if the anwser is not correct -->
         <div v-if="showExplanation">
               <p>{{question.explanation}}</p>
                <button type="button" class="btn btn-outline-primary" v-on:click="continueGame()">Continue</button>  
                 or 
                <button type="button" class="btn btn-outline-primary"  v-on:click="quitGame()">Close the deal</button>     
         </div>
    </div>
    <!-- Fix styling prices to interact with questions -->
    <div v-if="pleaseContinue" v-for='(price, index) in filterPriceList' :key='index'>
      <button v-if="price.show" type="button" class="btn btn-outline-primary" v-on:click="showQuestion(price.price)">{{price.price}} </button>    
    </div>
    <h1>Bank Offer: &euro; {{userCorrectResponses}}</h1>
    <div v-if="this.gameFinish">
      <p v-if="userCorrectResponses>0">Congrats...</p>
      <p v-if="userCorrectResponses==0">Never give up...</p>
      <p>Explanation...</p>
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
      gameFinish: false
    };
  },
  methods: {
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
          price: 300,
          explanation: " blbalbalbalba",
          responses: [
            { text: "Wrong, too bad.", correct: true },
            { text: "Right!", correct: false }
          ]
        },
        {
          text: "Question 2",
          price: 200,
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
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
