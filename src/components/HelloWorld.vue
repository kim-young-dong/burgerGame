<template>
  <div class="content" >
    <div class="information">
      <div>목표</div>
      <div class="burger">
        <div v-for="(ingredients, ind) in order" 
        :key="ind" 
        :style="{backgroundColor: color[ingredients]}"/>
      </div>
    </div>
    <div class="score">
      {{score}} 점
    </div>
    <progress :value="time.value" min="0" :max="time.max"></progress>
    <div class="time">

    </div>
    <div class="dish">
      <div v-if="!start">목표와 동일한 모양을 만드세요</div>
      <div v-if="!start">방향은 위에서 시작하며 아래에서 끝납니다.</div>
      <div v-if="!start">키보드를 누르면 시작합니다.</div>
      <div class="burger">
        <div v-for="(ingredients, ind) in dish"
        :key="ind"
        :style="{backgroundColor: color[ingredients]}"/>
      </div>
    </div>
    <div class="key">
      <!-- 키보드 이벤트 추가 -->
      <div style="backgroundColor: #FFAF75">Q</div>
      <div style="backgroundColor: #FF2222">W</div>
      <div style="backgroundColor: #FFCB11">E</div>
      <div style="backgroundColor: #6C431D">A</div>
      <div style="backgroundColor: #42CC36">S</div>
      <div style="backgroundColor: #548812">D</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      time: {
        max: 10000,
        value: 10000
      },
      // 0빵 1토마토 2치즈 3고기 4양상추 5피클
      order: [0,2,3,4,1,1,2,0],
      dish: [],
      color: ['#FFAF75', '#FF2222', '#FFCB11', '#6C431D', '#42CC36', '#548812'],
      score: 0,
      timerFunc: null,
      start: false
    }
  },
  methods: {
    gameStart() {
      this.timerFunc = setInterval(() => {
        this.time.value -= 100
        if(this.time.value <= 0) {
          clearInterval(this.timerFunc)
          this.time.max = 10000
          this.time.value = 10000
          this.start = false
          this.newOrder()
          this.dish = []
        }
      }, 100)
    },
    scoring(ingredients) {
      if(ingredients == this.order[this.dish.lastIndexOf(ingredients)]) {
        this.score += 100
      } else {
        this.newOrder()
        this.dish = []
      }
    },
    newOrder() {
      for (let i in this.order) {
        this.order[i] = Math.floor(Math.random() * 6)
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
        this.dish.push(0)
        this.scoring(0)
        break

        case 87: 
        this.dish.push(1)
        this.scoring(1)
        break  

        case 69: 
        this.dish.push(2)
        this.scoring(2)
        break

        case 65: 
        this.dish.push(3)
        this.scoring(3)
        break   

        case 83: 
        this.dish.push(4)
        this.scoring(4)
        break  

        case 68: 
        this.dish.push(5)
        this.scoring(5)
        break  
      }
      if(this.dish.length == 8) {
        if(this.time.max > 2000) {
          this.time.max -= 100 
        }
        this.time.value = this.time.max
        this.score += 1000
        this.newOrder()
        this.dish = []
      }
    }
  },
  created() {
    document.addEventListener('keydown', this.myDish)
    this.newOrder()
  },
  destroyed() {
    clearInterval(this.timerFunc)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 800px;
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
progress {
  width: 200px;
  height: 20px;
}
/* 주문 */
.information {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  width: 300px;
  height: 400px;
}
/* 접시 */
.dish {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  width: 300px;
  height: 400px;
}

/* 키 설명 */
.key {
  display: flex;
  justify-content: space-evenly;
  width: 400px;
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
