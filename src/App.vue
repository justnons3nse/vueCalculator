<template>
  <h2>Cestovné poistenie</h2>
  <div>
    <h3>Druh poistenia:</h3>
    <div class="sortLongTerm">
      <div>
        <button
          :class="{ active: shortToggle }"
          @click="shortTerm"
          class="selectButton"
        >
          <i class="fa-solid fa-suitcase-rolling"></i>
        </button>
        <p>Krátkodobé</p>
      </div>
      <div>
        <button
          :class="{ active: longToggle }"
          @click="longTerm"
          class="selectButton"
        >
          <i class="fas fa-caravan"></i>
        </button>
        <p>Celoročné</p>
      </div>
    </div>

    <div v-if="shortToggle || longToggle">
      <h3>Dĺžka poistenia:</h3>
      <div class="length">
        <div>
          <p>Začiatok</p>
          <input
            id="startDate"
            type="date"
            v-model="startDate"
            :min="todayDate"
            :max="endDate"
          />
        </div>

        <div v-if="startDate && shortToggle">
          <p>Koniec</p>
          <input
            class="endDate"
            v-if="shortToggle"
            type="date"
            name=""
            id=""
            v-model="endDate"
            v-on:input="calculateDays"
            :min="startDate"
          />
        </div>
      </div>
    </div>

    <div v-if="endDate || longToggle">
      <h3>Balík Poistenia:</h3>
      <div class="package">
        <div>
          <button
            :class="{ active: essentialToggle }"
            @click="essentialPackage"
            class="selectButton"
          >
            <i class="fa-solid fa-chess-pawn"></i>
          </button>
          <p>Základný</p>
        </div>
        <div>
          <button
            :class="{ active: mediumToggle }"
            @click="mediumPackage"
            class="selectButton"
          >
            <i class="fa-solid fa-chess-rook"></i>
          </button>
          <p>Rozšírený</p>
        </div>
        <div>
          <button
            :class="{ active: extraToggle }"
            @click="extraPackage"
            class="selectButton"
          >
            <i class="fa-solid fa-chess-queen"></i>
          </button>
          <p>Extra</p>
        </div>
      </div>
    </div>

    <div v-if="essentialToggle || mediumToggle || extraToggle">
      <h3>Pripoistenie:</h3>
      <div class="extraInsurance">
        <div>
          <button
            :class="{ active: cancelToggle }"
            @click="cancelToggle = !cancelToggle"
            class="selectButton"
          >
            <i class="fas fa-plane-slash"></i>
          </button>
          <p>Storno Cesty</p>
        </div>
        <div>
          <button
            :class="{ active: sportToggle }"
            @click="sportToggle = !sportToggle"
            class="selectButton"
          >
            <i class="fas fa-volleyball-ball"></i>
          </button>
          <p>Športové aktivity</p>
        </div>
      </div>
    </div>

    <div v-if="essentialToggle || mediumToggle || extraToggle" class="person">
      <select v-model="peopleSelected" v-on:select="calculatePrice">
        <option disabled value="">Vyberne prosím počet osôb</option>
        <option>1</option>
        <option>2</option>
        <option>3</option>
      </select>
    </div>
  </div>
  <button class="total" v-if="peopleSelected" @click="calculatePrice">Spočítať cenu</button>
  <div v-if="grandTotal > 0">
    <h2>Cena poistenia : {{ grandTotal }}$</h2>
  </div>
  <p>Check The Project at <a href="https://github.com/justnons3nse/vueCalculator" target="_blank">Github</a></p>
</template>

<script>
export default {
  data() {
    return {
      shortToggle: false,
      longToggle: false,
      isActive: false,
      startDate: "",
      endDate: "",
      days: "",
      todayDate: new Date().toISOString().slice(0, 10),
      essentialToggle: false,
      mediumToggle: false,
      extraToggle: false,
      essentialPackagePrice: 1,
      mediumPackagePrice: 1,
      extraPackagePrice: 1,
      packagePrice: 1,
      sportToggle: false,
      cancelToggle: false,
      peopleSelected: "",
      sports: 1,
      cancel: 1,
      grandTotal: 0,
      sportsPrice: 1,
      cancelPrice: 1,
    };
  },
  watch: {
    sportToggle() {
      if (!this.sportToggle) {
        this.sports = 1;
      }
    },
    cancelToggle() {
      if (!this.cancelToggle) {
        this.cancel = 1;
      }
    },
  },

  methods: {
    //Set Short Term to true and set given values to all choices
    shortTerm() {
      this.shortToggle = true;
      this.longToggle = false;
      this.essentialPackagePrice = 1.2;
      this.mediumPackagePrice = 1.8;
      this.extraPackagePrice = 2.4;
      this.sportsPrice = 1.3;
      this.cancelPrice = 1.5;
    },
    //Set Long Term to true and set given values to all choices
    longTerm() {
      this.longToggle = true;
      this.shortToggle = false;
      this.essentialPackagePrice = 39;
      this.mediumPackagePrice = 49;
      this.extraPackagePrice = 59;
      this.sportsPrice = 1.1;
      this.cancelPrice = 1.2;
    },
    // Calculate days between two dates
    calculateDays() {
      const diffDays = (date, otherDate) =>
        Math.ceil(Math.abs(date - otherDate) / (24 * 60 * 60 * 1000));
      if (this.startDate <= this.endDate) {
        this.days =
          diffDays(new Date(this.startDate), new Date(this.endDate)) + 1;
      }
    },
    //Calculate today date
    today() {
      var today = new Date();
      this.todayDate =
        today.getFullYear() +
        "-" +
        (today.getMonth() + 1) +
        "-" +
        today.getDate();
    },
    // Set Essential package to true
    essentialPackage() {
      this.essentialToggle = true;
      this.mediumToggle = false;
      this.extraToggle = false;
    },
    // Set Medium package to true
    mediumPackage() {
      this.essentialToggle = false;
      this.mediumToggle = true;
      this.extraToggle = false;
    },
    // Set Extra package to true
    extraPackage() {
      this.essentialToggle = false;
      this.mediumToggle = false;
      this.extraToggle = true;
    },
    calculateAll() {
      //Set package price based on what is selected
      if (this.essentialToggle) {
        this.packagePrice = this.essentialPackagePrice;
      } else if (this.mediumToggle) {
        this.packagePrice = this.mediumPackagePrice;
      } else if (this.extraToggle) {
        this.packagePrice = this.extraPackagePrice;
      }
      //Set sport or cancel price
      if (this.sportToggle) {
        this.sports = this.sportsPrice;
      }
      if (this.cancelToggle) {
        this.cancel = this.cancelPrice;
      }
      //Calculate price based on if Short or Long term is selected
      if (this.shortToggle) {
        let total =
          this.days *
          this.packagePrice *
          (this.sports + this.cancel - 1) *
          parseInt(this.peopleSelected);
        this.grandTotal = (Math.round(total * 100) / 100).toFixed(2);
      } else {
        let total =
          this.packagePrice *
          (this.sports + this.cancel - 1) *
          parseInt(this.peopleSelected);
        this.grandTotal = (Math.round(total * 100) / 100).toFixed(2);
      }
    },
    // Calculate Grand Total
    calculatePrice() {
      this.calculateAll();
    },
  },
};
</script>

<style>
@import "./style.css";
</style>