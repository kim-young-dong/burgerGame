<template>
  <div class="content" >
    <div class="orderList">
      <div class="order" v-for="(menu, ind) in orderList"
      :key="ind">
        <div class="burger">
          <div v-for="(ingredients, ind) in menu"
          :key="ind"
          :style="{backgroundColor: color[ingredients]}"/>
        </div>
      </div>
    </div>
    <div class="score">
      <div>
        {{ score }} 점
      </div>
      <div>
        {{ cookedBurger.count }} 개 완성
      </div>
    </div>
    <progress :value="timer.value" min="0" :max="timer.max"></progress>
    <div class="timer">

    </div>
    <div class="dish">
      <div v-if="!start">
        <div>주문과 동일한 메뉴가 되도록</div>
        <div>재료를 쌓으세요.</div>
      </div>
      <div v-if="!start">키보드를 누르면 시작합니다.</div>
      <div class="burger">
        <div v-for="(ingredients, ind) in dish"
        :class="ind == 0 ? 'stack' : ''"
        :key="ind"
        :style="{backgroundColor: color[ingredients]}"/>
      </div>
    </div>
    <div class="keyWrap">
      <div class="key">
        <div style="backgroundColor: #FFAF75">Q</div>
        <div style="backgroundColor: #FF2222">W</div>
        <div style="backgroundColor: #FFCB11">E</div>
      </div>
      <div class="key">
        <div style="backgroundColor: #6C431D">A</div>
        <div style="backgroundColor: #42CC36">S</div>
        <div style="backgroundColor: #548812">D</div>
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
        value: 10000
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
        this.timer.value -= 100
        if(this.timer.value <= 0) {
          clearInterval(this.newOrderFunc)
          clearInterval(this.timer.timerFunc)
          let audio = new Audio(this.sound.finish)
          audio.play()
          this.timer.max = 10000
          this.timer.value = 10000
          this.start = false
          this.dish = []
          this.orderList = []
          this.newOrder()
        }
      }, 100)
    },
    newOrder() {
      let n = 0 
      let newOrder = []
      while(n < 8) {
        newOrder.push(Math.floor(Math.random() * 6))
        n += 1
      }
      if(this.orderList.length < 5) {
        this.orderList.push(newOrder)
      }
    },    
    scoring(ingredients) {
      let firstOrder = this.orderList[0]
      if(ingredients == firstOrder[firstOrder.length - this.dish.length]) {
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
        this.score += 1000
        // 초기화
        this.orderList.splice(0, 1)
        if(this.orderList.length == 0) {
          this.newOrder()          
        }
        this.dish = []
      }
    }
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
<style scoped>
.content {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  height: 100%;
}
@keyframes stackBurger{
  from {
    transform: translateX(-100px);
  }
  to {
    transform: translateX(0px);
  }
}
/* 재료 */
.burger {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.burger div{
  width: 200px;
  height: 30px;
  background-color: #000;
}
.stack1, .stack2, .stack3, .stack4, .stack5, .stack6, .stack7, .stack8    {
  animation-name: stackBurger;
  animation-duration: 1s;
  animation-timing-function: 
  cubic-bezier(0.075, 0.82, 0.165, 1);
}
progress {
  width: 200px;
  height: 20px;
}
/* 주문 */
.orderList {
  display: flex;
  gap: 20px;
}
.order {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  width: 300px;
  height: 320px;
}
/* 점수 */ 
.score {
  display: flex;
  flex-direction: column;
  gap: 5px;
  width: 300px;
}
.score div {
  font-size: 16px;
  font-weight: bold;
  font-family:'Courier New', Courier, monospace;
}
/* 접시 */
.dish {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  width: 300px;
  height: 320px;
}

/* 키 설명 */
.keyWrap {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  gap: 10px;
  width: 300px;
}
.key {
  display: flex;
  justify-content: center;
  gap: 10px;
}
.key div {
  background-color: #ffb0a6;
  color: #fff;
  width: 50px;
  height: 50px;
  line-height: 50px;
  border-radius: 10px;
}

</style>
