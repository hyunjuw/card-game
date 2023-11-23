<script setup lang="ts">
import { ref } from 'vue'
  interface cardType {
    color: string,
    isOpen: boolean,
    isMatch: boolean
  }
  const cardArr = ref<cardType[]>([
    { color: 'red', isOpen: false, isMatch: false },
    { color: 'red', isOpen: false, isMatch: false },
    { color: 'orange', isOpen: false, isMatch: false },
    { color: 'orange', isOpen: false, isMatch: false },
    { color: 'green', isOpen: false, isMatch: false },
    { color: 'green', isOpen: false, isMatch: false },
    { color: 'gray', isOpen: false, isMatch: false },
    { color: 'gray', isOpen: false, isMatch: false },
    { color: 'skyblue', isOpen: false, isMatch: false },
    { color: 'skyblue', isOpen: false, isMatch: false },
    { color: 'pink', isOpen: false, isMatch: false },
    { color: 'pink', isOpen: false, isMatch: false },
  ])
  const selectedCard = ref<cardType[]>([])
  const matched = ref<cardType[]>([])
  const timer = ref()
  const timerCount = ref(30)
  const isDisabled = ref(true)

  const gameStart = () => { // start
    shuffle(cardArr.value)
    cardArr.value.forEach((item) => {
      item.isOpen = true
    })
    setTimeout(() => {
      cardArr.value.forEach((item) => {
        item.isOpen = false
        isDisabled.value = false
      })
      startTimer()
    }, 3000)
  }

  const shuffle = (array: cardType[]) => {  // 카드석기
    array.sort(() => Math.random() - 0.5)
  }

  const onFlip = (index: number) => {
    cardArr.value[index].isOpen = true
    if (selectedCard.value.length < 2) {
      selectedCard.value.push(cardArr.value[index])
      selectedCard.value.forEach((item, i, arr) => {
        if (selectedCard.value.length === 2) {
          if (arr[0].color === arr[1].color && arr[0] !== arr[1]){
            item.isMatch = true
            setTimeout(() => {
              item.isOpen = false
            }, 200)
            matched.value.push(item)
          }else {
            item.isMatch = false
            setTimeout(() => {
              cardArr.value.forEach((val) => {
                val.isOpen = false
              })
            }, 200)
          }
        }
      })
    }else {
      selectedCard.value.splice(0, 2, cardArr.value[index]);
      cardArr.value.forEach(item => {
        item.isOpen = false
        cardArr.value[index].isOpen = true
      })
    }
    gameResult()
  }

  const startTimer = () => {
    timer.value = setInterval(() => {
      if (timerCount.value !== 0){
        timerCount.value--;
      }else {
        clearInterval(timer.value)
        alert('제한시간이 초과되었습니다!')
      }
    }, 1000);
  }
  const gameResult = () => {
    if (cardArr.value.length === matched.value.length){ // 성공
      clearInterval(timer.value)
      if (timerCount.value !== 0) {
        setTimeout(() => {
          alert('축하합니다! 당신의 기록은 '+ timerCount.value +'초 입니다.')
        }, 200)
      }
    }
  }
</script>
<template>
  <div class="card">
    <div
      class="card-grid"
      :class="{'is-disabled':isDisabled}"
    >
      <template
        v-for="(card, index) in cardArr"
        :key="index"
      >
        <button
          type="button"
          class="card-item"
          :class="[{'flip':card.isOpen}, {'matched':card.isMatch}]"
          @click="onFlip(index)"
        >
          <span
            class="face front"
            :class="card.color"
          >Front</span>
          <span class="face back">Back</span>
        </button>
      </template>
    </div>
  </div>
  <div class="timer">
    Timer: {{ timerCount }}
    <br>
    <button
      type="button"
      @click="gameStart"
    >
      Game Start
    </button>
    <br>
    <button
      type="button"
      @click="$router.go(0)"
    >
      Reset
    </button>
  </div>
</template>

<style lang="scss">
</style>
