<script setup>
import { HomeFilled, Document, Picture, View, User, DataAnalysis, Search } from '@element-plus/icons-vue'
import { ref, computed } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { useUserStore } from '../../stores/user'
import { searchCoursePage } from '../../api/course'
import { searchUserPage } from '../../api/user'
import CourseManage from './CourseManage.vue'
import CourseCreate from './CourseCreate.vue'
import CourseDetail from './CourseDetail.vue'
import ResourceManage from './ResourceManage.vue'
import ResourceCreate from './ResourceCreate.vue'
import ResourceDetail from './ResourceDetail.vue'
import HomeworkManage from './HomeworkManage.vue'
import HomeworkCreate from './HomeworkCreate.vue'
import HomeworkDetail from './HomeworkDetail.vue'
import StudentHomeworkDetail from './StudentHomeworkDetail.vue'
import Statistic from './Statistic.vue'
import Dashboard from './Dashboard.vue'
import UserManagement from './UserManagement.vue'
import { ElMessage } from 'element-plus'

const router = useRouter()
const route = useRoute()
const userStore = useUserStore()

const activeMenu = ref('dashboard')
const showUserManagement = ref(false)

const searchText = ref('')
const searchLoading = ref(false)

const handleSearch = async () => {
  if(searchText.value.trim()){
    searchLoading.value = true
    try {
      console.log('搜索内容：',searchText.value)
      const currentPage = route.name
      
      if (currentPage.includes('Course')) {
        // 搜索课程
        const response = await searchCoursePage({
          keywords: searchText.value,
          page: 1,
          size: 10
        })
        console.log('搜索课程结果:', response)
        ElMessage.success(`找到 ${response.total || 0} 个课程`)
      } else if (currentPage.includes('User')) {
        // 搜索用户
        const response = await searchUserPage({
          keywords: searchText.value,
          page: 1,
          size: 10
        })
        console.log('搜索用户结果:', response)
        ElMessage.success(`找到 ${response.total || 0} 个用户`)
      } else if (currentPage.includes('Resource')) {
        // 资源搜索（暂时使用提示）
        ElMessage.info('资源搜索功能开发中')
      } else if (currentPage.includes('Homework')) {
        // 作业搜索（暂时使用提示）
        ElMessage.info('作业搜索功能开发中')
      } else {
        // 默认搜索所有类型
        ElMessage.info('请在具体页面中进行搜索')
      }
    } catch (error) {
      console.error('搜索失败:', error)
      ElMessage.error('搜索失败，请稍后重试')
    } finally {
      searchLoading.value = false
    }
  }
}

const clearSearch = () => {
  searchText.value = ''
}

const currentComponent = computed(() => {
  const name = route.name
  if (name === 'TeacherCourseManage') return CourseManage
  if (name === 'TeacherCourseCreate') return CourseCreate
  if (name === 'TeacherCourseDetail') return CourseDetail
  if (name === 'TeacherResourceManage') return ResourceManage
  if (name === 'TeacherResourceCreate') return ResourceCreate
  if (name === 'TeacherResourceDetail') return ResourceDetail
  if (name === 'TeacherHomeworkManage') return HomeworkManage
  if (name === 'TeacherHomeworkCreate') return HomeworkCreate
  if (name === 'TeacherHomeworkDetail') return HomeworkDetail
  if (name === 'TeacherStudentHomeworkDetail') return StudentHomeworkDetail
  if (name === 'TeacherStatistic') return Statistic
  if (name === 'TeacherUserManagement') return UserManagement
  return Dashboard
})

const handleMenuClick = (menu) => {
  activeMenu.value = menu
  switch (menu) {
    case 'dashboard':
      router.push('/teacher')
      break
    case 'course':
      router.push('/teacher/course')
      break
    case 'resource':
      router.push('/teacher/resource')
      break
    case 'homework':
      router.push('/teacher/homework')
      break
    case 'statistic':
      router.push('/teacher/statistic')
      break
  }
}

const handleUser = () => {
  router.push('/teacher/user-management')
}

const handleLogout = () => {
  userStore.logout()
  router.push('/login')
}
</script>

<template>
  <div class="home-container">
    <el-header class="header">
      <div class="header-left">
        <div class="logo">
          <img src="../../assets/logo.png" alt="理程学院" />
        </div>
        <div class="header-title">
        </div>
      </div>
      <div class="header-center">
        <el-input
          v-model="searchText"
          placeholder="搜索课程、用户、资源、作业..."
          class="search-input"
          clearable
          @clear="clearSearch"
          @keyup.enter="handleSearch"
        >
          <template #prefix>
            <el-icon><Search /></el-icon>
          </template>
          <template #append>
            <el-button :loading="searchLoading" @click="handleSearch">搜索</el-button>
          </template>
        </el-input>
      </div>
      <div class="header-right">
        <el-button type="primary" @click="handleUser">用户管理</el-button>
        <el-button type="primary" @click="handleLogout">退出登录</el-button>
      </div>
    </el-header>
    <div class="main-container">
      <el-aside class="aside">
        <el-menu
          :default-active="activeMenu"
          class="el-menu-vertical-demo"
          background-color="#001529"
          text-color="#fff"
          active-text-color="#ffd04b"
        >
          <el-menu-item index="dashboard" @click="handleMenuClick('dashboard')">
            <el-icon><HomeFilled /></el-icon>
            <span>首页</span>
          </el-menu-item>
          <el-menu-item index="course" @click="handleMenuClick('course')">
            <el-icon><Document /></el-icon>
            <span>我的课程</span>
          </el-menu-item>
          <el-menu-item index="resource" @click="handleMenuClick('resource')">
            <el-icon><Picture /></el-icon>
            <span>教学资源</span>
          </el-menu-item>
          <el-menu-item index="homework" @click="handleMenuClick('homework')">
            <el-icon><View /></el-icon>
            <span>作业管理</span>
          </el-menu-item>
          <el-menu-item index="statistic" @click="handleMenuClick('statistic')">
            <el-icon><DataAnalysis /></el-icon>
            <span>教学统计</span>
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-main class="main">
        <component :is="currentComponent" />
      </el-main>
    </div>
  </div>
</template>

<style scoped>
.home-container {
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  background: #f8f9fa;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

/* 顶部栏设计 */
.header {
  height: 100px;
  background: white;
  color: #333;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 30px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  position: relative;
  z-index: 100;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 20px;
}

.logo {
  height: 50px;
  transition: transform 0.3s ease;
  cursor: pointer;
}

.logo:hover {
  transform: scale(1.05);
}

.logo img {
  max-width: 160px;
  max-height: 160px;
  width: auto;
  height: auto;
  object-fit: contain;
}

.header-title {
  font-size: 18px;
  font-weight: 600;
  letter-spacing: 0.5px;
}

.header-right {
  display: flex;
  align-items: center;
  gap: 15px;
}

.header-center {
  flex: 1;              /* 弹性占满可用空间 */
  max-width: 600px;     /* 最大宽度限制，防止过宽 */
  margin: 0 40px;       /* 左右各40px外边距 */
}

.search-input {
  width: 100%;          /* 填满父容器宽度 */
  border-radius: 20px;  /* 整体圆角（但会被内部覆盖） */
}

.search-input :deep(.el-input__wrapper) {
  border-radius: 20px;                    /* 圆角20px */
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1); /* 轻微阴影 */
  transition: all 0.3s ease;              /* 平滑过渡动画 */
}

.search-input :deep(.el-input__wrapper:hover) {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15); /* 阴影加深，上浮感 */
}

.search-input :deep(.el-input__wrapper.is-focus) {
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2); /* 更明显的阴影 */
}

.search-input :deep(.el-input-group__append) {
  border-top-right-radius: 20px;      /* 右上圆角 */
  border-bottom-right-radius: 20px;   /* 右下圆角 */
  background: #667eea;                /* 渐变紫色背景 */
  color: white;
  border: none;                       /* 移除边框 */
}

.search-input :deep(.el-input-group__append .el-button) {
  border: none;
  background: transparent;  /* 透明背景 */
  color: white;
  font-weight: 500;         /* 中等字重 */
}

.search-input :deep(.el-input-group__append .el-button:hover) {
  background: rgba(255, 255, 255, 0.2); /* 白色半透明悬停效果 */
}

.header-right .el-button {
  border-radius: 8px;
  transition: all 0.3s ease;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.header-right .el-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

/* 主容器 */
.main-container {
  flex: 1;
  display: flex;
  overflow: hidden;
}

/* 侧边栏设计 */
.aside {
  width: 240px;
  background: #001529;
  box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
  overflow-y: hidden;
  transition: all 0.3s ease;
}

.aside:hover {
  box-shadow: 2px 0 15px rgba(0, 0, 0, 0.2);
}

/* 菜单样式 */
.el-menu-vertical-demo {
  height: 100%;
  background: transparent;
  border-right: none;
  padding: 20px 0;
}

.el-menu-item {
  height: 56px;
  line-height: 56px;
  margin: 0 16px;
  border-radius: 10px;
  transition: all 0.3s ease;
  color: #ffffff;
  font-size: 14px;
  font-weight: 500;
}

.el-menu-item:hover {
  background: rgba(255, 255, 255, 0.1);
  color: #ffffff;
}

.el-menu-item.is-active {
  background: #ff4d4f;
  color: white;
  box-shadow: 0 4px 12px rgba(255, 77, 79, 0.3);
}

.el-menu-item.is-active:hover {
  background: #ff7875;
  color: white;
}

.el-menu-item i {
  font-size: 18px;
  margin-right: 12px;
  transition: all 0.3s ease;
}

.el-menu-item.is-active i {
  transform: scale(1.1);
}

/* 主内容区 */
.main {
  flex: 1;
  padding: 30px;
  overflow-y: auto;
  background: #f8f9fa;
}

/* 滚动条样式 */
.main::-webkit-scrollbar {
  width: 8px;
}

.main::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 4px;
}

.main::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 4px;
}

.main::-webkit-scrollbar-thumb:hover {
  background: #a8a8a8;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .header {
    padding: 0 20px;
  }
  
  .logo img {
    max-width: 140px;
  }
  
  .header-title {
    display: none;
  }
  
  .aside {
    width: 80px;
  }
  
  .el-menu-item span {
    display: none;
  }
  
  .el-menu-item i {
    margin-right: 0;
    font-size: 20px;
  }
  
  .main {
    padding: 20px;
  }
}
</style>