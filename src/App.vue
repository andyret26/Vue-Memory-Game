<template>
  <div id="app">
    
    <div id="scoreBar">
      <scoreBar
      :playerList="playerList" 
      />
      <p id="time">TIME: {{time}}</p>
    </div>

    
      
    
    <select name="" id="boardSelect" v-model="boardSize" @change="lockBoardSize()" :class="{hiddenItem : hidden}">
      <option value="0" disabled>Select board size</option>
      <option value="4">4x4</option>
      <option value="6">6x6</option>
      <option value="8">8x8</option>
    </select>

    <button type="button" @click="arrayPush" v-bind:class="{'hiddenItem' : hidden}">Start Game</button>
    
    
    <p v-if="hidden == true">ROUND: {{round}}</p>
    <p>{{msg}}</p>

      <div class="board" 
        :class="{
          b4x4 : boardSize == 4,
          b6x6 : boardSize == 6,
          b8x8 : boardSize == 8}">

        

        <card  
          v-for="(card,index) in cardList"
          :key="`card-${index}`"
          :value="card.value"
          :cardHidden="card.cardHidden"
          :matchId="card.matchId"
          :position="card.position"
          @cardFliped="cardFliped"
        />

      
        <div id="winBox" v-if="cardPairs == 0">
          {{winner}}
           WINS THE GAME
           <button @click="restart">RESTART</button>
        </div>

        <div id="pairs-found" v-if="hidden">
          PAIRS FOUND: {{pairsFound}}
        </div>
      

      </div>

  </div>
</template>

<script>
import card from "./components/Card";
import scoreBar from "./components/ScoreBar"

export default {
  name: 'App',
  components: {
    card,
    scoreBar,
  },

  data: () => {
    return {
      msg:" ",
      msg2: "test",
      boardSize: 0,
      lockedBoardSize: 0,
      cardList: [],
      hidden: false,
      userSelected: [],
      cardOnePosition: Number,
      cardTwoPosition: Number,
      round: 1,
      cardPairs: Number,
      pairsFound: 0,
      winner: String,

      playerList: [
        {
          id: 1,
          nickname: "PLAYER 1",
          score: 0
        },
        {
          id: 2,
          nickname: "PLAYER 2",
          score: 0
        }
      ],

      flags: [
        'ALB',
        'AU',
        'BA',
        'BE',
        'BG',
        'BY',
        'CZ',
        'DK',
        'EE',
        'FIN',
        'FR',
        'GE',
        'GER',
        'GRE',
        'HR',
        'HU',
        'IC',
        'IE',
        'IT',
        'KO',
        'LI',
        'LT',
        'LV',
        'MC',
        'MD',
        'ME',
        'MT',
        'NO',
        'PL',
        'PT',
        'RS',
        'SM',
        'SPA',
        'SWE',
        'UK',
        'SW'
      ],

      time: "00:00:00",
      timeBegan: null,
      timeStoped: null,
      stoppDuration: 0,
      started: null,
      running: false,


    }
  },

 

  methods: {

    arrayPush: function() {

      if(this.lockedBoardSize != 0){

      
        this.startTimer()
        this.hideStartButton()

        for(let i=0; i<this.lockedBoardSize; i++){
          this.cardList.push({
            value: i,
          })
        }

        
        for (let i = 0; i<this.cardList.length/2; i++){
          this.cardList[i].matchId = this.flags[i]
        }
        
        for (let i = this.cardList.length/2; i<this.cardList.length; i++){
          this.cardList[i].matchId = this.flags[i - this.cardList.length/2]
        }

        this.shuffelArray(this.cardList)

        for(let i = 0; i < this.cardList.length; i++){
          this.cardList[i].position = i
        }

        this.cardPairs = this.lockedBoardSize/2

        this.msg = this.playerList[0].nickname + " STARTS"
      }
      else{
        alert("PLEASE SELECT BOARD SIZE BEFOR STARTING THE GAME")
      }
      
    },

    // shuffel array function 

    shuffelArray: function(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    },


    lockBoardSize: function(){
      this.lockedBoardSize = this.boardSize**2
    },


    hideStartButton: function(){
      this.hidden = true
    },


    // Gives Point to player
    givePoint: function(){
      if(this.round % 2 === 0){
        this.playerList[1].score +=1
      }
      else{
        this.playerList[0].score +=1
      }
    },

    // winner
    win: function(){
      if (this.cardPairs === 0){
              this.stopTimer()
        if(this.playerList[0].score > this.playerList[1].score){
          this.winner = this.playerList[0].nickname
          
        }
        else if(this.playerList[1].score > this.playerList[0].score){
          this.winner = this.playerList[1].nickname
          
        }
        else{
          this.winner = "NO ONE "
        }
      }
    },

    cardFliped: function(cardNum, cardPosition){
      
      this.msg = "  "
      
      this.cardList[cardPosition].cardHidden = false
      
      

      if(this.userSelected.length < 1){
        this.$set(this.userSelected, 0, cardPosition)
      }
      else if (this.userSelected.length == 1){
        this.$set(this.userSelected, 1, cardPosition);
      }

      

      if(this.userSelected.length == 2){
        this.cardOnePosition = this.userSelected[0]
        this.cardTwoPosition = this.userSelected[1]

        if(this.cardList[this.cardOnePosition].matchId === this.cardList[this.cardTwoPosition].matchId){
          this.cardList[this.cardOnePosition].cardHidden = false;
          this.cardList[this.cardTwoPosition].cardHidden = false;
          this.msg = "MATCHED"

          this.givePoint()
          this.cardPairs -= 1
          this.pairsFound += 1

          
          
          this.win()
          this.msg = "GO AGAIN"
          

        }
        else{
          this.msg = "WRONG"
          setTimeout(() => {
            this.cardList[this.cardOnePosition].cardHidden = true;
            this.cardList[this.cardTwoPosition].cardHidden = true;
            
            if(this.round % 2 === 0){
              this.msg = this.playerList[0].nickname + "'s turn"
            }
            else{
              this.msg = this.playerList[1].nickname + "'s turn"
            }
           
            this.round += 1
          }, 1500)
        }
        
        this.userSelected.length = 0
      }

    },

    restart: function(){

      for(let i = 0; i < this.lockedBoardSize; i++){
        this.cardList[i].cardHidden = true
      }
      this.playerList[0].score = 0
      this.playerList[1].score = 0
      this.cardPairs = this.lockedBoardSize/2
      this.round = 1
      this.msg = this.playerList[0].nickname + " STARTS"
      this.pairsFound = 0
      this.resetTimer()

      this.shuffelArray(this.cardList)

      for(let i = 0; i < this.cardList.length; i++){
        this.cardList[i].position = i
      }
    },

    startTimer: function(){
      if(this.running){
        return
      }

      if (this.timeBegan === null){
        this.timeBegan = new Date()
      }

      if(this.timeStoped !== null){
        this.stoppDuration += (new Date() - this.timeStoped)
      }

      this.started = setInterval(this.clockTicks, 10)
      this.running = true
    },

    clockTicks: function(){
      var currentTime = new Date()
      var timeElapsed = new Date(currentTime - this.timeBegan)
      var hour = timeElapsed.getUTCHours()
      var min = timeElapsed.getUTCMinutes()
      var sec = timeElapsed.getUTCSeconds()

      this.time = this.onlyINT(hour, 2) + ":" + this.onlyINT(min, 2) + ":" + this.onlyINT(sec, 2)

    },

    onlyINT: function(num, digit){
      var zero = ''
      for(var i = 0; i < digit; i++){
        zero += '0'
      }
      return (zero + num).slice(-digit)
    },

    stopTimer: function(){
      this.running = false
      this.timeStoped = new Date()
      clearInterval(this.started)

    },

    resetTimer: function(){
      this.running = false
      clearInterval(this.started)
      this.timeBegan = null
      this.time = "00:00:00"
      
      this.startTimer()

    }

    

  },

}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

*{
  font-family: 'Press Start 2P', cursive;
}
html{
  overflow-y: scroll;
}

html, body{
  margin: 0;
  padding: 0;
  background: url("../public/background.jpg") no-repeat center center fixed;
  background-size: cover;
}


#app{
  margin-left: auto;
  margin-right: auto;
  text-align: center;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  
  
}

p{
  height: 20px;
}
.board{
  align-self: center;
}
.hiddenItem{
  display: none;
}

.board{
  justify-content: center;
  text-align: ;
  position: relative;
  width: 500px;
}

.b8x8{
  display: grid;
  grid-template-columns: repeat(8, 100px);
  grid-template-rows: repeat(8, 100px);
  grid-row-gap: 25px;
  grid-column-gap: 25px;
}

.b6x6{
  display: grid;
  grid-template-columns: repeat(6, 100px);
  grid-template-rows: repeat(6, 100px);
  grid-row-gap: 25px;
  grid-column-gap: 25px;
}


.b4x4{
  display: grid;
  grid-template-columns: repeat(4, 100px);
  grid-template-rows: repeat(4, 100px);
  grid-row-gap: 25px;
  grid-column-gap: 25px;
}

select, button{
  background-color: #a2d9b1;
  height: 30px;
  padding: 5px;
  border-radius: 10px;
  margin: 10px;
  width: 300px;
}

#winBox{
  margin: auto;
  width: inherit;
  position: absolute;
  left: -25px;
  top: 100px;
  padding: 25px;
  color: #5e597a;
  background-color: #a2d9b1;
  border-radius: 5px;
  border: solid #6e9503 5px ;
}

select{
  text-align-last: center;
  padding-right: 29px;
}

#scoreBar{
  position: relative;
}

#time{
  position: absolute;
  right: -350px;
  top: 5px;
}

#pairs-found{
  width: inherit;
  margin-top: 5px;
}
  





</style>