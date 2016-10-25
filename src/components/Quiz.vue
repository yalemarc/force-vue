<!-- created by yale marc 10/25/16 -->
<template>
  <div class="mdl-cell mdl-cell--12-col mdl-card mdl-shadow--4dp" onload="fetchRandomPerson()" v-show="showHeading">
	<div class="mdl-card__title">
      <h2 class="mdl-card__title-text" @click="fetchRandomPerson" >Quiz</h2>
  	</div>
  	<div class="mdl-card__media" >
  	</div>

  	<div class="mdl-grid portfolio-copy" >
      <h3 class="mdl-cell mdl-cell--12-col mdl-typography--headline" >Test Your Knowledge</h3>
      <div class="mdl-cell mdl-cell--8-col mdl-card__supporting-text no-padding " >
          <p>
              Excepteur reprehenderit sint exercitation ipsum consequat qui sit id velit elit. Velit anim eiusmod labore sit amet.
          </p>
          <button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--colored" @click="fetchRandomPerson">
                  Start
          </button>
      </div>
  	</div>
  </div>
  	<div class="mdl-card__title">
      <h2 class="mdl-card__title-text" @click="fetchRandomPerson" v-show="!showHeading">Quiz</h2>
  	</div>
  	<div>
	  <div class="mdl-cell mdl-card mdl-shadow--4dp" style="float:left">
	    <figure class="mdl-card__media">
	      <div v-show="loading" class="mdl-spinner mdl-spinner--single-color mdl-js-spinner is-active"></div>
	      <img v-show="imageShow" :src.sync="personImage">
	      <span>{{personImageMsg}}</span>
	    </figure>
	  </div>
	  <div class="mdl-cell mdl-card mdl-shadow--4dp answer-container">
	  	<mdl-select class="getMdl-select__fix-height" label="Select Name of Character" id="character-select" :value.sync="characterName" :options="peopleArray" @change="personSelected"></mdl-select>
	  	<div class='correct-answer' v-show="showCorrect">YOU ARE CORRECT!!</div>
	  	<div class='wrong-answer' v-show="showWrong">SORRY THAT WAS NOT THE CORRECT ANSWER</div>
	  	<button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--colored submit-button" @click="submitAnswer" v-show="showSubmit" >
          Submit
        </button>
        <button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--colored submit-button" @click="nextQuestion" v-show="showNext" >
           Next
        </button>
	  </div>

  </div>
</template>

<script>
	import { MdlSelect, directives } from 'vue-mdl';
	import person from '../api/person';

export default{
  components: {
    MdlSelect,
  },
  directives,
  data() {
    return {
      characterName: 'Select',
      peopleNames: 'Ackbar,Adi Gallia,Anakin Skywalker,Arvel Crynyd,' +
          'Ayla Secura,BB8,Bail Prestor Organa,Barriss Offee,Ben Quadinaros,' +
          'Beru Whitesun lars,Bib Fortuna,Biggs Darklighter,' +
          'Boba Fett,Bossk,C-3PO,Captain Phasma,Chewbacca,Cliegg Lars,Cordé,' +
          'Darth Maul,Darth Vader,Dexter Jettster,Dooku,Dormé,Dud Bolt,' +
          'Eeth Koth,Finis Valorum,Finn,Gasgano,Greedo,Gregar Typho,Grievous,' +
          'Han Solo,IG-88,Jabba Desilijic Tiure,Jango Fett,Jar Jar Binks,' +
          'Jek Tono Porkins,Jocasta Nu,Ki-Adi-Mundi,Kit Fisto,Lama Su,' +
          'Lando Calrissian,Leia Organa,Lobot,Luke Skywalker,Luminara Unduli,' +
          'Mace Windu,Mas Amedda,Mon Mothma,Nien Nunb,Nute Gunray,' +
          'Obi-Wan Kenobi,Owen Lars,Padmé Amidala,Palpatine,Plo Koon,' +
          'Poe Dameron,Poggle the Lesser,Quarsh Panaka,' +
          'Qui-Gon Jinn,R2-D2,R4-P17,R5-D4,Ratts Tyerell,Raymus Antilles,Rey,' +
          'Ric Olié,Roos Tarpals,Rugor Nass,Saesee Tiin,San Hill,Sebulba,' +
          'Shaak Ti,Shmi Skywalker,Sly Moore,Tarfful,Taun We,Tion Medon,' +
          'Wat Tambor,Watto,Wedge Antilles,Wicket Systri Warrick,' +
          'Wilhuff Tarkin,Yarael Poof,Yoda,Zam Wesell',
      peopleArray: [],
      personData: {
        name: '',
        height: '',
        mass: '',
        hair_color: '',
        eye_color: '',
        birth_year: '',
        gender: '',
      },
      personImage: '',
      personImageMsg: '',
      imageShow: false,
      loading: false,
      showCorrect: false,
      showWrong: false,
      showSubmit: false,
      showNext: false,
      showHeading: true,
      selectedName: '',
	    };
  },
  methods: {
    fetchRandomPerson() {
      this.loading = true;
      this.imageShow = false;
      this.showHeading = false;
      this.personImageMsg = '';
      this.peopleArray = this.peopleNames.split(',');
      const randomPersonId = Math.floor((Math.random() * 87) + 1);
      person.fetch(randomPersonId).then((personData) => {
        this.personData = Object.assign(this.personData, personData);
        this.selectedName = this.personData.name;
        person.getPicture(this.personData.name).then((data) => {
          this.loading = false;
          if (data) {
            // this is sometimes returning no picture even though data returned
            this.personImage = data;
            this.imageShow = true;
          } else {
            this.fetchRandomPerson();
          }
        });
      });
    },
    submitAnswer() {
      if (this.characterName === this.selectedName) {
        this.showCorrect = true;
      } else {
        this.showWrong = true;
      }
      this.showSubmit = false;
      this.showNext = true;
    },
    nextQuestion() {
      this.showCorrect = false;
      this.showWrong = false;
      this.showNext = false;
      document.getElementById('character-select').value = '';
      this.fetchRandomPerson();
    },
    personSelected() {
      this.showSubmit = true;
    },
  },
	};

</script>
<style>
	.mdl-menu__container {
	  max-height: 200px;
	  background-color: yellow;
	  overflow-y: scroll !important;
	  box-shadow: 0 2px 2px 0 rgba(0,0,0,.14),0 3px 1px -2px rgba(0,0,0,.2),0 1px 5px 0 rgba(0,0,0,.12);
	  border: 3px solid rgba(0,0,0,.12);
	}
	.submit-button {
		position: relative;
		top: 200px;
		float: left;
		margin-right: 20px;
	}
	.answer-container{
		height:350px;
		padding-left: 20px;
		float: left;
	}
	.correct-answer{
		font-size: 1.3em;
		color:#429F4B;
		text-align: center;
	}
	.wrong-answer{
		font-size: 1.3em;
		color:#C4302C;
		text-align: center;
	}
</style>