<template>
  <div class="content" >
    <div class="information">
      <div class="time">
        <div>시간</div>
        <div>{{ timer.value[0] + ':' + timer.value[1] + timer.value[2] }}</div>
      </div>
      <div class="score">
        <div>
          <div>점수</div>
          <div>{{ score }}</div>
        </div>
        <div>
          <div>완성</div>
          <div>{{ cookedBurger.count }}</div>
        </div>
      </div>
    </div>
    <div class="orderList">
      <div class="order first">
        <div class="burger">
          <div v-for="(ingredients, ind) in orderList[0]"
          :key="ind"
          :style="{backgroundColor: color[ingredients]}"/>
        </div>
      </div>
      <div class="order second">
        <div class="burger">
          <div v-for="(ingredients, ind) in orderList[1]"
          :key="ind"
          :style="{backgroundColor: color[ingredients]}"/>
        </div>
      </div>
      <div class="order third">
        <div class="burger">
          <div v-for="(ingredients, ind) in orderList[2]"
          :key="ind"
          :style="{backgroundColor: color[ingredients]}"/>
        </div>
      </div>
    </div>
    <div class="dish">
      <div class="burger" v-if="start">
        <div v-for="(ingredients, ind) in dish"
        :class="ind == 0 ? 'stack' : ''"
        :key="ind"
        :style="{backgroundColor: color[ingredients]}"/>
      </div>
    </div>
    <div class="keyWrap">
      <div class="key">
        <div :style="{backgroundColor: '#FFAF75', 
        boxShadow: '0px 4px 4px rgba(0, 0, 0, 0.25)'}" 
        @click="myDish({keyCode: 81})">Q</div>
        <div :style="{backgroundColor: '#FF2222', 
        boxShadow: '0px 4px 4px rgba(0, 0, 0, 0.25)'}" 
        @click="myDish({keyCode: 87})">W</div>
        <div :style="{backgroundColor: '#FFCB11', 
        boxShadow: '0px 4px 4px rgba(0, 0, 0, 0.25)'}" 
        @click="myDish({keyCode: 69})">E</div>
        <div :style="{backgroundColor: '#6C431D',
        boxShadow: '0px 4px 4px rgba(0, 0, 0, 0.25)'}" 
        @click="myDish({keyCode: 65})">A</div>
        <div :style="{backgroundColor: '#42CC36',
        boxShadow: '0px 4px 4px rgba(0, 0, 0, 0.25)'}" 
        @click="myDish({keyCode: 83})">S</div>
        <div :style="{backgroundColor: '#548812',
        boxShadow: '0px 4px 4px rgba(0, 0, 0, 0.25)'}" 
        @click="myDish({keyCode: 68})">D</div>
      </div>
    </div>
  </div>
</template>

<script>
import fail from '../assets/MP_Fail.mp3'
import finish from '../assets/MP_GameOver.mp3'
import stackSound from '../assets/MP_Stack.mp3'
import wellDone from '../assets/MP_WellDone.mp3'

export default {
  data() {
    return {
      // 시작과 종료
      start: false,
      // 주문,조리,색정보
      orderList: [],
      newOrderFunc: null,
      dish: [],
      color: ['#FFAF75', '#FF2222', '#FFCB11', '#6C431D', '#42CC36', '#548812'],
      // 점수와 시간
      score: 0,
      cookedBurger: {
        count: 0,
        list: []
      },
      timer: {
        timerFunc: null,
        max: 10000,
        now: 10000,
        value: [0, 0, 0, 0]
      },  
      sound: {
        fail,
        finish,
        stackSound,
        wellDone
      }    
    }
  },

  methods: {
    gameStart() {
      // 신규 주문 
      this.newOrderFunc = setInterval(() => {
        this.newOrder()
      }, 3000)
      // 타이머
      this.timer.timerFunc = setInterval(() => {
        this.timer.now -= 10
        let nowTime = this.timer.now
        nowTime += ''

        if(nowTime.split('').length != 4) {
          this.timer.value = nowTime.split('')
          this.timer.value.unshift(0)
        } else {
          this.timer.value = nowTime.split('')
        }

        if(this.timer.now <= 0) {
          clearInterval(this.newOrderFunc)
          clearInterval(this.timer.timerFunc)
          let audio = new Audio(this.sound.finish)
          audio.play()
          this.timer.max = 10000
          this.timer.now = 10000
          this.timer.value = [0, 0, 0]
          this.start = false
          this.dish = []
          this.orderList = []
          this.newOrder()
        }
      }, 10)
    },

    newOrder() {
      let n = 0 
      let newOrder = []
      while(n < 8) {
        newOrder.push(Math.floor(Math.random() * 6))
        n += 1
      }
      if(this.orderList.length < 3) {
        this.orderList.push(newOrder)
      }
    },  

    stackBurger() {
      let target = document.querySelector('.dish .burger .stack')
      if(target) {
        target.animate([
            {transform: 'translateY(-100px)'},
            {transform: 'translateY(0px)'}
        ], 100)
      }
    },

    async scoring(ingredients) {
      let firstOrder = this.orderList[0]
      if(ingredients == firstOrder[firstOrder.length - this.dish.length]) {
        this.stackBurger()
        let audio = new Audio(this.sound.stackSound)
        if(this.dish.length != 8) {
          audio.play()
        }
        this.score += 10
      } else {
        let audio = new Audio(this.sound.fail)
        audio.play()
        this.dish = []
        this.orderList.splice(0, 1)
        this.newOrder()
      }
    },

    myDish(event) {
      if(this.start == false) {
        this.score = 0
        this.start = true
        this.gameStart()
      }
      switch(event.keyCode) {
        case 81: 
        this.dish.unshift(0)
        this.scoring(0)
        break

        case 87: 
        this.dish.unshift(1)
        this.scoring(1)
        break  

        case 69: 
        this.dish.unshift(2)
        this.scoring(2)
        break

        case 65: 
        this.dish.unshift(3)
        this.scoring(3)
        break   

        case 83: 
        this.dish.unshift(4)
        this.scoring(4)
        break  

        case 68: 
        this.dish.unshift(5)
        this.scoring(5)
        break  
      }

      if(this.dish.length == 8) {
        // 카운터 감소
        if(this.timer.max > 2000) {
          this.timer.max -= 200 
        }
        this.timer.value = this.timer.max
        // 사운드
        let audio = new Audio(this.sound.wellDone)
        audio.play()
        // 점수
        this.cookedBurger.count += 1
        this.cookedBurger.list.push(this.dish)
        this.score += 200
        // 초기화
        this.orderList.splice(0, 1)
        if(this.orderList.length == 0) {
          this.newOrder()          
        }
        this.dish = []
      }
    },
  },

  created() {
    document.addEventListener('keydown', this.myDish)
    this.newOrder()
  },
  destroyed() {
    clearInterval(this.timer.timerFunc)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.content {
  display: flex;
  gap: 15px;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  max-width: 360px;
  width: 100%;
  height: 100%;
}

/* 점수, 시간 */ 
.information {
  display: flex;
  justify-content: space-around;
  align-items: center;
  width: 100%;

  .time {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    width: 80px;
    height: 80px;
    font-size: 18px;
    background: #FFFFFF;
    box-shadow: 0px 4px 20px rgba(255, 56, 74, 0.5);
    border-radius: 15px;
    div {
      width: 100%;
      height: 50px;
      line-height: 50px;
    }
    div:first-child {
      width: 100%;
      height: 30px;
      line-height: 30px;
      font-size: 14px;
    }
  }
  .score {
    display: flex;
    justify-content: space-evenly;
    width: 200px;
    height: 80px;
    > div {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      width: 80px;
      height: 80px;
      font-size: 18px;
      background: #FFFFFF;
      box-shadow: 0px 4px 20px rgba(255, 56, 74, 0.5);
      border-radius: 15px;
      div {
        width: 100%;
        height: 50px;
        line-height: 50px;
      }
      div:first-child {
        width: 100%;
        height: 30px;
        line-height: 30px;
        font-size: 14px;
      }
    }
  }
}

/* burger */
.burger {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 5px;
  div {
    width: 100px;
    height: 10px;
    border-radius: 5px;
    background-color: #000;
  }
}

/* order,dish */
.orderList {
  display: flex;
  justify-content: space-around;
  width: 100%;
  height: 180px;
  background: #FFFFFF;
  box-shadow: 0px 4px 20px rgba(255, 56, 74, 0.5);
  border-radius: 15px;
}
.order {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  width: 100px;
  height: 100%;
}

@keyframes stackAnimation {
  from {
    transform: translateY(-100px)
  }
  to {
    transform: translateY(0px)
  }
}
.dish {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  background: #FFFFFF;
  box-shadow: 0px 4px 20px rgba(255, 56, 74, 0.5);
  border-radius: 15px;
  width: 100%;
  height: 180px;
  .burger {
    .stack {
      animation-name: stackAnimation;
      animation-duration: 0.1s;
    }
  }
}

/* 키 설명 */
.key {
  display: flex;
  justify-content: center;
  gap: 8px;
  width: 100%;
  div {
    background-color: #ffb0a6;
    color: #fff;
    width: 50px;
    height: 50px;
    line-height: 50px;
    border-radius: 10px;
  }
}

</style>
