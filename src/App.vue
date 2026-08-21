<script setup lang="ts">
import { computed, ref } from 'vue'

type ColorOption = { id: string; name: string; hex: string }
type ColorQuestion = {
  scene: string; category: string; frame: string; frameColor: string
  layout: 'scene' | 'detail' | 'type' | 'pixel'
  options: ColorOption[]; answer: string[]; explanation: string; reasons: Record<string, string>
}
type AnswerResult = { selected: string[]; matched: string[] }

const questions: ColorQuestion[] = [
  {
    scene: '雨停之後，城市還留著一點濕冷的光。', category: '城市天氣', frame: '以沉靜、帶水氣的藍灰色為底', frameColor: '#526777', layout: 'scene',
    options: [
      { id: 'rain-1', name: '霧藍', hex: '#9bb5bd' }, { id: 'rain-2', name: '柏油灰', hex: '#3f4c51' }, { id: 'rain-3', name: '苔綠', hex: '#687b6c' }, { id: 'rain-4', name: '暖黃', hex: '#d6ad57' },
      { id: 'rain-5', name: '玻璃灰', hex: '#c6d0cc' }, { id: 'rain-6', name: '磚紅', hex: '#9c5c50' }, { id: 'rain-7', name: '深夜藍', hex: '#243746' }, { id: 'rain-8', name: '水泥米', hex: '#b4aea0' },
    ], answer: ['rain-1', 'rain-7', 'rain-2', 'rain-5', 'rain-4'], explanation: '雨天變暗後，暖黃街燈成為城市裡真正的光源；霧藍與深夜藍建立濕冷天空，柏油灰是濕街道，玻璃灰則負責窗面與路面的反射。', reasons: {
      'rain-1': '符合雨後空氣的濕冷與霧氣。', 'rain-2': '是被雨水打濕、反射光線的柏油路。', 'rain-3': '苔綠可以出現在潮濕城市角落，但不是這個畫面的核心。', 'rain-4': '下雨變暗後亮起的街燈與店燈，是明確的暖色光源。', 'rain-5': '代表窗面與路面的灰色反射，屬於城市材質而非光源。', 'rain-6': '暖度太像磚牆或夕陽，和雨後冷感不一致。', 'rain-7': '代表雨天提早變暗的天空與城市背景。', 'rain-8': '和柏油灰同樣是灰色地面／建築質感，功能太接近。',
    },
  },
  {
    scene: '凌晨三點，唯一還亮著的便利商店。', category: '深夜記憶', frame: '以人工光源切開深色夜幕', frameColor: '#273044', layout: 'detail',
    options: [
      { id: 'store-1', name: '螢光綠', hex: '#b8d66c' }, { id: 'store-2', name: '夜幕藍', hex: '#202b43' }, { id: 'store-3', name: '冷白光', hex: '#e8eee7' }, { id: 'store-4', name: '番茄紅', hex: '#c45243' },
      { id: 'store-5', name: '奶茶棕', hex: '#b18d69' }, { id: 'store-6', name: '塑膠橘', hex: '#ed9c4d' }, { id: 'store-7', name: '深紫', hex: '#433c5e' }, { id: 'store-8', name: '薄荷灰', hex: '#9ab3a7' },
    ], answer: ['store-1', 'store-2', 'store-3', 'store-6', 'store-4'], explanation: '夜幕藍建立凌晨的孤獨感，冷白光是店內不眠的燈，螢光綠與塑膠橘帶出招牌和貨架，番茄紅補上促銷、熱食區與招牌的辨識度。', reasons: {
      'store-1': '像便利商店招牌、貨架或包裝上的螢光色。', 'store-2': '是凌晨三點的夜幕與店外街道。', 'store-3': '對應店內整晚不關的冷白燈。', 'store-4': '便利商店常見的促銷、熱食與招牌色。', 'store-5': '太像溫暖居家飲品，缺少便利商店的人工感。', 'store-6': '像塑膠包裝、暖色燈與夜裡的小片暖光。', 'store-7': '氣氛偏神秘或夜店，不像明亮的便利商店。', 'store-8': '和冷白光功能重複，存在感不如紅色招牌。',
    },
  },
  {
    scene: '夏天海邊，一面被曬熱、帶鹽味的老牆。', category: '夏日場景', frame: '以溫暖日照承接海風的清爽', frameColor: '#d9a45b', layout: 'type',
    options: [
      { id: 'wall-1', name: '曬熱黃', hex: '#e4b866' }, { id: 'wall-2', name: '海水藍', hex: '#5da5ad' }, { id: 'wall-3', name: '褪色珊瑚', hex: '#d27865' }, { id: 'wall-4', name: '墨黑', hex: '#282b2c' },
      { id: 'wall-5', name: '葡萄紫', hex: '#72546d' }, { id: 'wall-7', name: '海藻綠', hex: '#557e71' }, { id: 'wall-8', name: '貝殼白', hex: '#f0e5cf' },
    ], answer: ['wall-1', 'wall-2', 'wall-3', 'wall-7', 'wall-8'], explanation: '曬熱黃是直立老牆吸收的陽光，海水藍帶來鹽味與風，褪色珊瑚、海藻綠與貝殼白補上牆面細節，形成明亮的海邊復古感。', reasons: {
      'wall-1': '是被夏日陽光曬熱的直立牆面主色。', 'wall-2': '帶出牆外的海、鹽味與清爽空氣。', 'wall-3': '像老牆上褪掉的珊瑚色油漆。', 'wall-4': '對比太重，會把曬熱與海風的輕盈感壓暗。', 'wall-5': '偏神秘與夜色，不像夏日老牆。', 'wall-7': '像牆角潮濕處的海藻與植物。', 'wall-8': '像鹽痕、剝落牆面與強烈日光。',
    },
  },
  {
    scene: '復古遊戲第一關，音樂剛響起，冒險還沒有開始。', category: '遊戲想像', frame: '以高對比的像素感喚起童年期待', frameColor: '#392b51', layout: 'pixel',
    options: [
      { id: 'game-1', name: '像素紫', hex: '#7656a2' }, { id: 'game-2', name: '能量青', hex: '#4fc4bb' }, { id: 'game-3', name: '金幣黃', hex: '#edc44d' }, { id: 'game-4', name: '森林綠', hex: '#47704e' },
      { id: 'game-5', name: '灰階白', hex: '#d0d1c7' }, { id: 'game-7', name: '泥土棕', hex: '#866044' }, { id: 'game-8', name: '深海藍', hex: '#274f76' },
    ], answer: ['game-1', 'game-2', 'game-3', 'game-4', 'game-6'], explanation: '像素紫作為懷舊背景，能量青帶出遊戲介面的電子感，金幣黃像等待被發現的獎勵，森林綠補出第一關的探索場景，警報紅則代表敵人或危險提示。', reasons: {
      'game-1': '像復古主機與早期遊戲常見的深色背景。', 'game-2': '帶出電子介面、能量與螢幕光。', 'game-3': '像第一關裡可收集的金幣與獎勵。', 'game-4': '建立冒險開始時的草地、森林與探索感。', 'game-5': '太像除錯畫面或單色介面，缺少冒險情緒。', 'game-6': '可代表敵人、危險、攻擊或生命值警示。', 'game-7': '偏向寫實泥土，不像這組像素世界的主要識別色。', 'game-8': '深海藍雖合理，但比警報紅少了遊戲事件的明確訊號。',
    },
  },
]

const questionIndex = ref(0)
const selected = ref<string[]>([])
const results = ref<AnswerResult[]>([])
const submitted = ref(false)
const finished = ref(false)
const currentQuestion = computed(() => questions[questionIndex.value])
const currentResult = computed(() => results.value[questionIndex.value])
const answerColors = computed(() => currentQuestion.value.answer.map((id) => currentQuestion.value.options.find((option) => option.id === id)?.hex ?? '#b9bbb5'))
const score = computed(() => results.value.reduce((total, result) => total + result.matched.length, 0))
const maxScore = questions.length * 3
const colorProfile = computed(() => {
  if (score.value >= 10) return { title: '情境配色家', note: '你能把文字裡的氣氛，轉成很有方向感的色彩。' }
  if (score.value >= 7) return { title: '色彩觀察家', note: '你對顏色的情緒與場景聯想很敏銳。' }
  if (score.value >= 4) return { title: '直覺調色師', note: '你的選擇有自己的個性，再多一點觀察就會更精準。' }
  return { title: '配色探險者', note: '你願意相信直覺，這正是配色遊戲最有趣的地方。' }
})

function toggleColor(id: string) {
  if (submitted.value) return
  if (selected.value.includes(id)) selected.value = selected.value.filter((colorId) => colorId !== id)
  else if (selected.value.length < 3) selected.value = [...selected.value, id]
}
function submitAnswer() {
  if (selected.value.length !== 3 || submitted.value) return
  const matched = selected.value.filter((id) => currentQuestion.value.answer.includes(id))
  results.value[questionIndex.value] = { selected: [...selected.value], matched }
  submitted.value = true
}
function nextQuestion() {
  if (questionIndex.value === questions.length - 1) { finished.value = true; return }
  questionIndex.value += 1; selected.value = []; submitted.value = false
}
function restartGame() {
  questionIndex.value = 0; selected.value = []; results.value = []; submitted.value = false; finished.value = false
}
</script>

<template>
  <main class="game-shell">
    <nav aria-label="Main navigation">
      <a class="brand" href="./" aria-label="Mood Match home" @click.prevent="restartGame"><span class="brand-mark"><span></span><span></span><span></span></span><span>mood match</span></a>
      <span class="nav-note">A small color intuition game</span>
    </nav>

    <section v-if="!finished" class="game-area">
      <header class="game-header">
        <div><p class="eyebrow"><span></span> Color mood quiz</p><h1>把情境，<em>配成顏色。</em></h1><p class="intro">每一題都有一個色彩框架。從 8 個色塊中選出 3 個，拼出你認為最符合情境的配色。</p></div>
        <div class="progress" aria-label="Game progress"><span>ROUND</span><strong>{{ String(questionIndex + 1).padStart(2, '0') }}<small> / {{ String(questions.length).padStart(2, '0') }}</small></strong></div>
      </header>

      <article class="question-card" :style="{ '--frame-color': currentQuestion.frameColor }">
        <div class="question-meta"><span>{{ currentQuestion.category }}</span><span>選 3 個色塊</span></div>
        <div v-if="submitted" class="scene-visual" :class="`visual-${currentQuestion.layout}`" aria-label="本題解答圖片">
          <div class="visual-main"><span v-for="color in answerColors" :key="color" :style="{ '--answer-color': color }"></span></div>
        </div>
        <h2>{{ currentQuestion.scene }}</h2>
        <p class="frame-hint"><i :style="{ backgroundColor: currentQuestion.frameColor }"></i>{{ currentQuestion.frame }}</p>
        <div class="color-grid" role="group" aria-label="Color choices">
          <button v-for="(option, optionIndex) in currentQuestion.options" :key="option.id" class="color-choice" :class="{ selected: selected.includes(option.id), correct: submitted && currentQuestion.answer.includes(option.id), incorrect: submitted && selected.includes(option.id) && !currentQuestion.answer.includes(option.id), muted: submitted && !currentQuestion.answer.includes(option.id) && !selected.includes(option.id) }" type="button" :aria-label="`色塊 ${optionIndex + 1}${submitted && currentQuestion.answer.includes(option.id) ? '，正確答案' : selected.includes(option.id) ? '，錯誤' : ''}`" :aria-pressed="selected.includes(option.id)" @click="toggleColor(option.id)"><span class="color-circle" :style="{ backgroundColor: option.hex }"><b v-if="selected.includes(option.id)">{{ selected.indexOf(option.id) + 1 }}</b><i v-if="submitted && (currentQuestion.answer.includes(option.id) || selected.includes(option.id))" class="answer-mark">{{ currentQuestion.answer.includes(option.id) ? '✓' : '×' }}</i></span></button>
        </div>
        <div v-if="!submitted" class="action-row"><span>{{ selected.length }} / 3 selected</span><button class="primary-button" type="button" :disabled="selected.length !== 3" @click="submitAnswer">完成配色 <b>→</b></button></div>
        <div v-else class="feedback" aria-live="polite"><div><strong>{{ currentResult.matched.length }} / 3 個配色方向符合</strong><p>{{ currentQuestion.explanation }}</p><div class="reason-list"><div v-for="option in currentQuestion.options" :key="option.id" class="reason-item" :class="{ valid: currentQuestion.answer.includes(option.id), picked: selected.includes(option.id) }"><span class="reason-mark">{{ currentQuestion.answer.includes(option.id) ? '✓' : '×' }}</span><span class="reason-color" :style="{ backgroundColor: option.hex }"></span><p>{{ currentQuestion.reasons[option.id] }}</p></div></div></div><button class="primary-button" type="button" @click="nextQuestion">{{ questionIndex === questions.length - 1 ? '查看結果' : '下一題' }} <b>→</b></button></div>
      </article>
    </section>

    <section v-else class="result-page">
      <p class="eyebrow"><span></span> Your color story</p><h1>你的配色，<em>有自己的氣候。</em></h1>
      <div class="final-score"><strong>{{ score }}</strong><span>/ {{ maxScore }} colors matched</span></div>
      <p class="profile-title">{{ colorProfile.title }}</p><p class="intro result-intro">{{ colorProfile.note }} 四題結束後，你總共找到了 {{ score }} 個符合情境的顏色方向。</p>
      <div class="answer-list"><article v-for="(result, index) in results" :key="index" class="answer-row"><div class="answer-number">0{{ index + 1 }}</div><div class="answer-copy"><strong>{{ questions[index].scene }}</strong><p>{{ questions[index].explanation }}</p></div><div class="mini-palette"><i v-for="colorId in result.selected" :key="colorId" :style="{ backgroundColor: questions[index].options.find((option) => option.id === colorId)?.hex }"></i></div><span class="match-count">{{ result.matched.length }}/3</span></article></div>
      <button class="primary-button restart" type="button" @click="restartGame">再玩一次 <b>↗</b></button>
    </section>
    <footer><span>Four scenes · Twelve color directions</span><span>Trust your eye</span></footer>
  </main>
</template>
