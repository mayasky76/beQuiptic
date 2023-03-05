<template>
  <div class="screen">
    <h3>{{ jsondata[question].clue }}</h3>

    <div v-for="(letter, i) in jsondata[question].word" :key="i" class="box">
      <span v-if="response[i]">{{ response[i] }}</span>
      <span v-else>&nbsp;</span>
    </div>

    <button class="checkbtn" @click="checkAnswer" ref="checkbtn">check</button>

    <div v-if="explain" class="explain">
      <Transition name="slide-fade">
        <div>
          {{ jsondata[question].explanation }}
        </div>
      </Transition>
    </div>

    <div class="hints" v-if="!explain">
      <template v-for="(hint, i) in jsondata[question].hints" :key="i">
        <Transition name="slide-fade">
          <div class="hint" v-if="hintcount > i">{{ hint }}</div>
        </Transition>
      </template>
    </div>
    <button class="hintbtn" @click="hintcount++" v-if="hintcount < 5">
      hint
    </button>
    <button
      class="explainbtn"
      @click="explain = true"
      v-if="hintcount === 5 && !explain"
    >
      Explain
    </button>

    <button
      class="nextbtn"
      @click="nextQuestion"
      v-if="hintcount === 5 && explain"
    >
      Next
    </button>
  </div>
  <div class="toolbar">Quiptic Clue Teacher</div>
  <div class="keyboard">
    <div class="keys" v-for="(row, i) in Object.keys(keyboard)" :key="i">
      <button
        class="key"
        v-for="(k, index) in keyboard[row]"
        :key="index"
        @click="keyClick(k)"
      >
        <div v-if="k === '<'">
          <svg style="width: 20px; height: 10px" viewBox="0 0 24 24">
            <path
              d="M22,3H7C6.31,3 5.77,3.35 5.41,3.88L0,12L5.41,20.11C5.77,20.64 6.31,21 7,21H22A2,2 0 0,0 24,19V5A2,2 0 0,0 22,3M19,15.59L17.59,17L14,13.41L10.41,17L9,15.59L12.59,12L9,8.41L10.41,7L14,10.59L17.59,7L19,8.41L15.41,12"
            />
          </svg>
        </div>
        <div v-else-if="k === '>'">GO</div>
        <div v-else>{{ k }}</div>
      </button>
    </div>
  </div>
</template>

<script>
import jsondata from "./data.json";
import "./style.css";
export default {
  name: "App",
  data() {
    return {
      jsondata,
      question: 0,
      response: [],
      hintcount: 0,
      explain: false,
      /* > is enter < is delete */
      keyboard: {
        row1: "qwertyuiop",
        row2: "asdfghjkl",
        row3: ">zxcvbnm<",
      },
    };
  },
  methods: {
    nextQuestion() {
      this.response = [];
      this.hintcount = 0;
      this.explain = false;
      this.question++;
    },
    checkAnswer() {
      console.log(this.response.join(""));
      if (
        this.response.join("").toLowerCase() ===
        this.jsondata[this.question].word.toLowerCase()
      ) {
        this.$swal(`Well Done!`);
        this.explain = true;
      } else {
        this.$swal(`Sorry - that's not correct!`);
      }
    },
    keyClick(k) {
      if (k === "<") {
        this.response.pop();
        return false;
      }
      if (k === ">") {
        alert("check");
        return false;
      }
      if (this.response.length < this.jsondata[this.question].word.length)
        this.response.push(k);
    },
  },
};
</script>

<style>
</style>
