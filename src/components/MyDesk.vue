<template>
  <div class="desk">
    <h4>Быки и коровы</h4>
    <div v-show="isFinished || !isStarted" class="header">
      <div v-show="isFinished" class="number">
        <span>{{number1}}</span>
        <span>{{number2}}</span>
        <span>{{number3}}</span>
        <span>{{number4}}</span>
      </div>

      <div v-show="!isStarted || isFinished" class="start-button">
        <button @click="startTheGame">Начать новую игру!</button>
      </div>
    </div>

    <div v-show="isStarted" class="body">
      <div v-show="moves.length>0" class="moves">
        <div v-for="move in moves" class="move">
          <span>{{move.n1}}</span>
          <span>{{move.n2}}</span>
          <span>{{move.n3}}</span>
          <span>{{move.n4}}</span>
          <span class="bulls">{{move.bulls}}</span>
          <span class="cows">{{move.cows}}</span>
        </div>
      </div>

      <div class="current">
        <span v-on:click="setCurrent(1)" v-bind:class="{active: current===1}">{{try1}}</span>
        <span v-on:click="setCurrent(2)" v-bind:class="{active: current===2}">{{try2}}</span>
        <span v-on:click="setCurrent(3)" v-bind:class="{active: current===3}">{{try3}}</span>
        <span v-on:click="setCurrent(4)" v-bind:class="{active: current===4}">{{try4}}</span>

        <button v-on:click="move" v-bind:class="{disabled: !allTaken}">&lt;</button>
      </div>
    </div>

    <div class="footer">
      <a v-bind:class="{disabled: isTaken(1)}" v-on:click="select(1)">1</a>
      <a v-bind:class="{disabled: isTaken(2)}" v-on:click="select(2)">2</a>
      <a v-bind:class="{disabled: isTaken(3)}" v-on:click="select(3)">3</a>
      <a v-bind:class="{disabled: isTaken(4)}" v-on:click="select(4)">4</a>
      <a v-bind:class="{disabled: isTaken(5)}" v-on:click="select(5)">5</a>
      <a v-bind:class="{disabled: isTaken(6)}" v-on:click="select(6)">6</a>
      <a v-bind:class="{disabled: isTaken(7)}" v-on:click="select(7)">7</a>
      <a v-bind:class="{disabled: isTaken(8)}" v-on:click="select(8)">8</a>
      <a v-bind:class="{disabled: isTaken(9)}" v-on:click="select(9)">9</a>
      <a v-bind:class="{disabled: isTaken(0)}" v-on:click="select(0)">0</a>
    </div>
  </div>
</template>

<script>
export default {
  name: 'my-desk',
  data: function () {
    return {
      isStarted: false,
      current: 1,
      number1: -1,
      number2: -1,
      number3: -1,
      number4: -1,
      try1: null,
      try2: null,
      try3: null,
      try4: null,
      isFinished: false,
      moves:[]
    }
  },
  computed: {
    numbers: function(){
      return [
        this.number1,
        this.number2,
        this.number3,
        this.number4,
      ];
    },
    takenDigits: function () {
      let t  = [];
      if (this.try1 !== null) t.push(this.try1);
      if (this.try2 !== null) t.push(this.try2);
      if (this.try3 !== null) t.push(this.try3);
      if (this.try4 !== null) t.push(this.try4);
      return  t;
    },

    allTaken: function () {
      return this.takenDigits.length === 4;
    }
  },
  methods: {
    isTaken: function (n) {
      return this.takenDigits.indexOf(n) > -1;
    },

    startTheGame: function () {
      let n1,n2,n3,n4,s;
      do {
        let n = Math.floor(Math.random()*(9877-123)+123);
        n4 = n % 10;
        n = (n-n4)/10;
        n3 = n % 10;
        n = (n-n3)/10;
        n2 = n % 10;
        n1 = (n-n2)/10;
        s = (n1-n2)*(n1-n3)*(n1-n4)*(n2-n3)*(n2-n4)*(n3-n4);
      } while (s===0);

      this.number1 = n1;
      this.number2 = n2;
      this.number3 = n3;
      this.number4 = n4;
      this.isStarted = true;
      this.isFinished = false;
      this.moves = [];
      this.current = 1;
    },

    select: function (n) {
      if (!this.isStarted || (n && this.isTaken(n))) {
        return false;
      }

      switch (this.current) {
        case 1: this.try1 = n; break;
        case 2: this.try2 = n; break;
        case 3: this.try3 = n; break;
        case 4: this.try4 = n; break;
      }

      if (!n) return;

      if (this.current==4) {
        this.current=1;
      } else {
        this.current++;
      }
    },

    setCurrent: function (n) {
      if (!this.isStarted) return;

      if (n===this.current) {
        this.select(null);
      } else {
        this.current = n;
      }
    },

    move: function () {
      if (!this.isStarted || this.isFinished) return false;
      if (!this.allTaken) return false;
      let m = {
        n1: this.try1,
        n2: this.try2,
        n3: this.try3,
        n4: this.try4,
        bulls: 0,
        cows: 0
      };
      console.log(m);
      m = this.calc(m);
      this.moves.push(m);

      if (m.bulls === 4) {
        this.isFinished = true;
      }

      this.try1 = null;
      this.try2 = null;
      this.try3 = null;
      this.try4 = null;
      this.current = 1;
    },

    calc: function (m) {
      let bulls = 0;
      let cows = 0;
      if (m.n1 === this.number1) bulls++;
      if (m.n2 === this.number2) bulls++;
      if (m.n3 === this.number3) bulls++;
      if (m.n4 === this.number4) bulls++;
      if (this.numbers.indexOf(m.n1) > -1) cows++;
      if (this.numbers.indexOf(m.n2) > -1) cows++;
      if (this.numbers.indexOf(m.n3) > -1) cows++;
      if (this.numbers.indexOf(m.n4) > -1) cows++;
      cows -= bulls;
      m.bulls = bulls;
      m.cows = cows;
      return m;
    }
  }
}
</script>

<style scoped>
.desk {
  margin: 50px auto;
  width: 320px;
  border: 1px solid #e3e3e3;
  padding: 15px 0;
  text-align: center;
}
.header {
  border-top: 1px solid #e3e3e3;
  padding: 15px 0;
}
.body {
  border-bottom: 1px solid #e3e3e3;
  padding: 15px 30px;
  border-top: 1px solid #e3e3e3;
}
.footer {
  padding: 15px 15px 0;
}
.footer a {
  display: inline-block;
  font-size: 24px;
  padding: 12px 20px;
  border: 1px solid #e3e3e3;
  background: #e8e8e8;
  border-radius: 4px;
  margin: 15px;
  cursor: pointer;
}
.footer a:hover {
  background: #6a6a6a;
  color: #ffffff;
}
.footer a.disabled {
  background: #eaeaea;
  color: #aaaaaa;
}
.number span {
  border: 1px solid #666666;
  display: inline-block;
  font-size: 24px;
  padding: 12px 17px;
  border-radius: 4px;
  margin: 0 5px;
}
.start-button button {
  width: 73%;
  font-size: 18px;
  background: #2c3e50;
  color: #e3e3e3;
  text-align: center;
  padding: 15px;
  border: 1px solid #e3e3e3;
  border-radius: 4px;
}
.number + .start-button {
  margin-top: 20px;
}
.current {
  display: flex;
  justify-content: space-around;
}
.current span {
  cursor: pointer;
  border: 1px solid #e3e3e3;
  font-size: 15px;
  display: inline-block;
  width: 20px;
  height: 20px;
  padding: 10px;
}
.current span.active {
  background: gold;
  border-color: #666666;
}
.current button {
  height: 43px;
  width: 44px;
  font-size: 18px;
  background: #2c3e50;
  color: #e3e3e3;
  text-align: center;
  border: 1px solid #e3e3e3;
  border-radius: 4px;
}
.current button.disabled {
  background: #eeeeee;
  color: #e3e3e3;
}
.moves {
  width: 100%;
  border: 1px solid #e3e3e3;
  margin-bottom: 30px;
}
.moves .move {
  border-bottom: 1px solid #e3e3e3;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr;
}
.moves .move span {
  padding: 10px;
  text-align: center;
  border-right: 1px solid #e3e3e3;
}
.moves .move span.bulls {
  background: #bb0d20;
  color: #ffffff;
}
.moves .move span.cows {
  background: #4ebb4d;
  color: #ffffff;
}
</style>
