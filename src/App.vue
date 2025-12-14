<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
// 导入公告数据
import announcementsData from './data/announcements.json';
// 导入特色玩法数据
import featuresData from './data/features.json';
// 导入照片数据
import photosData from './data/photos.json';

// 深色模式切换逻辑
const isDarkMode = ref(false);

// 滚动监听逻辑
const isScrolled = ref(false);
const showScrollTop = ref(false);

// 移动端菜单控制
const isMenuOpen = ref(false);

// 公告数据
const announcements = ref(announcementsData);
// 特色玩法数据
const features = ref(featuresData);
// 照片数据
const photos = ref(photosData);

// 初始化主题
const initTheme = () => {
  // 从localStorage获取主题设置
  const savedTheme = localStorage.getItem('theme');
  if (savedTheme) {
    isDarkMode.value = savedTheme === 'dark';
    document.documentElement.setAttribute('data-theme', savedTheme);
  } else {
    // 检测系统偏好
    const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
    isDarkMode.value = prefersDark;
    document.documentElement.setAttribute('data-theme', prefersDark ? 'dark' : 'light');
  }
};

// 切换主题
const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value;
  const newTheme = isDarkMode.value ? 'dark' : 'light';
  document.documentElement.setAttribute('data-theme', newTheme);
  localStorage.setItem('theme', newTheme);
};

const handleScroll = () => {
  const scrollPosition = window.scrollY;
  isScrolled.value = scrollPosition > 100;
  showScrollTop.value = scrollPosition > 300;
};

const scrollToTop = () => {
  window.scrollTo({
    top: 0,
    behavior: 'smooth'
  });
};

onMounted(() => {
  initTheme();
  // 按照日期从新到旧排序公告
  announcements.value.sort((a, b) => {
    return new Date(b.date).getTime() - new Date(a.date).getTime();
  });
  window.addEventListener('scroll', handleScroll);
  // 监听系统主题变化
  window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
    if (!localStorage.getItem('theme')) {
      isDarkMode.value = e.matches;
      document.documentElement.setAttribute('data-theme', e.matches ? 'dark' : 'light');
    }
  });
});

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll);
});
</script>

<template>
  <div id="app">
    <!-- 顶部导航栏 -->
    <header class="header" :class="{ scrolled: isScrolled }">
      <div class="container">
        <nav class="navbar">
          <div class="navbar-brand">
            <img src="https://file.fis.ink/img/fishcpy/logo_c.png" alt="fishcpy logo" class="logo" />
            <span class="site-name">fishcpy mc</span>
          </div>
          <div class="navbar-actions">
          <!-- 汉堡菜单按钮 -->
          <button class="menu-toggle" @click="isMenuOpen = !isMenuOpen" aria-label="切换菜单">
            <span class="menu-icon"></span>
          </button>
          <ul class="navbar-nav" :class="{ 'menu-open': isMenuOpen }">
            <li class="nav-item"><a href="#" class="nav-link" @click="isMenuOpen = false">首页</a></li>
            <li class="nav-item"><a href="#about" class="nav-link" @click="isMenuOpen = false">关于我们</a></li>
            <li class="nav-item"><a href="#features" class="nav-link" @click="isMenuOpen = false">特色玩法</a></li>
            <li class="nav-item"><a href="#photos" class="nav-link" @click="isMenuOpen = false">服务器照片</a></li>
            <li class="nav-item"><a href="#announcements" class="nav-link" @click="isMenuOpen = false">最新公告</a></li>
            <li class="nav-item"><a href="#contact" class="nav-link" @click="isMenuOpen = false">联系我们</a></li>
          </ul>
          <!-- 深色模式切换按钮 -->
          <button class="theme-toggle" @click="toggleTheme" aria-label="切换主题">
            {{ isDarkMode ? '🌞' : '🌙' }}
          </button>
        </div>
        </nav>
      </div>
    </header>

    <!-- 主要内容区域 -->
    <main class="main">
      <!-- 英雄区域 -->
      <section class="hero">
        <div class="container">
          <h1 class="hero-title">欢迎来到 fishcpy mc</h1>
          <p class="hero-subtitle">探索无限可能，创造属于你的世界</p>
          <div class="cta-container">
            <a href="#contact" class="cta-button">立即加入</a>
          </div>
        </div>
      </section>

      <!-- 服务器简介 -->
      <section id="about" class="about">
        <div class="container">
          <h2>服务器简介</h2>
          <div class="card fade-in">
            <p>简介这一块</p>
          </div>
        </div>
      </section>

      <!-- 特色玩法 -->
      <section id="features" class="features">
        <div class="container">
          <h2>特色玩法</h2>
          <div class="features-grid">
            <div 
              v-for="(feature, index) in features" 
              :key="index" 
              class="card feature-card fade-in"
              :style="{ animationDelay: `${0.1 * (index + 1)}s` }"
            >
              <h3>{{ feature.title }}</h3>
              <p>{{ feature.content }}</p>
            </div>
          </div>
        </div>
      </section>

      <!-- 服务器照片展示 -->
      <section id="photos" class="photos">
        <div class="container">
          <h2>服务器照片</h2>
          <div class="photos-grid">
            <div 
              v-for="(photo, index) in photos" 
              :key="photo.id" 
              class="card photo-card fade-in"
              :style="{ animationDelay: `${0.1 * (index + 1)}s` }"
            >
              <div class="photo-image-container">
                <img :src="photo.imageUrl" :alt="photo.title" class="photo-image" />
              </div>
              <div class="photo-content">
                <h3>{{ photo.title }}</h3>
                <p class="photo-date">{{ photo.date }}</p>
                <p>{{ photo.description }}</p>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- 最新公告 -->
      <section id="announcements" class="announcements">
        <div class="container">
          <h2>最新公告</h2>
          <div 
            v-for="(announcement, index) in announcements" 
            :key="index" 
            class="card fade-in"
            :style="{ animationDelay: `${0.1 * (index + 1)}s` }"
          >
            <h3>{{ announcement.title }}</h3>
            <p class="announcement-date">{{ announcement.date }}</p>
            <p>{{ announcement.content }}</p>
          </div>
        </div>
      </section>

      <!-- QQ群信息 -->
      <section id="contact" class="qq-group">
        <div class="container">
          <h2>加入我们的QQ群</h2>
          <div class="card qq-card fade-in">
            <div class="qq-info">
              <h3>fishcpy mc 官方交流群</h3>
              <p class="qq-number">群号：1011484183</p>
              <p>加入QQ群。</p>
            </div>
            <a href="https://qm.qq.com/q/kUNYWdUBbi" target="_blank" rel="noopener noreferrer" class="qq-button cta-button">立即加入</a>
          </div>
        </div>
      </section>
    </main>

    <!-- 页脚 -->
    <footer class="footer">
      <div class="container">
        <p>&copy; 2025 fishcpy cloud. All rights reserved.</p>
      </div>
    </footer>

    <!-- 滚动到顶部按钮 -->
    <button class="scroll-top" :class="{ visible: showScrollTop }" @click="scrollToTop" aria-label="滚动到顶部">
      ↑
    </button>
  </div>
</template>

<style scoped>
/* 顶部导航栏样式 */
.header {
  background-color: var(--header-bg);
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  position: sticky;
  top: 0;
  z-index: 1000;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  transition: all 0.3s ease;
  width: 100%;
}

.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 0;
  width: 100%;
  margin: 0;
}

.navbar-brand {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin: 0;
  padding: 0;
}

.logo {
  height: 3rem;
  width: auto;
  margin: 0;
  padding: 0;
}

.site-name {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--primary-color);
  margin: 0;
  padding: 0;
}

/* 导航栏操作区域 */
.navbar-actions {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin: 0;
  padding: 0;
}

.navbar-nav {
  display: flex;
  list-style: none;
  gap: 2rem;
  margin: 0;
  padding: 0;
  align-items: center;
}

.nav-link {
  color: var(--text-color);
  font-weight: 500;
  transition: all 0.3s ease;
  margin: 0;
  padding: 0.5rem 0;
  display: inline-block;
}

.nav-link:hover {
  color: var(--primary-color);
  text-decoration: none;
}

/* 主题切换按钮 */
.theme-toggle {
  background-color: var(--bg-dark);
  color: var(--text-color);
  width: 2.5rem;
  height: 2.5rem;
  padding: 0;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.2rem;
}

.theme-toggle:hover {
  background-color: var(--border-color);
  transform: rotate(180deg);
}

/* 汉堡菜单按钮样式 */
.menu-toggle {
  background-color: transparent;
  border: none;
  cursor: pointer;
  display: none;
  flex-direction: column;
  justify-content: space-around;
  width: 2rem;
  height: 2rem;
  padding: 0;
  z-index: 1100;
}

.menu-icon {
  display: block;
  width: 2rem;
  height: 0.25rem;
  background-color: var(--text-color);
  border-radius: 1rem;
  transition: all 0.3s ease;
  position: relative;
}

.menu-icon::before,
.menu-icon::after {
  content: '';
  display: block;
  width: 2rem;
  height: 0.25rem;
  background-color: var(--text-color);
  border-radius: 1rem;
  transition: all 0.3s ease;
  position: absolute;
  left: 0;
}

.menu-icon::before {
  top: -0.75rem;
}

.menu-icon::after {
  top: 0.75rem;
}

/* 汉堡菜单打开状态 */
.menu-toggle[aria-expanded="true"] .menu-icon {
  background-color: transparent;
}

.menu-toggle[aria-expanded="true"] .menu-icon::before {
  transform: rotate(45deg);
  top: 0;
}

.menu-toggle[aria-expanded="true"] .menu-icon::after {
  transform: rotate(-45deg);
  top: 0;
}

/* 英雄区域样式 - 全屏大标题 */
.hero {
  background-color: var(--hero-bg);
  color: var(--text-color);
  padding: 0;
  text-align: center;
  position: relative;
  overflow: hidden;
  height: 100vh;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  left: 0;
  top: 0;
  z-index: 1;
  margin: 0;
}

/* 确保main标签没有默认的margin或padding */
main.main {
  margin: 0;
  padding: 0;
  width: 100%;
}

/* 确保body没有默认的margin */
body {
  margin: 0;
  padding: 0;
}

.hero::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image: 
    linear-gradient(var(--grid-color) 1px, transparent 1px),
    linear-gradient(90deg, var(--grid-color) 1px, transparent 1px);
  background-size: 40px 40px;
  background-position: 0 0;
  opacity: 0.5;
  pointer-events: none;
  z-index: -1;
}

.hero > .container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  z-index: 2;
}

.hero-title {
  font-size: clamp(3rem, 8vw, 6rem);
  margin-bottom: 1.5rem;
  color: var(--primary-color);
  position: relative;
  z-index: 1;
  font-weight: 700;
  line-height: 1.1;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
  width: 100%;
  text-align: center;
}

.hero-subtitle {
  font-size: clamp(1rem, 3vw, 1.5rem);
  margin-bottom: 2.5rem;
  color: var(--text-light);
  position: relative;
  z-index: 1;
  max-width: 800px;
  padding: 0;
  width: 100%;
  text-align: center;
}

/* 立即加入按钮容器，用于居中 */
.cta-container {
  display: flex;
  justify-content: center;
  width: 100%;
  position: relative;
  z-index: 1;
  margin: 0;
  padding: 0;
}

/* 立即加入按钮样式 */
.cta-button {
  font-size: clamp(1rem, 2vw, 1.25rem);
  padding: 0.8em 2em;
  background-color: var(--primary-color);
  color: white;
  border-radius: 4px;
  position: relative;
  z-index: 1;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  display: inline-block;
  text-decoration: none;
  border: none;
  cursor: pointer;
  font-family: inherit;
  font-weight: 500;
  transition: all 0.3s ease;
}

.cta-button:hover {
  background-color: var(--primary-dark);
  transform: translateY(-2px) scale(1.05);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.2);
  text-decoration: none;
}

.cta-button:focus,
.cta-button:focus-visible {
  outline: 2px solid var(--primary-dark);
  outline-offset: 2px;
}

/* 主要内容区域样式 */
.main {
  padding: 2rem 0;
  position: relative;
  width: 100%;
  margin: 0 auto;
}

section {
  margin-bottom: 4rem;
  position: relative;
  width: 100%;
  margin-left: auto;
  margin-right: auto;
}

section h2 {
  text-align: center;
  margin-bottom: 2rem;
  color: var(--text-color);
}

/* 卡片样式 - 应用磨玻璃效果 */
.card {
  background-color: var(--card-bg);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid var(--border-color);
}

/* 特色玩法网格 */
.features-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  width: 100%;
  margin: 0 auto;
}

/* 确保所有容器内的内容都正确居中 */
.container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 2rem;
  box-sizing: border-box;
}

.feature-card {
  height: 100%;
  display: flex;
  flex-direction: column;
}

/* 服务器照片样式 */
.photos-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 2rem;
  width: 100%;
  margin: 0 auto;
}

.photo-card {
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.photo-image-container {
  width: 100%;
  height: 250px;
  overflow: hidden;
  margin-bottom: 1rem;
  border-radius: 4px;
}

.photo-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.photo-card:hover .photo-image {
  transform: scale(1.05);
}

.photo-content {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.photo-date {
  font-size: 0.9rem;
  color: var(--text-light);
  margin-bottom: 0.5rem;
  font-style: italic;
}

/* 响应式照片布局 */
@media (max-width: 768px) {
  .photos-grid {
    grid-template-columns: 1fr;
  }
  
  .photo-image-container {
    height: 200px;
  }
}

/* 公告样式 */
.announcement-date {
  font-size: 0.9rem;
  color: var(--text-light);
  margin-bottom: 1rem;
  font-style: italic;
}

/* QQ群信息样式 */
.qq-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  gap: 1.5rem;
}

.qq-info {
  max-width: 600px;
}

.qq-number {
  font-size: 1.25rem;
  font-weight: 600;
  color: var(--qq-color);
  margin-bottom: 1rem;
}

/* 页脚样式 */
.footer {
  background-color: var(--footer-bg);
  color: var(--text-light);
  padding: 2rem 0;
  text-align: center;
  margin-top: 4rem;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border-top: 1px solid var(--border-color);
}

/* 滚动到顶部按钮 */
.scroll-top {
  background-color: var(--bg-dark);
  color: var(--text-color);
  border: 1px solid var(--border-color);
}

.scroll-top:hover {
  background-color: var(--border-color);
}

/* 响应式设计 */
@media (max-width: 768px) {
  .navbar {
    flex-direction: row;
    justify-content: space-between;
    gap: 1rem;
  }
  
  .navbar-actions {
    flex-direction: row;
    gap: 1rem;
    width: auto;
    justify-content: flex-end;
  }
  
  /* 显示汉堡菜单按钮 */
  .menu-toggle {
    display: flex;
  }
  
  /* 隐藏桌面端导航菜单 */
  .navbar-nav {
    position: fixed;
    top: 6rem;
    left: 0;
    width: 100%;
    background-color: var(--header-bg);
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 2rem 0;
    gap: 1.5rem;
    box-shadow: 0 10px 15px rgba(0, 0, 0, 0.1);
    transform: translateY(-150%);
    opacity: 0;
    visibility: hidden;
    transition: all 0.3s ease;
    z-index: 1050;
  }
  
  /* 导航菜单打开状态 */
  .navbar-nav.menu-open {
    transform: translateY(0);
    opacity: 1;
    visibility: visible;
  }
  
  .nav-link {
    font-size: 1.2rem;
    padding: 1rem 0;
  }
  
  .hero-title {
    font-size: 2rem;
  }
  
  .hero-subtitle {
    font-size: 1rem;
  }
  
  .features-grid {
    grid-template-columns: 1fr;
  }
}

/* 动画延迟 */
.fade-in {
  opacity: 0;
}

.fade-in:nth-child(1) {
  animation-delay: 0.1s;
}

.fade-in:nth-child(2) {
  animation-delay: 0.2s;
}

.fade-in:nth-child(3) {
  animation-delay: 0.3s;
}

.fade-in:nth-child(4) {
  animation-delay: 0.4s;
}

/* 导航栏滚动效果 */
.header.scrolled {
  box-shadow: 0 2px 15px rgba(0, 0, 0, 0.15);
  background-color: var(--header-bg);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  transition: all 0.3s ease;
}

/* 平滑滚动到锚点 */
html {
  scroll-behavior: smooth;
}

/* 滚动到顶部按钮 */
.scroll-top {
  position: fixed;
  bottom: 2rem;
  right: 2rem;
  width: 3rem;
  height: 3rem;
  border-radius: 50%;
  background-color: var(--primary-color);
  color: white;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transition: all 0.3s ease;
  opacity: 0;
  visibility: hidden;
  z-index: 999;
}

.scroll-top.visible {
  opacity: 1;
  visibility: visible;
  transform: translateY(0);
}

.scroll-top:hover {
  background-color: var(--primary-dark);
  transform: translateY(-2px) scale(1.05);
}

/* 导航链接的悬停效果 */
.nav-link {
  position: relative;
  padding: 0.5rem 0;
}

.nav-link::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 0;
  height: 2px;
  background-color: var(--primary-color);
  transition: width 0.3s ease;
}

.nav-link:hover::after {
  width: 100%;
}

/* 卡片的悬停缩放效果 */
.card {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.card:hover {
  transform: translateY(-4px) scale(1.02);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

/* 移除按钮的波纹效果 */
button {
  position: relative;
  overflow: visible;
}
</style>
