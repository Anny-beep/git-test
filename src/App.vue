<template>
  <div class="app">
    <!-- 顶部导航栏 -->
    <header class="app-header">
      <div class="logo">
        <h1>地理信息数据平台</h1>
      </div>
      <nav class="nav">
        <ul>
          <li :class="['nav-item', { active: activeNavItem === 'home' }]" @click.stop="handleNavClick('home')">
            <a href="#">地图首页</a>
          </li>
          <li :class="['nav-item', { active: activeNavItem === 'data' }]" @click.stop="handleNavClick('data')">
            <a href="#">数据中心</a>
          </li>
          <li :class="['nav-item', { active: activeNavItem === 'tools' }]" @click.stop="handleNavClick('tools')">
            <a href="#">分析工具</a>
          </li>
          <li :class="['nav-item', { active: activeNavItem === 'settings' }]" @click.stop="handleNavClick('settings')">
            <a href="#">系统设置</a>
          </li>
        </ul>
      </nav>
    </header>

    <!-- 主要内容区域 -->
    <main class="app-main">
      <!-- 地图首页 -->
      <div v-if="activeNavItem === 'home'" class="home-page">
        <div class="main-content">
          <!-- 左侧地图区域 -->
          <div class="map-section">
            <div class="section-header">
              <h2>地图可视化</h2>
              <div class="map-tools">
                <button @click="toggleLayerControl" class="tool-btn">
                  图层控制
                </button>
                <button @click="toggleDataOverlay" class="tool-btn">
                  数据叠加
                </button>
              </div>
            </div>
            <div class="map-wrapper">
              <AMap
                ref="mapRef"
                :center="mapCenter"
                :zoom="mapZoom"
                @map-loaded="onMapLoaded"
                @map-click="onMapClick"
              />
            </div>
          </div>

          <!-- 右侧数据面板 -->
          <div class="data-section">
            <div class="section-header">
              <h2>数据统计分析</h2>
            </div>
            <DataChart title="区域人口分布" />
            <DataChart title="经济发展指标" />
          </div>
        </div>
      </div>

      <!-- 数据中心 -->
      <DataCenter v-else-if="activeNavItem === 'data'" />

      <!-- 分析工具 -->
      <AnalysisTools v-else-if="activeNavItem === 'tools'" />

      <!-- 系统设置 -->
      <Settings v-else-if="activeNavItem === 'settings'" />
    </main>

    <!-- 底部信息 -->
    <footer class="app-footer">
      <div class="footer-content">
        <p>&copy; 2026 地理信息数据平台 - 基于高德地图 API 构建</p>
        <FooterLinks />
      </div>
    </footer>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import AMap from './components/AMap.vue'
import DataChart from './components/DataChart.vue'
import DataCenter from './components/DataCenter.vue'
import AnalysisTools from './components/AnalysisTools.vue'
import Settings from './components/Settings.vue'
import FooterLinks from './components/FooterLinks.vue'
import { mapConfig } from './config/map'

  export default {
    name: 'App',
    components: {
      AMap,
      DataChart,
      DataCenter,
      AnalysisTools,
      Settings,
      FooterLinks
    },
  setup() {
    const mapRef = ref(null)
    const mapCenter = ref(mapConfig.center)
    const mapZoom = ref(mapConfig.zoom)
    const layerControlVisible = ref(false)
    const dataOverlayVisible = ref(false)
    const activeNavItem = ref('home')

    // 导航菜单点击处理
    const handleNavClick = (item) => {
      activeNavItem.value = item
      console.log('导航到:', item)
      // 这里可以添加页面切换逻辑---
    }

    // 地图加载完成回调
    const onMapLoaded = (map) => {
      console.log('地图加载完成:', map)
      // 可以在这里添加初始化数据标记等操作
      addSampleMarkers(map)
    }

    // 地图点击事件回调
    const onMapClick = (e) => {
      console.log('地图点击:', e)
      // 可以在这里处理地图点击事件，如显示信息窗口等
    }

    // 添加示例标记
    const addSampleMarkers = (map) => {
      // 示例标记点数据
      const markers = [
        { position: [116.397428, 39.90923], title: '北京市中心' },
        { position: [116.417428, 39.91923], title: '朝阳区' },
        { position: [116.377428, 39.90923], title: '西城区' },
        { position: [116.397428, 39.92923], title: '海淀区' }
      ]

      markers.forEach(markerData => {
        if (mapRef.value) {
          mapRef.value.addMarker(markerData.position, {
            title: markerData.title,
            icon: new window.AMap.Icon({
              size: new window.AMap.Size(32, 32),
              image: 'https://webapi.amap.com/theme/v1.3/markers/n/mark_r.png',
              imageSize: new window.AMap.Size(32, 32)
            })
          })
        }
      })
    }

    // 切换图层控制
    const toggleLayerControl = () => {
      layerControlVisible.value = !layerControlVisible.value
      console.log('图层控制:', layerControlVisible.value)
    }

    // 切换数据叠加
    const toggleDataOverlay = () => {
      dataOverlayVisible.value = !dataOverlayVisible.value
      console.log('数据叠加:', dataOverlayVisible.value)
    }

    onMounted(() => {
      console.log('应用初始化完成')
    })

    return {
      mapRef,
      mapCenter,
      mapZoom,
      activeNavItem,
      onMapLoaded,
      onMapClick,
      toggleLayerControl,
      toggleDataOverlay,
      handleNavClick
    }
  }
}
</script>

<style>
/* 全局样式重置 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-size: 14px;
  line-height: 1.5;
  color: #333;
  background-color: #f5f7fa;
}

/* 应用容器 */
.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

/* 顶部导航栏 */
.app-header {
  background: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 0 30px;
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  position: sticky;
  top: 0;
  z-index: 1000;
}

.logo h1 {
  font-size: 20px;
  font-weight: 600;
  color: #409eff;
}

.nav ul {
  display: flex;
  list-style: none;
  gap: 20px;
}

.nav-item {
  position: relative;
}

.nav-item a {
  text-decoration: none;
  color: #666;
  font-size: 14px;
  font-weight: 500;
  padding: 8px 16px;
  border-radius: 4px;
  transition: all 0.3s ease;
}

.nav-item a:hover {
  color: #409eff;
  background-color: #ecf5ff;
}

.nav-item.active a {
  color: #409eff;
  background-color: #ecf5ff;
}

/* 主要内容区域 */
.app-main {
  flex: 1;
  padding: 30px;
  min-height: calc(100vh - 120px);
}

.home-page {
  height: 100%;
}

.main-content {
  display: grid;
  grid-template-columns: 1fr 400px;
  gap: 30px;
  height: calc(100vh - 150px);
}

/* 地图区域 */
.map-section {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.section-header {
  padding: 20px;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #fafafa;
}

.section-header h2 {
  font-size: 16px;
  font-weight: 600;
  color: #333;
  margin: 0;
}

.map-tools {
  display: flex;
  gap: 10px;
}

.tool-btn {
  padding: 6px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.3s ease;
}

.tool-btn:hover {
  border-color: #409eff;
  color: #409eff;
}

.map-wrapper {
  flex: 1;
  position: relative;
}

.map-wrapper :deep(.map-container) {
  height: 100%;
}

/* 数据面板区域 */
.data-section {
  display: flex;
  flex-direction: column;
  gap: 20px;
  overflow-y: auto;
  padding-right: 10px;
}

.data-section .section-header {
  position: sticky;
  top: 0;
  z-index: 100;
  margin-bottom: -10px;
}

/* 底部信息 */
.app-footer {
  background: white;
  border-top: 1px solid #eee;
  padding: 20px 30px;
  margin-top: auto;
}

.footer-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 12px;
  color: #999;
}

/* 滚动条样式 */
.data-section::-webkit-scrollbar {
  width: 6px;
}

.data-section::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 3px;
}

.data-section::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 3px;
}

.data-section::-webkit-scrollbar-thumb:hover {
  background: #a8a8a8;
}

/* 响应式设计 */
@media (max-width: 1200px) {
  .main-content {
    grid-template-columns: 1fr;
    height: auto;
  }
  
  .data-section {
    max-height: 600px;
  }
}

@media (max-width: 768px) {
  .app-header {
    padding: 0 20px;
  }
  
  .logo h1 {
    font-size: 18px;
  }
  
  .nav ul {
    gap: 10px;
  }
  
  .nav-item a {
    padding: 6px 12px;
    font-size: 13px;
  }
  
  .app-main {
    padding: 20px;
  }
  
  .main-content {
    gap: 20px;
  }
}
</style>