<template>
  <div class="text-white" :style="{ backgroundColor: background }">
    <div v-if="showGameMods"
      class=" relative-positionr mainGamemode"
      >

      <div class=" flex items-end">
        <div>
        <div class=" flex justify-between text-center q-px-xl f2w text-bold" style="width: 100vw;">
          <div class="">Easy</div>
          <div>Medium</div>
          <div>Hard</div>
        </div>
         <!-- ปุ่มเลือกGameMod -->
        <div class="flex justify-between no-wrap  q-px-xl q-mt-md" style="width: 100vw;">
          <q-radio v-model="gameMods" val="1" size="lg" class="bg-radio" dense/>
          <q-radio v-model="gameMods" val="2" size="lg" class="bg-radio" dense/>
          <q-radio v-model="gameMods" val="3" size="lg" class="bg-radio" dense/>
          <q-radio v-model="gameMods" val="4" size="lg" class="bg-radio" dense/>
          <q-radio v-model="gameMods" val="5" size="lg" class="bg-radio" dense/>
         </div>
        </div>
      </div>
      <div class=" flex items-center justify-center">
        <BTN
        @confirm="setStartGame()"
        icon="fa-solid fa-play"
        width="20vw"
        height="20vw"
        iconSize="10vw"
        ></BTN>
      </div>
      <div class="flex justify-center items-end q-pb-md">
        <div class="flex  justify-between "
        style="width: 95vw;
        ">
        <!-- ปุ่มตั้งค่า -->
        <BTN @confirm="showSeting = true"
        icon="fa-solid fa-gear"
        width="8vw"
        height="8vw"
        ></BTN>
        <!-- ปุ่มอื่นๆ -->
        <BTN
        icon="fa-solid fa-star"
        width="8vw"
        height="8vw"
        @click="showSeting = true"
        ></BTN>
      </div>
    </div>

    </div>
    <!-- เล่นเกม -->
    <div v-if="playGame" class="mainGamePlay">
      <div class=" q-py-md flex justify-center">
        <div class="" style="width: fit-content">
          <div class="flex justify-center">
            <BTN icon="fa-solid fa-house"
            @confirm="(playGame = false), (showGameMods = true), (startGame = false)"
            ></BTN>
            </div>
          <div class="text-center f4w q-mt-md">
            Matches <br/> {{ matches }}
          </div>
          <div class="text-center f4w">
            Turns <br/> {{ turns}}
          </div >
          <div class="text-center f4w" v-if="showTimer">
            Time <br/> <span v-if="startGame">{{ currentTime }}</span>
          </div>
      </div>
      </div>
      <div class="flex items-center justify-center">
        <div class="row"
        :class="lvShow.subLv[sub].col == 3 ? 'grid3X3':
        lvShow.subLv[sub].col == 4 ? 'grid4X4': ''"
        v-if="gameMods == 1"
        >

            <div v-for="(item, index) in lvShow.subLv[sub].format" :key="index"
            >
              <div class="" style="height: 100px; width: 100px;">

                <div v-if="item.card && !item.card.match && removePairs">
                <img :src="peek != true && !item.card.peek ? cardBack :item.card.img" alt="" style="width: 100px; height: 100px;"
                v-if="item.space == undefined"
                @click="ckMatch(index)"
                >
              </div>

              </div>
            </div>
        </div>
        <div v-else class="flex items-center justify-center text-black" style="height: 100%; width: 100%;">
          ทำไม่ทันจ้า
        </div>
      </div>
    </div>

    <!-- ไปsub เลเวล ต่อไป -->
    <div v-if="showNext" style="width: 100vw; height: 100vh;"
    class="flex items-center justify-center"
    >
      <BTN
        @confirm="goToNext()"
        :class="showNext ?  'slide-in': ''"
        icon="fa-solid fa-forward"
        width="20vw"
        height="20vw"
        iconSize="10vw"
        ></BTN>

    </div>
  </div>
  <q-dialog v-model="showSeting" persistent maximized>
    <q-card class="flex justify-center"
    :style="{ background: background }"
    >
      <div class="flex items-center justify-center relative-position" >
        <div style=" width: 95vw; height: 95vh; overflow-y: auto;
        overflow-x: hidden;
        "
        class=" q-py-xl q-px-md">
          <!-- revealTime -->
          <div class="flex items-center no-wrap justify-between q-my-sm">
            <div class="f4w text-bold">Reveal Time</div>
            <div style="width:50%">
            <q-slider
            v-model="revealTime"
            :min="0.2"
            :max="2.5"
            :step="0.1"
            :label-value="revealTime + 's'"
            label-always
            color="grey-10"
            label-color="transparent "
          />
        </div>
          </div>
          <!-- Card Back -->
          <div class="flex items-center no-wrap gridMain q-my-sm"
          >
          <div class="f4w text-bold no-wrap">Card Back</div>
          <div class="cardBackContent">
          <div v-for="img in cardBackImg" :key="img"
          class="flex justify-center items-center"
          :class="cardBack == img? 'selected': 'cardBackImg'"
          >
            <img :src="img"
                style="

                width: 10vw; width: 10vw;"
                class=""
                @click="cardBack = img"
              >
            </div>
            </div>
          </div>
           <!-- background -->
           <div class="flex  items-center no-wrap gridMain q-my-sm">
            <div class="f4w text-bold q-mr-sm">Background</div>
            <div class="flex no-wrap" style="gap: 2px;">
              <div v-for="bg in backgroundColor" :key="bg"
              :style="{ backgroundColor: bg }"
                style="
                width: 10vw;
                height: 10vw;"
                :class="background == bg ? 'selected': 'backgroundDiv'"
                @click="background = bg"
              ></div>

            </div>
          </div>
          <!-- ShowTimer -->
          <div class="flex items-center no-wrap gridMain q-my-sm">
            <div class="f4w text-bold" >Show Timer</div>
            <div class=""
            >
              <q-checkbox
              v-model="showTimer"
              class=""
              dense
              color="grey-9"
              />
            </div>
          </div>
          <!-- Card Peek -->
          <div class="flex items-center no-wrap gridMain q-my-sm">
            <div class="f4w text-bold" >Card Peek</div>
            <div class=""
            >
              <q-checkbox
              v-model="cardPeek"
              class=""
              dense
              color="grey-9"
              />
            </div>
          </div>
          <!-- Remove Pairs -->
          <div class="flex items-center no-wrap gridMain q-my-sm">
            <div class="f4w text-bold" >Remove Pairs</div>
            <div class=""
            >
              <q-checkbox
              v-model="removePairs"
              class=""
              dense
              color="grey-9"
              />
            </div>
          </div>
        </div>
      </div>

      <!-- ตั้งค่าด้านล่าง เปิด/ปิดเสียง ,ค่าเริ่มต้น, ยืนยัน -->
      <div class="setingBottom flex items-center justify-center"
      >
      <div class="relative-position flex items-center">
        <!-- เกี่ยวกับเสียง -->
        <BTN @confirm="setSongbackground()"
        :icon="playsongbackground ? 'fa-solid fa-volume-high': 'fa-solid fa-volume-xmark'"
        class="q-mr-sm">
        </BTN>
        <BTN @confirm="playsound = !playsound"
        :icon="playsound ? 'fa-solid fa-volume-high': 'fa-solid fa-volume-xmark'"
        ></BTN>
        <!-- ค่าเริ่มต้น -->
        <BTN
        icon=""
        label="set to default"
        @confirm="setDefault()"
        class="q-mx-md"
        ></BTN>
          <!-- ยืนยัน -->
        <BTN
        icon=""
        label="OK"
        fontSize="3vw"
        @confirm="showSeting = false"
        width="9vw" height="9vw"></BTN>
      </div>
      </div>
    </q-card>
  </q-dialog>
</template>
<script setup>
import { ref } from 'vue'
import { Howl } from 'howler'
import BTN from '../components/customButton.vue'
// import { ref, onMounted, nextTick } from "vue";
// import { useQuasar } from "quasar";
defineOptions({
  name: 'IndexPage'
})
const showGameMods = ref(true)
const playGame = ref(false)
const showSeting = ref(false)
const showTimer = ref(false)
const cardPeek = ref(true)
const removePairs = ref(true)
const gameMods = ref('1')
const background = ref('#f7bc63')
const revealTime = ref(0.8)
const cardBack = ref('/img/cardBack1.webp')
const matches = ref(0)
const turns = ref(0)
const backgroundColor = ref([
  '#f7bc63',
  '#343794',
  '#806153',
  '#546942',
  '#765585'
])
const cardBackImg = ref([
  '/img/cardBack1.webp',
  '/img/cardBack2.webp',
  '/img/cardBack3.webp',
  '/img/cardBack4.webp',
  '/img/cardBack5.webp',
  '/img/cardBack6.webp',
  '/img/cardBack7.webp',
  '/img/cardBack8.webp'
])

const songbackground = '../song/catsong.mp3'
const audio = ref(null)
const playsongbackground = ref(false)
const setSongbackground = (reset) => {
  // ถ้ามีการเล่นเสียงอยู่แล้ว ให้หยุดเสียง
  if (audio.value || reset !== undefined) {
    audio.value.stop()
    audio.value = null
    playsongbackground.value = false
  } else {
    playsongbackground.value = true
    // หากไม่มีการเล่นเสียง ให้เริ่มเล่นเสียงเพลง
    audio.value = new Howl({
      src: [songbackground],
      autoplay: true,
      loop: true
    })
  }
}

const playsound = ref(true)

const setDefault = () => {
  revealTime.value = 0.8
  cardBack.value = '/img/cardBack1.webp'
  background.value = '#f7bc63'
  showTimer.value = false
  cardPeek.value = true
  removePairs.value = true
  playsongbackground.value = false
  playsound.value = true
  setSongbackground('reset')
}

const setStartGame = () => {
  sub.value = 0
  matches.value = 0
  turns.value = 0
  lvShow.value = []

  if (gameMods.value === '1') {
    lvShow.value = JSON.parse(JSON.stringify(lv.value[0]))
    randonCard(lvShow.value.subLv[sub.value])
  }
  resetTimer()
  startCountdown()
  showGameMods.value = false
  playGame.value = true
}

let intervalId = null

const startGame = ref(false)
const peek = ref(false)

const startCountdown = () => {
  turns.value = 0
  matches.value = 0
  peek.value = cardPeek.value
  let countdown = revealTime.value
  intervalId = setInterval(() => {
    countdown -= 0.1
    if (countdown <= 0) {
      clearInterval(intervalId)
      startGame.value = true
      peek.value = false
      if (showTimer.value) {
        continueTimer()
      }
      // ทำสิ่งที่ต้องการหลังจากนับถอยหลังเสร็จ
    }
  }, 100) // ทุก ๆ 100 มิลลิวินาที (0.1 วินาที)
}

const showNext = ref(false)

const stopTimer = () => {
  clearInterval(timerId.value)
}
const continueTimer = () => {
  startTimer()
}
const resetTimer = () => {
  clearInterval(timerId)
  totalSeconds = 0
  currentTime.value = '00:00'
}
let totalSeconds = 0
const timerId = ref(null)
const currentTime = ref('00:00')
const startTimer = () => {
  timerId.value = setInterval(() => {
    ++totalSeconds
    const hours = Math.floor(totalSeconds / 3600)
    const minutes = Math.floor((totalSeconds % 3600) / 60)
    const seconds = totalSeconds % 60

    const formattedHours = String(hours).padStart(2, '0')
    const formattedMinutes = String(minutes).padStart(2, '0')
    const formattedSeconds = String(seconds).padStart(2, '0')

    let displayTime
    if (hours > 0) {
      displayTime = `${formattedHours}:${formattedMinutes}:${formattedSeconds}`
    } else {
      displayTime = `${formattedMinutes}:${formattedSeconds}`
    }

    currentTime.value = displayTime
  }, 1000)
}

const sub = ref(0)

const listCard = [{
  id: 1,
  img: '/Card/c1.jpg',
  peek: false,
  match: false
},
{
  id: 2,
  img: '/Card/c2.jpg',
  peek: false,
  match: false
},
{
  id: 3,
  img: '/Card/c3.jpg',
  peek: false,
  match: false
},
{
  id: 4,
  img: '/Card/c4.jpg',
  peek: false,
  match: false
},
{
  id: 5,
  img: '/Card/c5.jpg',
  peek: false,
  match: false
},
{
  id: 6,
  img: '/Card/c6.jpg',
  peek: false,
  match: false
}
]
const numFormat = ref([])
const randonCard = (data) => {
  numFormat.value = []
  const getRandomNumber = () => Math.floor(Math.random() * 6) + 1
  let i = null
  const card = []
  while (card.length < data.card) {
    i = getRandomNumber()
    if (!card.includes(i) && card.length < 6) {
      card.push(i)
      setFormat(data.format, i)
    } else if (card.length >= 6) {
      card.push(i)
      setFormat(data.format, i)
    }
  }
  // let card = data.card
}

const setFormat = (data, numCard) => {
  const getRandomNumberFormat = () => Math.floor(Math.random() * data.length)
  let canUse = 2
  while (canUse > 0) {
    const k = getRandomNumberFormat()

    if (data[k]?.space === undefined) {
      if (data[k].card === null) {
        lvShow.value.subLv[sub.value].format[k].card = listCard[numCard - 1]
        canUse -= 1
      }
    }
  }
  console.log('numFormatnumFormat', numFormat.value)
}

const lv = ref([
  {
    lv: 1,
    subLv: [
      {
        col: 3,
        id: 1,
        format:
        [
          {
            index: 0,
            card: null
          }, {
            index: 1,
            card: null
          }, {
            index: 2,
            card: null
          }, {
            index: 3,
            card: null
          },
          {
            index: 4,
            space: true
          }, {
            index: 5,
            card: null
          },
          {
            index: 6,
            card: null
          }, {
            index: 7,
            card: null
          },
          {
            index: 8,
            card: null
          }
        ],
        card: 4
      },
      {
        col: 4,
        id: 2,
        format:
        [
          {
            index: 0,
            card: null
          }, {
            index: 1,
            card: null
          }, {
            index: 2,
            card: null
          }, {
            index: 3,
            card: null
          },
          {
            index: 4,
            card: null
          }, {
            index: 5,
            card: null
          },
          {
            index: 6,
            card: null
          }, {
            index: 7,
            card: null
          },
          {
            index: 8,
            card: null
          },
          {
            index: 10,
            card: null
          },
          {
            index: 11,
            card: null
          },
          {
            index: 12,
            card: null
          }
        ],
        card: 6
      },
      {
        col: 4,
        id: 3,
        format:
        [
          {
            index: 0,
            space: true

          },
          {
            index: 1,
            card: null
          },
          {
            index: 2,
            card: null
          },
          {
            index: 3,
            space: true
          },
          {
            index: 4,
            card: null
          },
          {
            index: 5,
            card: null
          },
          {
            index: 6,
            card: null
          },
          {
            index: 7,
            card: null
          },
          {
            index: 8,
            card: null
          },
          {
            index: 9,
            card: null
          },
          {
            index: 10,
            card: null
          },
          {
            index: 11,
            card: null
          },
          {
            index: 12,
            card: null
          },
          {
            index: 13,
            card: null
          },
          {
            index: 14,
            card: null
          },
          {
            index: 15,
            card: null
          }
        ],
        card: 7
      }
    ]
  }
])

const playSoundClick = () => {
  const audio = new Audio('/song/click.mp3')
  audio.play()
}

const previousCardid = ref(null)
const previousCardindex = ref(null)
const onClick = ref(false)
const ckMatch = (index) => {
  if (!onClick.value) {
    if (playsound.value) {
      playSoundClick()
    }
    const card = { ...lvShow.value.subLv[sub.value].format[index].card }
    card.peek = true
    lvShow.value.subLv[sub.value].format[index].card = card
    onClick.value = true
    if (previousCardid.value == null) {
      previousCardid.value = lvShow.value.subLv[sub.value].format[index].card.id
      previousCardindex.value = index
      console.log('previousCardid', previousCardid.value)
      onClick.value = false
    } else {
      if (index !== previousCardindex.value) {
        if (previousCardid.value === lvShow.value.subLv[sub.value].format[index].card.id) {
          matches.value += 1
          turns.value += 1
          setTimeout(() => {
            lvShow.value.subLv[sub.value].format[index].card.match = true
            lvShow.value.subLv[sub.value].format[previousCardindex.value].card.match = true
            previousCardid.value = null
            previousCardindex.value = null
            if (matches.value === lvShow.value.subLv[sub.value].card) {
              stopTimer()
              playGame.value = false
              showNext.value = true
            }
            onClick.value = false
          }, 1000)
        } else {
          turns.value += 1
          setTimeout(() => {
            lvShow.value.subLv[sub.value].format[index].card.peek = false
            lvShow.value.subLv[sub.value].format[previousCardindex.value].card.peek = false
            previousCardid.value = null
            previousCardindex.value = null
            onClick.value = false
          }, 1000)
        }
      } else {
        onClick.value = false
      }
    }
  }
}

const goToNext = () => {
  if (!onClick.value && sub.value !== 2) {
    onClick.value = true
    matches.value = 0
    turns.value = 0
    sub.value += 1
    console.log('sub.value ', sub.value)
    lvShow.value = JSON.parse(JSON.stringify(lv.value[0]))
    randonCard(lvShow.value.subLv[sub.value])
    showNext.value = false
    playGame.value = true
    startCountdown()
    onClick.value = false
  }
}

const lvShow = ref(null)

// onMounted(() => {
//   randonCard(lvShow.value.subLv[0])
// })
</script>

<style scoped>
@keyframes slideIn {
  0% {
    transform: translateY(-100%);
  }
  100% {
    transform: translateY(0%);
  }
}

.slide-in {
  animation: slideIn 1s forwards;
}

</style>
