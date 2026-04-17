<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue';

// 狀態變數：控制切換按鈕
const isCountdown = ref(false);
const showZhuyin = ref(false);
const isDarkMode = ref(false);

// 時間相關變數
const currentTime = ref('');
const countdownTime = ref(3600); // 預設倒數 60 分鐘 (3600秒)
let timerInterval = null;
let countdownInterval = null;

// 監考資料表單變數
const inputTime = ref('');
const inputSubject = ref('');
const inputTeacher = ref('');

// 更新現在時間
const updateCurrentTime = () => {
  const now = new Date();
  currentTime.value = now.toLocaleTimeString('zh-TW');
};

// 計算倒數計時格式 (HH:MM:SS)
const formattedCountdown = computed(() => {
  const hours = Math.floor(countdownTime.value / 3600);
  const minutes = Math.floor((countdownTime.value % 3600) / 60);
  const seconds = countdownTime.value % 60;
  return `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
});

// 切換時間/倒數
const toggleTimeDisplay = () => {
  isCountdown.value = !isCountdown.value;
};

// 切換注音顯示
const toggleZhuyinMode = () => {
  showZhuyin.value = !showZhuyin.value;
};

// 切換深淺色主題 (黑底白字/白底黑字)
const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value;
};

// 開啟淡江教科系網頁
const goToTkuEt = () => {
  window.open('https://www.et.tku.edu.tw/', '_blank');
};

// 生命週期：掛載時啟動計時器
onMounted(() => {
  updateCurrentTime();
  timerInterval = setInterval(updateCurrentTime, 1000);
  
  countdownInterval = setInterval(() => {
    if (countdownTime.value > 0) {
      countdownTime.value--;
    }
  }, 1000);
});

// 生命週期：卸載時清除計時器避免記憶體流失
onUnmounted(() => {
  clearInterval(timerInterval);
  clearInterval(countdownInterval);
});
</script>

<template>
  <div :class="['app-wrapper', { 'dark-mode': isDarkMode, 'zhuyin-mode': showZhuyin }]">
    <header class="header">
      <!-- 標題左側按鈕 -->
      <button class="tku-btn" @click="goToTkuEt">
        <ruby>淡<rt>ㄉㄢˋ</rt>江<rt>ㄐㄧㄤ</rt>教<rt>ㄐㄧㄠˋ</rt>科<rt>ㄎㄜ</rt>系<rt>ㄒㄧˋ</rt></ruby>
      </button>
      <!-- 中間標題文字 -->
      <h1 class="title">
        <ruby>互<rt>ㄏㄨˋ</rt>動<rt>ㄉㄨㄥˋ</rt>程<rt>ㄔㄥˊ</rt>式<rt>ㄕˋ</rt>設<rt>ㄕㄜˋ</rt>計<rt>ㄐㄧˋ</rt></ruby>_413730267
      </h1>
      <div class="spacer"></div>
    </header>

    <main class="main-content">
      <!-- 三個小工具按鈕 -->
      <div class="toolbar">
        <button @click="toggleTimeDisplay">切換 倒數計時/現在時間</button>
        <button @click="toggleZhuyinMode">切換 注音符號/無注音</button>
        <button @click="toggleTheme">切換 黑底白字/白底黑字</button>
      </div>

      <!-- 時間顯示區 -->
      <div class="time-display">
        <h2>{{ isCountdown ? '倒數計時' : '現在時間' }}</h2>
        <div class="time-text">{{ isCountdown ? formattedCountdown : currentTime }}</div>
      </div>

      <!-- 資料輸入區 -->
      <div class="input-section">
        <h3>監考資料輸入</h3>
        <div class="input-group">
          <label>時間：</label>
          <input v-model="inputTime" type="text" placeholder="例如: 10:00 - 12:00" />
        </div>
        <div class="input-group">
          <label>科目：</label>
          <input v-model="inputSubject" type="text" placeholder="例如: 互動程式設計" />
        </div>
        <div class="input-group">
          <label>監考老師：</label>
          <input v-model="inputTeacher" type="text" placeholder="輸入老師姓名" />
        </div>
      </div>

      <!-- 資料顯示區 -->
      <div class="display-section">
        <h3>考場資訊</h3>
        <p><strong>時間：</strong> {{ inputTime || '尚未輸入' }}</p>
        <p><strong>科目：</strong> {{ inputSubject || '尚未輸入' }}</p>
        <p><strong>監考老師：</strong> {{ inputTeacher || '尚未輸入' }}</p>
      </div>
    </main>
  </div>
</template>

<style scoped>
/* === 基礎排版與轉場動畫 (簡約風) === */
.app-wrapper {
  min-height: 100vh;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  transition: background-color 0.4s ease, color 0.4s ease;
  background-color: #f7f9fc; /* 柔和的淺灰底色 */
  color: #2d3748; /* 深灰字體，降低視覺疲勞 */
  padding: 40px 20px;
  box-sizing: border-box;
}

/* === 注音模式控制 === */
.app-wrapper:not(.zhuyin-mode) rt {
  display: none;
}

/* === 深色模式(黑底白字) CSS === */
.app-wrapper.dark-mode {
  background-color: #1a202c; /* 質感深藍黑 */
  color: #f7fafc;
}

/* === Header 頂部排版 === */
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  max-width: 900px;
  margin: 0 auto 40px auto;
  padding-bottom: 20px;
  border-bottom: 1px solid #e2e8f0;
  transition: border-color 0.4s ease;
}
.app-wrapper.dark-mode .header {
  border-bottom-color: #4a5568;
}
.title {
  margin: 0;
  font-size: 1.8rem;
  font-weight: 600;
  text-align: center;
  flex-grow: 1;
  letter-spacing: 1px;
}
.tku-btn { flex-shrink: 0; }
.spacer { width: 100px; } /* 用於平衡左側按鈕的空間，保持標題置中 */

/* === 主區域 === */
.main-content {
  max-width: 800px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: 25px;
}

/* === 共用卡片樣式 (微陰影、圓角、留白) === */
.time-display, .input-section, .display-section {
  background: #ffffff;
  padding: 30px 40px;
  border-radius: 16px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.03);
  transition: background-color 0.4s ease, box-shadow 0.4s ease;
}
.app-wrapper.dark-mode .time-display,
.app-wrapper.dark-mode .input-section,
.app-wrapper.dark-mode .display-section {
  background: #2d3748;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
}

/* === 工具列按鈕 === */
.toolbar {
  display: flex;
  gap: 12px;
  justify-content: center;
  flex-wrap: wrap;
}

/* 按鈕美化 (無邊框、扁平化+微陰影) */
button {
  padding: 10px 18px;
  cursor: pointer;
  border: none;
  border-radius: 8px;
  background-color: #edf2f7;
  color: #4a5568;
  font-size: 0.95rem;
  font-weight: 500;
  box-shadow: 0 2px 5px rgba(0,0,0,0.05);
  transition: all 0.2s;
}
button:hover {
  background-color: #e2e8f0;
  transform: translateY(-2px);
  box-shadow: 0 4px 10px rgba(0,0,0,0.08);
}
button:active {
  transform: translateY(0);
}
.app-wrapper.dark-mode button {
  background-color: #4a5568;
  color: #f7fafc;
  box-shadow: 0 2px 5px rgba(0,0,0,0.2);
}
.app-wrapper.dark-mode button:hover {
  background-color: #718096;
}

/* === 時間顯示區塊 === */
.time-display {
  text-align: center;
}
.time-display h2 { 
  margin: 0 0 10px 0;
  font-size: 1.2rem;
  font-weight: 500;
  color: #718096;
}
.app-wrapper.dark-mode .time-display h2 {
  color: #a0aec0;
}
.time-text {
  font-size: 4rem;
  font-weight: 700;
  font-variant-numeric: tabular-nums;
  letter-spacing: 2px;
  color: #2b6cb0; /* 簡約中的一點主題色 (藍色) */
}
.app-wrapper.dark-mode .time-text {
  color: #90cdf4;
}

/* === 標題設計 === */
h3 {
  margin-top: 0;
  margin-bottom: 20px;
  font-size: 1.4rem;
  font-weight: 600;
  border-left: 4px solid #3182ce;
  padding-left: 10px;
}
.app-wrapper.dark-mode h3 {
  border-left-color: #63b3ed;
}

/* === 表單與結果區域 === */
.input-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}
.input-group label {
  margin-bottom: 8px;
  font-weight: 600;
  font-size: 0.95rem;
}
input {
  padding: 14px 16px;
  font-size: 1rem;
  border: 1px solid #e2e8f0;
  border-radius: 10px;
  background-color: #f7f9fc;
  transition: all 0.2s ease;
  outline: none;
  color: #2d3748;
}
input:focus {
  border-color: #3182ce;
  background-color: #ffffff;
  box-shadow: 0 0 0 3px rgba(49, 130, 206, 0.1);
}
.app-wrapper.dark-mode input {
  background-color: #1a202c;
  border-color: #4a5568;
  color: #f7fafc;
}
.app-wrapper.dark-mode input:focus {
  border-color: #63b3ed;
  background-color: #2d3748;
  box-shadow: 0 0 0 3px rgba(99, 179, 237, 0.2);
}

.display-section p {
  margin: 10px 0;
  font-size: 1.1rem;
  line-height: 1.6;
}

/* === 響應式：手機版調整 === */
@media (max-width: 600px) {
  .app-wrapper { padding: 20px 15px; }
  .header {
    flex-direction: column;
    gap: 15px;
    border-bottom: none;
    margin-bottom: 20px;
  }
  .spacer { display: none; }
  .toolbar { flex-direction: column; }
  .time-text { font-size: 3rem; }
  .time-display, .input-section, .display-section {
    padding: 20px;
  }
}
</style>
