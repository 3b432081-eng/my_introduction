<script setup>
import { ref } from 'vue'
import ProjectCard from './components/ProjectCard.vue'

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
</script>

<template>
  <header>
    <div class="container">
      <nav>
        <a class="logo" href="#home">主頁</a>
        <ul class="nav-links">
          <li><a href="#about">關於我</a></li>
          <li><a href="#contact">聯絡我</a></li>
        </ul>
      </nav>
    </div>
  </header>

  <main>
    <section class="hero container" id="home">
      <div>
        <span class="eyebrow">自我介紹網頁</span>
        <h1>我是 <span class="accent">梁家豪</span></h1>
        <p>
          我是一位對科技、設計與創作充滿興趣的人。喜歡把想法轉化成實際作品，
          並持續學習新的工具與知識。這裡記錄了我的自我介紹、技能與作品。
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

    <section id="about">
      <div class="container">
        <h2 class="section-title">關於我</h2>
        <p class="section-desc">
          我是一名大學生，目前正在勤益科技大學就讀，科系為資訊管理系。
        </p>

        <div class="about-grid">
          <div class="card">
            <h3>我的故事</h3>
            <p>
              我喜歡研究新科技，也享受解決問題的過程。
              對我來說，每一個專案都是一次學習與成長的機會。
            </p>
          </div>
          <div class="card">
            <h3>我的興趣</h3>
            <p>
              程式設計、網頁開發、音樂、看動漫，以及探索各種有趣的新事物。
            </p>
          </div>
        </div>
      </div>
    </section>

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

            <button type="submit" class="btn" :disabled="submitted">送出</button>

            <p v-if="successMessage" class="success-message">
              ✅ 表單送出成功！謝謝您的聯絡。
            </p>
          </form>
        </article>
      </div>
    </section>
  </main>

  <footer>
    © {{ currentYear }} 你的名字. All rights reserved.
  </footer>
</template>
