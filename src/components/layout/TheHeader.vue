<template>
  <div>
    <!-- 添加占位元素，防止内容被固定导航栏遮挡 -->
    <div class="h-[100px]"></div>

    <!-- 添加 fixed 定位和背景模糊效果 -->
    <header
      class="fixed top-0 left-0 right-0 z-50 bg-white/95 backdrop-blur-md shadow-md transition-all duration-300"
    >
      <nav class="container mx-auto px-4 py-2">
        <div class="flex items-center justify-between">
          <!-- 左侧 Logo 和标题区域 -->
          <div class="flex items-center space-x-6">
            <!-- Logo 放大到 20 -->
            <div class="w-20 h-20 rounded-full overflow-hidden shadow-lg">
              <img
                src="@/assets/images/logo.png"
                alt="凤翔泥塑"
                class="w-full h-full object-cover"
              />
            </div>
            <!-- 标题区域 -->
            <div class="flex flex-col">
              <h1
                class="text-2xl font-bold text-primary"
                style="font-family: 'STKaiti', 'KaiTi', serif"
              >
                陕西·宝鸡·凤翔
              </h1>
              <div
                class="text-base text-gray-600"
                style="font-family: 'STSong', 'SimSun', serif"
              >
                非物质文化遗产传承基地
              </div>
            </div>
          </div>

          <!-- 中间导航区域 - 使用 Element Plus 菜单 -->
          <div class="hidden lg:flex items-center space-x-6">
            <router-link to="/" class="nav-link">首页</router-link>

            <!-- 历史文化下拉菜单 -->
            <el-dropdown trigger="hover" @command="handleCommand">
              <router-link to="/history" class="nav-link flex items-center">
                历史文化
                <el-icon
                  class="ml-1 text-sm transition-transform group-hover:rotate-180"
                >
                  <ArrowDown />
                </el-icon>
              </router-link>
              <template #dropdown>
                <el-dropdown-menu class="custom-dropdown">
                  <el-dropdown-item command="/history#origin">
                    <div class="flex items-center py-1">
                      <el-icon class="mr-2"><Collection /></el-icon>
                      <span>泥塑起源</span>
                    </div>
                  </el-dropdown-item>
                  <el-dropdown-item command="/history#development">
                    <div class="flex items-center py-1">
                      <el-icon class="mr-2"><Timer /></el-icon>
                      <span>发展历程</span>
                    </div>
                  </el-dropdown-item>
                  <el-dropdown-item command="/history#culture">
                    <div class="flex items-center py-1">
                      <el-icon class="mr-2"><Medal /></el-icon>
                      <span>文化渊源</span>
                    </div>
                  </el-dropdown-item>
                  <el-dropdown-item command="/history#inheritors">
                    <div class="flex items-center py-1">
                      <el-icon class="mr-2"><User /></el-icon>
                      <span>技艺传承人</span>
                    </div>
                  </el-dropdown-item>
                </el-dropdown-menu>
              </template>
            </el-dropdown>

            <!-- 其他导航链接 -->
            <router-link to="/crafting" class="nav-link">制作工艺</router-link>
            <router-link to="/classic-products" class="nav-link"
              >经典产品</router-link
            >
            <router-link to="/cultural-products" class="nav-link"
              >文创产品</router-link
            >
            <router-link to="/education" class="nav-link">科普动画</router-link>
            <router-link to="/interactive-games" class="nav-link"
              >互动游戏</router-link
            >
            <router-link to="/contact" class="nav-link">关于我们</router-link>
          </div>

          <!-- 时间显示部分 -->
          <div class="hidden lg:flex items-center">
            <el-card class="time-card" shadow="hover">
              <div class="flex items-center gap-4">
                <!-- 日期部分 -->
                <div class="flex flex-col items-center border-r pr-4">
                  <div class="text-sm text-gray-500">{{ dateInfo.year }}</div>
                  <div class="text-2xl font-bold text-primary">
                    {{ dateInfo.date }}
                  </div>
                  <div class="text-sm text-gray-500">{{ dateInfo.day }}</div>
                </div>

                <!-- 时间部分 -->
                <div class="flex flex-col items-center pl-2">
                  <el-icon class="text-xl text-primary mb-1"><Timer /></el-icon>
                  <div class="text-2xl font-mono font-bold text-gray-700">
                    {{ timeInfo.time }}
                  </div>
                  <div class="text-xs text-gray-500">{{ timeInfo.period }}</div>
                </div>
              </div>
            </el-card>
          </div>
        </div>

        <!-- 移动端导航菜单 -->
        <transition
          enter-active-class="transition duration-200 ease-out"
          enter-from-class="transform -translate-y-4 opacity-0"
          enter-to-class="transform translate-y-0 opacity-100"
          leave-active-class="transition duration-150 ease-in"
          leave-from-class="transform translate-y-0 opacity-100"
          leave-to-class="transform -translate-y-4 opacity-0"
        >
          <div v-show="isMenuOpen" class="lg:hidden mt-2">
            <el-menu
              :default-active="activeIndex"
              mode="vertical"
              class="border-none rounded-lg shadow-lg"
            >
              <el-menu-item index="/">首页</el-menu-item>

              <!-- 移动端历史文化下拉菜单 -->
              <el-sub-menu index="/history">
                <template #title>历史文化</template>
                <el-menu-item
                  v-for="section in historySections"
                  :key="section.anchor"
                  :index="`/history${section.anchor}`"
                  @click="handleSectionClick(section.anchor)"
                >
                  {{ section.title }}
                </el-menu-item>
              </el-sub-menu>

              <el-menu-item index="/crafting">制作工艺</el-menu-item>
              <el-menu-item index="/classic-products">经典产品</el-menu-item>
              <el-menu-item index="/cultural-products">文创产品</el-menu-item>
              <el-menu-item index="/education">科普动画</el-menu-item>
              <el-menu-item index="/interactive-games">互动游戏</el-menu-item>
              <el-menu-item index="/contact">关于我们</el-menu-item>
            </el-menu>
          </div>
        </transition>
      </nav>
    </header>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch, nextTick } from "vue";
import { useRoute, useRouter } from "vue-router";
import {
  Menu,
  Timer,
  ArrowDown,
  Collection,
  Medal,
  User,
} from "@element-plus/icons-vue";

const route = useRoute();
const router = useRouter();
const isMenuOpen = ref(false);
const time = ref("");
const date = ref("");
const activeIndex = ref("/");

// 历史文化页面的各个部分
const historySections = [
  { anchor: "#origin", title: "泥塑起源" },
  { anchor: "#development", title: "发展历程" },
  { anchor: "#culture", title: "文化渊源" },
  { anchor: "#inheritors", title: "技艺传承人" },
];

// 添加日期和时间的响应式数据
const dateInfo = ref({
  year: "",
  date: "",
  day: "",
});

const timeInfo = ref({
  time: "",
  period: "",
});

// 处理部分跳转
const handleSectionClick = async (anchor: string) => {
  try {
    // 关闭移动端菜单
    isMenuOpen.value = false;

    await router.push({
      path: "/history",
      hash: anchor,
    });

    // 等待路由跳转完成
    await nextTick();

    // 滚动到指定位置
    const element = document.querySelector(anchor);
    if (element) {
      const headerHeight = 100;
      window.scrollTo({
        top: element.offsetTop - headerHeight,
        behavior: "smooth",
      });
    }
  } catch (error) {
    console.error("Navigation error:", error);
  }
};

// 监听路由变化
watch(
  () => route.fullPath,
  async (newPath) => {
    // 更新激活的菜单项
    activeIndex.value = route.path;

    // 如果有 hash 并且在历史文化页面，处理滚动
    if (route.hash && route.path === "/history") {
      await nextTick();
      const element = document.querySelector(route.hash);
      if (element) {
        const headerHeight = 100; // 导航栏高度
        const elementPosition = element.getBoundingClientRect().top;
        const offsetPosition =
          elementPosition + window.pageYOffset - headerHeight;

        window.scrollTo({
          top: offsetPosition,
          behavior: "smooth",
        });
      }
    }
  },
  { immediate: true }
);

// 更新时间的函数
const updateTime = () => {
  const now = new Date();
  const days = [
    "星期日",
    "星期一",
    "星期二",
    "星期三",
    "星期四",
    "星期五",
    "星期六",
  ];

  // 更新日期信息
  dateInfo.value = {
    year: now.getFullYear().toString(),
    date: `${(now.getMonth() + 1).toString().padStart(2, "0")}/${now
      .getDate()
      .toString()
      .padStart(2, "0")}`,
    day: days[now.getDay()],
  };

  // 更新时间信息
  const hours = now.getHours();
  const minutes = now.getMinutes();
  const seconds = now.getSeconds();

  timeInfo.value = {
    time: `${hours.toString().padStart(2, "0")}:${minutes
      .toString()
      .padStart(2, "0")}:${seconds.toString().padStart(2, "0")}`,
    period: hours < 12 ? "上午" : "下午",
  };
};

let timer: number;

onMounted(() => {
  updateTime();
  timer = window.setInterval(updateTime, 1000);
});

onUnmounted(() => {
  if (timer) {
    clearInterval(timer);
  }
});

// 处理锚点跳转
const handleAnchorClick = (anchor: string) => {
  const element = document.querySelector(anchor);
  if (element) {
    element.scrollIntoView({ behavior: "smooth" });
  }
};

// 可以添加滚动监听来改变导航栏样式
const handleScroll = () => {
  const header = document.querySelector("header");
  if (window.scrollY > 0) {
    header?.classList.add("header-shadow");
  } else {
    header?.classList.remove("header-shadow");
  }
};

// 添加滚动监听
onMounted(() => {
  window.addEventListener("scroll", handleScroll);
});

// 移除滚动监听
onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll);
});

// 处理下拉菜单点击
const handleCommand = (command: string) => {
  router.push(command);
};
</script>

<style scoped>
/* 导航链接基础样式 */
.nav-link {
  @apply text-gray-700 font-medium px-4 py-2 rounded-lg transition-colors duration-300 hover:text-primary hover:bg-primary/5;
  position: relative;
}

.nav-link::after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 50%;
  width: 0;
  height: 2px;
  background: var(--el-color-primary);
  transition: all 0.3s ease;
  transform: translateX(-50%);
}

.nav-link:hover::after {
  width: 80%;
}

.router-link-active::after {
  width: 80%;
}

/* 下拉菜单样式 */
:deep(.custom-dropdown) {
  border-radius: 12px;
  padding: 8px;
  min-width: 180px;
  border: none;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(8px);
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
}

:deep(.el-dropdown-menu__item) {
  padding: 8px 16px;
  border-radius: 8px;
  margin: 2px 0;
  font-size: 0.95rem;
  color: var(--el-text-color-primary);
}

:deep(.el-dropdown-menu__item:hover) {
  background-color: var(--el-color-primary-light-9);
  color: var(--el-color-primary);
}

:deep(.el-dropdown-menu__item i) {
  color: var(--el-text-color-secondary);
}

:deep(.el-dropdown-menu__item:hover i) {
  color: var(--el-color-primary);
}

/* 动画效果 */
:deep(.el-dropdown-menu) {
  transform-origin: top;
  animation: dropdown-in 0.2s ease-out;
}

@keyframes dropdown-in {
  0% {
    opacity: 0;
    transform: scaleY(0.8);
  }
  100% {
    opacity: 1;
    transform: scaleY(1);
  }
}

/* 移动端样式 */
@media (max-width: 1024px) {
  .nav-link {
    @apply w-full text-left;
  }

  .nav-link::after {
    display: none;
  }
}

/* 添加平滑滚动效果 */
html {
  scroll-behavior: smooth;
}

/* 滚动时的阴影效果 */
.header-shadow {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

/* 确保固定导航栏下的内容不被遮挡 */
.main-content {
  padding-top: 100px;
}

/* 修复路由链接样式 */
:deep(.el-sub-menu__title) {
  .router-link-active {
    color: inherit;
  }
}

/* 确保子菜单项正确显示 */
:deep(.el-menu--popup) {
  min-width: 160px;
  border-radius: 4px;
  margin-top: 5px;
}

/* 时间卡片样式 */
.time-card {
  @apply bg-white/90 backdrop-blur-md;
  border: 1px solid rgba(var(--el-color-primary-rgb), 0.1);
  transition: all 0.3s ease;
}

.time-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  border-color: var(--el-color-primary-light-5);
}

:deep(.el-card__body) {
  padding: 12px 20px;
}

/* 数字跳动动画 */
.text-2xl {
  transition: all 0.3s ease;
}

.text-2xl:hover {
  color: var(--el-color-primary);
  transform: scale(1.1);
}

/* 分隔线样式 */
.border-r {
  border-color: rgba(var(--el-color-primary-rgb), 0.1);
}

/* 图标动画 */
.el-icon {
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}
</style>
