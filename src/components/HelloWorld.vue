<template>
  <div class="content">
    <div class="information">
      <div class="time">

      </div>
      <div class="burger">
        <div v-for="(ingredients, ind) in order" 
        :key="ind" 
        :style="{backgroundColor: color[ingredients]}"></div>
      </div>
    </div>
    <div class="score">
      {{score}} 점
    </div>
    <div class="burger">
      <div v-for="(ingredients, ind) in order" 
      :key="ind" 
      :style="{backgroundColor: color[ingredients]}"></div>
    </div>
    <div class="key">
      <!-- 키보드 이벤트 추가 -->
      <div style="backgroundColor: #FF2222">Q</div>
      <div style="backgroundColor: #FFCB11">W</div>
      <div style="backgroundColor: #6C431D">E</div>
      <div style="backgroundColor: #42CC36">R</div>
      <div class="space" style="backgroundColor: #FFAF75">Space</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 0빵 1토마토 2치즈 3고기 4양상추
      order: [0,2,3,4,1,1,2,0],
      dish: [0],
      color: ['#FFAF75', '#FF2222', '#FFCB11', '#6C431D', '#42CC36'],
      score: 0
    }
  },
  methods: {
    name() {
      
    },
  },
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
.information {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 300px;
  height: 400px;
  background-color: blanchedalmond;
}
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
.key div.space {
  width: 150px;
}
</style>
