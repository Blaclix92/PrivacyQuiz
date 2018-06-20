
<template>
<div class="hello">
  <div class="row">
  <div class="col-md-4"></div>
  <div v-if="!start" class="col-md-4">
    <h1>Welkom bij de Privacy Quiz!</h1>
    <p>Weet jij eigenlijk wel waar al jou gegevens belanden? Wat gebeurd er met al die posts die je op Facebook zet? Dit zijn allemaal vragen waar we als mensen in de huidige wereld
      over moeten nadenken. Met deze quiz testen we hoeveel jij weet van privacy! Wees niet bang zelfs als je het niet weet helpen wij je om bewuster te worden!
     </p>
    <button type="button" class="btn btn-outline-primary"  v-on:click="startGame()">Start</button>     
  </div>
  <div class="col-md-4"></div>
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
                <div v-html="question.explanation"></div>
                <button type="button" class="btn btn-outline-primary" v-on:click="continueGame()">Continue</button>     
        </div>
        <h1>Totaal aantal punten: {{userCorrectResponses}}</h1>
        <div v-if="this.gameFinish">
          <p v-if="userCorrectResponses==10">Zeer indrukwekkend je kent de privacywetten op je duimpje en weet precies wat je wel en niet mag delen </p>
          <p v-else-if="userCorrectResponses>6 ">Jij weet goed hoe je jou privacy kan waarborgen en hoe de wetgeving werkt.</p>
          <p v-else-if="userCorrectResponses>3 ">Je bent goed op weg om een privacy guru te worden</p>
          <p v-else-if="userCorrectResponses>0">Je begint de implicaties van de wetgeving omtrent privacy te snappen</p>
          <p v-else-if="userCorrectResponses==0">Je hebt nog veel te leren, kijk op de website van de Autoriteit Persoonsgegevens voor meer informatie!</p>
          <p>Bedankt voor het deelnemen aan onze Quiz. We hopen dat je meer te weten bent gekomen over de impact van privacy en wetgeving op jou en je omgeving!</p>
          <p>Developers: Benny Mohan en Rick Zegelaar</p>
          <p>Vragen: Wouter Lindeboom en Samuel Putter</p>   
        </div>
    </div>
    <div class="col-md-4 priceList" v-if="!gameFinish"> 
      <div><br /></div>
      <div v-for='(price, index) in filterPriceList' :key='index'>
        <button v-if="price.show" type="button" class="priceButton" :disabled="showExplanation" v-on:click="showQuestion(price.price)">{{price.price}} </button>    
      </div>
      <div><br /></div>
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
          text: "Wat houdt het Recht op dataportabiliteit in?",
          price: 1000,
          explanation: "Onder de AVG hebben mensen het recht op dataportabiliteit, oftewel overdraagbaarheid van persoonsgegevens. In de AVG (artikel 20) heet dit het 'recht om gegevens over te dragen'. <br><br>Het houdt in dat mensen het recht hebben om de persoonsgegevens te ontvangen die een organisatie van hen heeft. Zo kunnen zij hun gegevens bijvoorbeeld makkelijk doorgeven aan een andere leverancier van dezelfde soort dienst. Ook kunnen mensen vragen om gegevens rechtstreeks over te dragen aan een andere organisatie. <br><br>Bron: <a href='https://autoriteitpersoonsgegevens.nl/nl/zelf-doen/privacyrechten/recht-op-dataportabiliteit'>Autoriteit Persoongegevens</a>",
          responses: [
            { text: "Een betrokkene moet door hem verstrekte persoonsgegevens van het ene elektronische verwerkingssysteem naar het andere kunnen overdragen, zonder door de verantwoordelijke voor de verwerking te worden verhinderd.", correct: true },
            { text: "Een betrokkene moet de data die door hem is verstrekt kunnen opvragen en dit dient door de instantie uitgeprint en opgestuurd te worden in een zwarte envelop.", correct: false },
            { text: "De betrokkene moet een csv bestand kunnen opvragen met alle gegevens die de persoon heeft verstrekt aan de instantie.", correct: false },
            { text: "Niemand weet precies wat deze wet inhoudt", correct: false },
          ]
        },
        {
          text: "Voor veel persoonsgegevens geldt in de AVG een bewaartermijn. Wat is de bewaartermijn voor een sollicitatiebrief?",
          price: 900,
          explanation: "Een sollicitatiebrief valt ook onder de AVG omdat deze persoonsgegevens bevatten! <br><br> Het is gebruikelijk dat na 4 weken zonder toestemming de brief vernietigd wordt. Met toestemming mag een organisatie dit een jaar opslaan.<br><br> Bron: <a href='https://autoriteitpersoonsgegevens.nl/nl/onderwerpen/werk-uitkering/sollicitaties'>Autoriteit Persoonsgegevens</a>",
          responses: [
            { text: "Vier weken zonder toestemming, een jaar met toestemming ", correct: true },
            { text: "Twee weken zonder toestemming, 6 maanden met toestemming", correct: false },
            { text: "Alle sollicitatiebrieven moeten vernietigd worden na afloop van het gesprek", correct: false },
            { text: "Vier weken zonder toestemming, oneindig met toestemming", correct: false }
          ]
        },
        {
          text: "Welke van de onderstaande opties is geen datalek?",
          price: 800,
          explanation: "In het geval van de wifi die zonder toestemming wordt gebruikt kan er niet van een datalek gesproken worden gezien er geen bestanden zijn benaderd.<br><br>Bron: <a href='https://autoriteitpersoonsgegevens.nl/nl/onderwerpen/beveiliging/meldplicht-datalekken'>Autoriteit Persoonsgegevens</a>",
          responses: [
            { text: "Een medewerker verliest een bedrijfslaptop met persoonsgegevens in de trein", correct: false },
            { text: "Er krijgt iemand toegang tot de wifi zonder toestemming, maar deze persoon heeft geen bestanden bekeken of gestolen", correct: true },
            { text: "Een oude PC wordt met harde schijf waar nog persoonsgegevens op staan bij het grofvuil gezet", correct: false },
            { text: "Een brand in een archiefkast vernietigd personeelsdossiers waarvan geen back ups beschikbaar zijn", correct: false },
          ]
        },
        {
          text: "Hoe veel tijd heb je om een datalek bij de autoriteit persoonsgegevens te melden?",
          price: 700,
          explanation: "In artikel 33 van de meldplicht datalekken staat beschreven dat bedrijven 72 uur hebben na het opmerken van het datalek om dit te melden.<br><br>Bron: <a href='https://autoriteitpersoonsgegevens.nl/nl/onderwerpen/beveiliging/meldplicht-datalekken'>Autoriteit Persoonsgegevens</a>",
          responses: [
            { text: "24 uur", correct: false },
            { text: "48 uur", correct: false },
            { text: "72 uur", correct: true },
            { text: "Een week", correct: false },
          ]
        },
        {
          text: "Wat is het verschil tussen AVG en GDPR?",
          price: 600,
          explanation: "Er is geen verschil tussen de twee, ze zijn namelijk allebei hetzelfde waarbij de GDPR de Engelse benaming is! <br><br>Bron: <a href='https://autoriteitpersoonsgegevens.nl/nl/onderwerpen/avg-europese-privacywetgeving/algemene-informatie-avg'>Autoriteit Persoonsgegevens</a>",
          responses: [
            { text: "De AVG geldt alleen voor Nederland, de GDPR voor de rest van Europa", correct: false },
            { text: "AVG vervangt de oude GDPR wet", correct: false },
            { text: "Er is geen verschil, AVG is alleen een Nederlandse vertaling van GDPR", correct: true },
          ]
        },
        {
          text: "Welke van de onderstaande persoonsgegevens wordt gezien als een bijzonder persoonsgegeven?",
          price: 500,
          explanation: "Hoewel de over gegevens ook als persoonsgegeven beschouwd worden zijn er een paar uitzondering die als bijzonder worden aangemerkt. Denk hierbij aan politieke voorkeur maar ook ras en gezondheid. <br><br>Bron: <a href='https://autoriteitpersoonsgegevens.nl/nl/over-privacy/persoonsgegevens/wat-zijn-persoonsgegevens'>Autoriteit Persoonsgegevens</a>",
          responses: [
            { text: "Voor- en achternaam", correct: false },
            { text: "Burgerservicenummer", correct: false },
            { text: "Politieke voorkeur", correct: true },
            { text: "Telefoonnummer", correct: false }
          ]
        },
        {
          text: "Je maakt een selfie van jezelf en je vrienden, mag je deze foto zomaar online delen?",
          price: 400,
          explanation: "Iedereen in de foto heeft een bepaald belang dat via het portretrecht beschermd wordt. Hiervoor moet dus toestemming worden gegeven.<br><br> Bron: <a href='http://www.iusmentis.com/auteursrecht/nl/foto/portretrecht/'>Arnoud Engelfriet</a>",
          responses: [
            { text: "Nee, er moet expliciet toestemming gegeven worden door iedereen in de foto", correct: true },
            { text: "Ja, iedereen poseerde voor de foto en dat geldt als toestemming geven", correct: false },
            { text: "Ja, je hebt zelf de foto genomen dus je mag hem delen waar je wilt", correct: false }
          ]
        },
        {
          text: "Mag je als hobbyfotograaf fotos waarin gezichten van mensen te zien op je persoonlijke PC bewaren?",
          price: 300,
          explanation: "Het portretrecht laat toe dat je voor eigen gebruik foto's met gezichten van andere mensen mag opslaan. <br><br> Bron: <a href='http://www.iusmentis.com/auteursrecht/nl/foto/portretrecht/'>Arnoud Engelfriet</a>",
          responses: [
            { text: "Nee, absoluut niet", correct: false},
            { text: "Ja, maakt niet uit voor welk doeleinde", correct: false},
            { text: "Ja, maar alleen voor persoonlijk gebruik", correct: true },
            { text: "Nee, alleen als je ze veilig opslaat", correct: false }
          ]
        },
        {
          text: "Wat is geen recht verwerkt in het AVG:",
          price: 200,
          explanation: "Recht op onderwijs heeft natuurlijk niks te maken met privacy! <br><br> Bron: <a href='https://autoriteitpersoonsgegevens.nl/nl/onderwerpen/avg-europese-privacywetgeving/algemene-informatie-avg'>Autoriteit Persoonsgegevens</a>",
          responses: [
            { text: "Recht op onderwijs", correct: true },
            { text: "Recht op dataportabiliteit", correct: false },
            { text: "Recht op correctie en verwijdering", correct: false},
            { text: "Recht op inzage", correct: false}
          ]
        },
        {
          text: "Wat betekent de afkorting AVG?",
          price: 100,
          explanation: "De afkorting AVG staat voor Algemene verordening gegevensbescherming<br><br> Bron: <a href='https://autoriteitpersoonsgegevens.nl/nl/onderwerpen/avg-europese-privacywetgeving/algemene-informatie-avg'>Autoriteit Persoonsgegevens</a>",
          responses: [
            { text: "Arbeid voor Geld", correct: false },
            { text: "Antisociale Vereniging Gehaktdag", correct: false },
            { text: "Algemene verordening gegevensbescherming", correct: true },
            { text: "Activiteiten van Gemeenten", correct: false }

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
	width: 450px;
	margin: 5px;
	padding: 8px;
  font-size: 1em;
}

.priceList {
  background: linear-gradient(black, blue, black);
  border-color: gray;
  border-width: 15px;
  height: 100%;
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
