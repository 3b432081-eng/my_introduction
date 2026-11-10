<script setup>
import { ref, computed, onMounted } from 'vue'
import ProjectCard from './components/ProjectCard.vue'
import Common from './components/common.vue'

// =========================
// 作品資料
// =========================
const projects = [
  {
    id: 1,
    tag: 'html撰寫',
    title: '自我介紹網頁',
    content: '使用 AI 建構出架構，再由我修改細節'
  },
  {
    id: 2,
    tag: '',
    title: '無',
    content: ''
  },
  {
    id: 3,
    tag: '',
    title: '無',
    content: ''
  }
]

// =========================
// 聯絡表單
// =========================
const name = ref('')
const email = ref('')
const emailError = ref(false)
const submitted = ref(false)
const successMessage = ref(false)
const currentYear = new Date().getFullYear()

function validateEmail() {
  if (email.value.trim() === '') {
    emailError.value = false
    return
  }

  const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  emailError.value = !emailPattern.test(email.value)
}

function submitForm() {
  validateEmail()

  if (!name.value.trim() || !email.value.trim() || emailError.value) {
    return
  }

  successMessage.value = true
  submitted.value = true
}

// =========================
// YouBike 即時資料
// =========================
const apiUrl =
  'https://newdatacenter.taichung.gov.tw/api/v1/no-auth/resource.download?rid=bf0fee92-9980-418c-a095-2eacf2eb49f6'

const stations = ref([])
const keyword = ref('勤益')
const appliedKeyword = ref('勤益')

const sortType = ref('name')
const onlyAvailable = ref(false)

const loading = ref(false)
const errorMessage = ref('')
const hasLoaded = ref(false)

// 將 API 回傳的數字字串轉成數字
function toNumber(value) {
  const result = Number(value)
  return Number.isFinite(result) ? result : 0
}

// 格式化更新時間
function formatTime(value) {
  if (!value) return '無資料'

  const time = String(value)

  if (!/^\d{14}$/.test(time)) {
    return time
  }

  return `${time.slice(0, 4)}/${time.slice(4, 6)}/${time.slice(6, 8)} ${time.slice(8, 10)}:${time.slice(10, 12)}:${time.slice(12, 14)}`
}

// 從 API 回傳內容取得站點陣列
function getStationArray(payload) {
  if (Array.isArray(payload)) return payload

  if (Array.isArray(payload.data)) return payload.data
  if (Array.isArray(payload.result)) return payload.result
  if (Array.isArray(payload.records)) return payload.records
  if (Array.isArray(payload.retVal)) return payload.retVal

  // 某些 API 會使用物件包裝站點資料
  if (payload.retVal && typeof payload.retVal === 'object') {
    return Object.values(payload.retVal)
  }

  return []
}

// 重新抓取 YouBike 資料
async function loadYouBikeData() {
  if (loading.value) return

  loading.value = true
  errorMessage.value = ''

  try {
    const response = await fetch(apiUrl, {
      cache: 'no-store'
    })

    if (!response.ok) {
      throw new Error(`HTTP 錯誤：${response.status}`)
    }

    const result = await response.json()
    const data = getStationArray(result)

    if (data.length === 0) {
      throw new Error('API 沒有回傳有效的站點資料')
    }

    stations.value = data.map((station, index) => ({
      id: station.sno || `${station.sna}-${index}`,

      // 移除 YouBike2.0_ 前綴
      name: String(station.sna || '')
        .replace(/^YouBike2\.0_/, ''),

      area: station.sarea || '',
      address: station.ar || '',

      total: toNumber(station.tot),
      bikes: toNumber(station.sbi),
      empty: toNumber(station.bemp),

      updateTime: formatTime(station.mday)
    }))

    hasLoaded.value = true

  } catch (error) {
    console.error('YouBike 資料載入失敗：', error)

    errorMessage.value =
      '資料載入失敗，請稍後再試。若持續發生，可能是 API 連線或跨來源存取（CORS）限制。'

  } finally {
    loading.value = false
  }
}

// 按下搜尋按鈕
function searchStations() {
  appliedKeyword.value = keyword.value.trim()
}

// 計算篩選、排序後的站點
const filteredStations = computed(() => {
  let result = [...stations.value]

  // 關鍵字搜尋
  if (appliedKeyword.value) {
    result = result.filter(station =>
      station.name.includes(appliedKeyword.value) ||
      station.area.includes(appliedKeyword.value) ||
      station.address.includes(appliedKeyword.value)
    )
  }

  // 只看有車可借
  if (onlyAvailable.value) {
    result = result.filter(station => station.bikes > 0)
  }

  // 排序
  if (sortType.value === 'name') {
    result.sort((a, b) => a.name.localeCompare(b.name, 'zh-TW'))
  } else if (sortType.value === 'bikes') {
    result.sort((a, b) => b.bikes - a.bikes)
  }

  // 最多顯示 12 個站點
  return result.slice(0, 12)
})

// 網頁載入時自動取得資料
onMounted(() => {
  loadYouBikeData()
})
</script>

<template>
  <header>
    <div class="container">
      <nav>
        <a class="logo" href="#home">主頁</a>

        <ul class="nav-links">
          <li><a href="#about">關於我</a></li>
          <li><a href="#projects">我的作品</a></li>
          <li><a href="#youbike">YouBike</a></li>
          <li><a href="#contact">聯絡我</a></li>
        </ul>
      </nav>
    </div>
  </header>

  <main>
    <!-- 自我介紹 -->
    <section class="hero container" id="home">
      <div>
        <span class="eyebrow">自我介紹網頁</span>

        <h1>我是 <span class="accent">梁家豪</span></h1>

        <p>
          我是一位對科技、設計與創作充滿興趣的人。
          喜歡把想法轉化成實際作品，
          並持續學習新的工具與知識。
          這裡記錄了我的自我介紹、技能與作品。
        </p>

        <div class="hero-actions">
          <a class="btn btn-secondary" href="#contact">聯絡我</a>
        </div>
      </div>

      <div class="profile-card">
        <div class="profile-photo">
          <img src="/me.jpg" alt="我的個人照片" />
        </div>
      </div>
    </section>

    <!-- 關於我 -->
    <section id="about">
      <div class="container">
        <h2 class="section-title">關於我</h2>

        <p class="section-desc">
          我是一名大學生，目前正在勤益科技大學就讀，科系為資訊管理系。
        </p>

        <div class="about-grid">
          <Common
            title="我的故事"
            content="我喜歡研究新科技，也享受解決問題的過程。對我來說，每一個專案都是一次學習與成長的機會。"
          />

          <Common
            title="我的興趣"
            content="程式設計、網頁開發、音樂、看動漫，以及探索各種有趣的新事物。"
          />
        </div>
      </div>
    </section>

    <!-- 我的技能 -->
    <section id="skills">
      <div class="container">
        <h2 class="section-title">我的技能</h2>
        <p class="section-desc">程式設計。</p>

        <div class="card">
          <h3>Skills &amp; Tools</h3>

          <div class="skill-list">
            <span class="skill">HTML</span>
            <span class="skill">JavaScript</span>
            <span class="skill">C#</span>
          </div>
        </div>
      </div>
    </section>

    <!-- 我的作品 -->
    <section id="projects">
      <div class="container">
        <h2 class="section-title">我的作品</h2>

        <div class="cards">
          <ProjectCard
            v-for="project in projects"
            :key="project.id"
            :title="project.title"
            :content="project.content"
            :tag="project.tag"
          />
        </div>
      </div>
    </section>

    <!-- YouBike 即時車位 -->
    <section id="youbike">
      <div class="container">
        <h2 class="section-title">YouBike 即時車位</h2>

        <p class="section-desc">
          查詢臺中市 YouBike 2.0 站點的即時借車與還車資訊。
        </p>

        <div class="youbike-panel">

          <!-- 搜尋與重新抓取 -->
          <div class="youbike-controls">
            <input
              v-model="keyword"
              type="search"
              placeholder="請輸入站名關鍵字"
              @keyup.enter="searchStations"
            />

            <button
              class="btn"
              @click="searchStations"
              :disabled="loading"
            >
              搜尋
            </button>

            <button
              class="btn btn-secondary"
              @click="loadYouBikeData"
              :disabled="loading"
            >
              {{ loading ? '資料載入中...' : '↻ 重新抓取資料' }}
            </button>

            <select v-model="sortType">
              <option value="name">依站名排序</option>
              <option value="bikes">可借車輛多到少</option>
            </select>
          </div>

          <!-- 篩選 -->
          <div class="youbike-options">
            <label>
              <input
                v-model="onlyAvailable"
                type="checkbox"
              />
              只看有車可借
            </label>
          </div>

          <!-- 資料狀態 -->
          <p
            v-if="loading"
            class="youbike-status"
            role="status"
          >
            正在取得 YouBike 即時資料...
          </p>

          <p
            v-else-if="errorMessage"
            class="youbike-error"
            role="alert"
          >
            {{ errorMessage }}
          </p>

          <p
            v-else-if="hasLoaded && filteredStations.length === 0"
            class="youbike-status"
          >
            找不到符合條件的站點。
          </p>

          <p
            v-else-if="hasLoaded"
            class="youbike-status"
          >
            已取得資料，以下顯示最多 12 個符合條件的站點。
          </p>

          <p v-if="hasLoaded" class="youbike-result-count">
            顯示 {{ filteredStations.length }} 個站點
          </p>

          <!-- 站點卡片 -->
          <div class="youbike-grid">
            <article
              v-for="station in filteredStations"
              :key="station.id"
              class="youbike-card"
            >
              <h3>{{ station.name }}</h3>

              <p class="youbike-area">
                {{ station.area }}
              </p>

              <p class="youbike-address">
                {{ station.address }}
              </p>

              <div class="youbike-bike-count">
                <span>可借車輛</span>

                <strong
                  :class="{ 'zero-bikes': station.bikes === 0 }"
                >
                  {{ station.bikes }}
                </strong>
              </div>

              <div class="youbike-info">
                <p>
                  可還空位：
                  <strong>{{ station.empty }}</strong>
                </p>

                <p>
                  總停車格：
                  <strong>{{ station.total }}</strong>
                </p>
              </div>

              <p class="youbike-time">
                更新時間：{{ station.updateTime }}
              </p>
            </article>
          </div>

          <!-- 資料來源 -->
          <p class="youbike-source">
            資料來源：臺中市政府交通局，政府資料開放平臺。
            資料依政府資料開放授權條款第 1 版提供。
          </p>
        </div>
      </div>
    </section>

    <!-- 聯絡我 -->
    <section id="contact">
      <div class="container">
        <article class="contact-box">
          <h2>想和我聯絡嗎？</h2>

          <p>
            歡迎留下您的姓名與 Email，
            我會透過 Email 與您聯絡。
          </p>

          <form
            class="contact-form"
            :class="{ submitted }"
            @submit.prevent="submitForm"
            novalidate
          >
            <div class="form-group">
              <label for="name">姓名：</label>

              <input
                id="name"
                v-model="name"
                type="text"
                name="name"
                placeholder="請輸入您的姓名"
                required
                :disabled="submitted"
              />
            </div>

            <div class="form-group">
              <label for="email">Email：</label>

              <input
                id="email"
                v-model="email"
                type="email"
                name="email"
                placeholder="請輸入您的 Email"
                required
                :disabled="submitted"
                @input="validateEmail"
              />

              <span v-if="emailError" class="email-error">
                Email 格式不正確
              </span>
            </div>

            <button
              type="submit"
              class="btn"
              :disabled="submitted"
            >
              送出
            </button>

            <p v-if="successMessage" class="success-message">
              ✅ 表單送出成功！謝謝您的聯絡。
            </p>
          </form>
        </article>
      </div>
    </section>
  </main>

  <footer>
    © {{ currentYear }} 梁家豪. All rights reserved.
  </footer>
</template>
