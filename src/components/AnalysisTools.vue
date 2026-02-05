<template>
  <div class="analysis-tools">
    <div class="page-header">
      <h1>分析工具</h1>
      <p>使用专业工具分析地理信息数据</p>
    </div>

    <!-- 工具分类 -->
    <div class="tool-categories">
      <div 
        class="category-card" 
        v-for="category in toolCategories" 
        :key="category.value"
        :class="{ active: activeCategory === category.value }"
        @click="activeCategory = category.value"
      >
        <div class="category-icon">{{ category.icon }}</div>
        <div class="category-content">
          <h3>{{ category.label }}</h3>
          <p>{{ category.description }}</p>
        </div>
      </div>
    </div>

    <!-- 工具详情 -->
    <div class="tool-details">
      <div class="section-header">
        <h2>{{ currentCategory?.label || '选择分析工具' }}</h2>
      </div>

      <!-- 路径规划工具 -->
      <div v-if="activeCategory === 'route'" class="tool-panel">
        <div class="tool-form">
          <div class="form-group">
            <label>起点</label>
            <input type="text" v-model="routeStart" placeholder="输入起点地址" />
          </div>
          <div class="form-group">
            <label>终点</label>
            <input type="text" v-model="routeEnd" placeholder="输入终点地址" />
          </div>
          <div class="form-group">
            <label>出行方式</label>
            <select v-model="routeMode">
              <option value="drive">驾车</option>
              <option value="walk">步行</option>
              <option value="bike">骑行</option>
              <option value="transit">公交</option>
            </select>
          </div>
          <button class="btn btn-primary" @click="calculateRoute">
            计算路线
          </button>
        </div>
        <div class="tool-result">
          <h3>路线规划结果</h3>
          <div v-if="routeResult" class="result-content">
            <div class="result-item">
              <span class="result-label">总距离:</span>
              <span class="result-value">{{ routeResult.distance }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">预计时间:</span>
              <span class="result-value">{{ routeResult.duration }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">出行方式:</span>
              <span class="result-value">{{ routeResult.mode }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">路线详情:</span>
              <span class="result-value">{{ routeResult.steps }}</span>
            </div>
          </div>
          <div v-else class="result-placeholder">
            <span>🗺️</span>
            <p>点击"计算路线"查看规划结果</p>
          </div>
        </div>
      </div>

      <!-- 区域分析工具 -->
      <div v-else-if="activeCategory === 'area'" class="tool-panel">
        <div class="tool-form">
          <div class="form-group">
            <label>分析区域</label>
            <input type="text" v-model="areaName" placeholder="输入区域名称" />
          </div>
          <div class="form-group">
            <label>分析类型</label>
            <select v-model="areaAnalysisType">
              <option value="population">人口分布</option>
              <option value="economy">经济活力</option>
              <option value="facility">设施密度</option>
              <option value="environment">环境质量</option>
            </select>
          </div>
          <div class="form-group">
            <label>分析半径</label>
            <input type="number" v-model="areaRadius" placeholder="输入分析半径(米)" />
          </div>
          <button class="btn btn-primary" @click="analyzeArea">
            分析区域
          </button>
        </div>
        <div class="tool-result">
          <h3>区域分析结果</h3>
          <div v-if="areaResult" class="result-content">
            <div class="result-item">
              <span class="result-label">分析区域:</span>
              <span class="result-value">{{ areaResult.area }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">分析类型:</span>
              <span class="result-value">{{ areaResult.type }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">分析半径:</span>
              <span class="result-value">{{ areaResult.radius }} 米</span>
            </div>
            <div class="result-item">
              <span class="result-label">分析结果:</span>
              <span class="result-value">{{ areaResult.score }} 分</span>
            </div>
            <div class="result-item">
              <span class="result-label">评价:</span>
              <span class="result-value">{{ areaResult.evaluation }}</span>
            </div>
          </div>
          <div v-else class="result-placeholder">
            <span>📊</span>
            <p>点击"分析区域"查看分析结果</p>
          </div>
        </div>
      </div>

      <!-- 地形分析工具 -->
      <div v-else-if="activeCategory === 'terrain'" class="tool-panel">
        <div class="tool-form">
          <div class="form-group">
            <label>分析区域</label>
            <input type="text" v-model="terrainArea" placeholder="输入地形区域" />
          </div>
          <div class="form-group">
            <label>分析类型</label>
            <select v-model="terrainAnalysisType">
              <option value="elevation">高程分析</option>
              <option value="slope">坡度分析</option>
              <option value="aspect">坡向分析</option>
              <option value="viewshed">可视域分析</option>
            </select>
          </div>
          <div class="form-group">
            <label>精度设置</label>
            <select v-model="terrainAccuracy">
              <option value="low">低精度</option>
              <option value="medium">中精度</option>
              <option value="high">高精度</option>
            </select>
          </div>
          <button class="btn btn-primary" @click="analyzeTerrain">
            分析地形
          </button>
        </div>
        <div class="tool-result">
          <h3>地形分析结果</h3>
          <div v-if="terrainResult" class="result-content">
            <div class="result-item">
              <span class="result-label">分析区域:</span>
              <span class="result-value">{{ terrainResult.area }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">分析类型:</span>
              <span class="result-value">{{ terrainResult.type }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">精度设置:</span>
              <span class="result-value">{{ terrainResult.accuracy }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">分析结果:</span>
              <span class="result-value">{{ terrainResult.data }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">分析时间:</span>
              <span class="result-value">{{ terrainResult.time }}</span>
            </div>
          </div>
          <div v-else class="result-placeholder">
            <span>⛰️</span>
            <p>点击"分析地形"查看分析结果</p>
          </div>
        </div>
      </div>

      <!-- 网络分析工具 -->
      <div v-else-if="activeCategory === 'network'" class="tool-panel">
        <div class="tool-form">
          <div class="form-group">
            <label>分析中心点</label>
            <input type="text" v-model="networkCenter" placeholder="输入中心点" />
          </div>
          <div class="form-group">
            <label>分析类型</label>
            <select v-model="networkAnalysisType">
              <option value="service">服务范围</option>
              <option value="closest">最近设施</option>
              <option value="isochrone">等时圈</option>
              <option value="accessibility">可达性分析</option>
            </select>
          </div>
          <div class="form-group">
            <label>分析参数</label>
            <input type="number" v-model="networkParameter" placeholder="输入分析参数" />
          </div>
          <button class="btn btn-primary" @click="analyzeNetwork">
            分析网络
          </button>
        </div>
        <div class="tool-result">
          <h3>网络分析结果</h3>
          <div v-if="networkResult" class="result-content">
            <div class="result-item">
              <span class="result-label">中心点:</span>
              <span class="result-value">{{ networkResult.center }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">分析类型:</span>
              <span class="result-value">{{ networkResult.type }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">分析参数:</span>
              <span class="result-value">{{ networkResult.parameter }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">分析结果:</span>
              <span class="result-value">{{ networkResult.data }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">覆盖范围:</span>
              <span class="result-value">{{ networkResult.coverage }}</span>
            </div>
          </div>
          <div v-else class="result-placeholder">
            <span>🌐</span>
            <p>点击"分析网络"查看分析结果</p>
          </div>
        </div>
      </div>

      <!-- 默认提示 -->
      <div v-else class="tool-panel">
        <div class="empty-state">
          <div class="empty-icon">🔍</div>
          <h3>选择分析工具</h3>
          <p>从上方选择一个分析工具类别开始分析</p>
        </div>
      </div>
    </div>

    <!-- 最近分析记录 -->
    <div class="recent-analyses">
      <div class="section-header">
        <h2>最近分析记录</h2>
      </div>
      <div class="analysis-records">
        <div class="record-item" v-for="(record, index) in recentAnalyses" :key="index">
          <div class="record-icon">{{ record.icon }}</div>
          <div class="record-content">
            <div class="record-title">{{ record.title }}</div>
            <div class="record-meta">
              <span class="record-time">{{ record.time }}</span>
              <span class="record-category">{{ record.category }}</span>
            </div>
          </div>
          <button class="record-action" @click="viewAnalysisRecord(record)">查看</button>
        </div>
        <div v-if="recentAnalyses.length === 0" class="empty-records">
          <p>暂无分析记录</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: 'AnalysisTools',
  setup() {
    const activeCategory = ref('')

    // 工具分类
    const toolCategories = [
      {
        value: 'route',
        label: '路径规划',
        description: '规划最优出行路线',
        icon: '🗺️'
      },
      {
        value: 'area',
        label: '区域分析',
        description: '分析特定区域的各项指标',
        icon: '📊'
      },
      {
        value: 'terrain',
        label: '地形分析',
        description: '分析地形地貌特征',
        icon: '⛰️'
      },
      {
        value: 'network',
        label: '网络分析',
        description: '分析空间网络关系',
        icon: '🌐'
      }
    ]

    // 当前选中的分类
    const currentCategory = computed(() => {
      return toolCategories.find(category => category.value === activeCategory.value)
    })

    // 路径规划参数
    const routeStart = ref('北京市中心')
    const routeEnd = ref('朝阳区')
    const routeMode = ref('drive')
    const routeResult = ref(null)

    // 区域分析参数
    const areaName = ref('北京市海淀区')
    const areaAnalysisType = ref('population')
    const areaRadius = ref(1000)
    const areaResult = ref(null)

    // 地形分析参数
    const terrainArea = ref('香山公园')
    const terrainAnalysisType = ref('elevation')
    const terrainAccuracy = ref('medium')
    const terrainResult = ref(null)

    // 网络分析参数
    const networkCenter = ref('北京市中心')
    const networkAnalysisType = ref('service')
    const networkParameter = ref(5)
    const networkResult = ref(null)

    // 最近分析记录
    const recentAnalyses = ref([
      {
        title: '北京市中心到朝阳区路线规划',
        category: '路径规划',
        time: '2026-02-04 14:30',
        icon: '🗺️'
      },
      {
        title: '海淀区人口分布分析',
        category: '区域分析',
        time: '2026-02-04 13:15',
        icon: '📊'
      },
      {
        title: '香山公园地形分析',
        category: '地形分析',
        time: '2026-02-04 11:45',
        icon: '⛰️'
      }
    ])

    // 添加分析记录
    const addAnalysisRecord = (title, category, icon) => {
      const newRecord = {
        title,
        category,
        time: new Date().toLocaleString('zh-CN'),
        icon
      }
      recentAnalyses.value.unshift(newRecord)
      // 只保留最近10条记录
      if (recentAnalyses.value.length > 10) {
        recentAnalyses.value = recentAnalyses.value.slice(0, 10)
      }
    }

    // 查看分析记录
    const viewAnalysisRecord = (record) => {
      console.log('查看分析记录:', record)
      alert(`查看分析记录:\n标题: ${record.title}\n类别: ${record.category}\n时间: ${record.time}`)
    }

    // 路径规划计算
    const calculateRoute = () => {
      if (!routeStart.value || !routeEnd.value) {
        alert('请输入起点和终点')
        return
      }

      console.log('计算路线:', {
        start: routeStart.value,
        end: routeEnd.value,
        mode: routeMode.value
      })

      // 模拟路径规划结果
      const modeMap = {
        drive: '驾车',
        walk: '步行',
        bike: '骑行',
        transit: '公交'
      }

      routeResult.value = {
        distance: '12.5 公里',
        duration: '约 30 分钟',
        mode: modeMap[routeMode.value],
        steps: '北京市中心 → 建国门 → 国贸 → 朝阳区'
      }

      // 添加到分析记录
      addAnalysisRecord(`${routeStart.value}到${routeEnd.value}路线规划`, '路径规划', '🗺️')
    }

    // 区域分析
    const analyzeArea = () => {
      if (!areaName.value) {
        alert('请输入分析区域')
        return
      }

      console.log('分析区域:', {
        area: areaName.value,
        type: areaAnalysisType.value,
        radius: areaRadius.value
      })

      // 模拟区域分析结果
      const typeMap = {
        population: '人口分布',
        economy: '经济活力',
        facility: '设施密度',
        environment: '环境质量'
      }

      const score = Math.floor(Math.random() * 20) + 80
      let evaluation
      if (score >= 90) evaluation = '优秀'
      else if (score >= 80) evaluation = '良好'
      else evaluation = '一般'

      areaResult.value = {
        area: areaName.value,
        type: typeMap[areaAnalysisType.value],
        radius: areaRadius.value,
        score,
        evaluation
      }

      // 添加到分析记录
      addAnalysisRecord(`${areaName.value}${typeMap[areaAnalysisType.value]}分析`, '区域分析', '📊')
    }

    // 地形分析
    const analyzeTerrain = () => {
      if (!terrainArea.value) {
        alert('请输入分析区域')
        return
      }

      console.log('分析地形:', {
        area: terrainArea.value,
        type: terrainAnalysisType.value,
        accuracy: terrainAccuracy.value
      })

      // 模拟地形分析结果
      const typeMap = {
        elevation: '高程分析',
        slope: '坡度分析',
        aspect: '坡向分析',
        viewshed: '可视域分析'
      }

      const accuracyMap = {
        low: '低精度',
        medium: '中精度',
        high: '高精度'
      }

      let data
      if (terrainAnalysisType.value === 'elevation') {
        data = `平均海拔 ${Math.floor(Math.random() * 500) + 100} 米`
      } else if (terrainAnalysisType.value === 'slope') {
        data = `平均坡度 ${Math.floor(Math.random() * 30) + 5}°`
      } else if (terrainAnalysisType.value === 'aspect') {
        data = `主要坡向 东南方向`
      } else {
        data = `可视范围 ${Math.floor(Math.random() * 5) + 3} 公里`
      }

      terrainResult.value = {
        area: terrainArea.value,
        type: typeMap[terrainAnalysisType.value],
        accuracy: accuracyMap[terrainAccuracy.value],
        data,
        time: `${Math.floor(Math.random() * 5) + 1} 秒`
      }

      // 添加到分析记录
      addAnalysisRecord(`${terrainArea.value}${typeMap[terrainAnalysisType.value]}`, '地形分析', '⛰️')
    }

    // 网络分析
    const analyzeNetwork = () => {
      if (!networkCenter.value) {
        alert('请输入分析中心点')
        return
      }

      console.log('分析网络:', {
        center: networkCenter.value,
        type: networkAnalysisType.value,
        parameter: networkParameter.value
      })

      // 模拟网络分析结果
      const typeMap = {
        service: '服务范围',
        closest: '最近设施',
        isochrone: '等时圈',
        accessibility: '可达性分析'
      }

      let data, coverage
      if (networkAnalysisType.value === 'service') {
        data = `覆盖 ${Math.floor(Math.random() * 50) + 10} 个设施`
        coverage = `${networkParameter.value} 公里半径`
      } else if (networkAnalysisType.value === 'closest') {
        data = `最近设施距离 ${Math.floor(Math.random() * 500) + 100} 米`
        coverage = '周边 5 公里'
      } else if (networkAnalysisType.value === 'isochrone') {
        data = `${networkParameter.value} 分钟可达范围`
        coverage = `覆盖面积 ${Math.floor(Math.random() * 10) + 5} 平方公里`
      } else {
        data = `可达性评分 ${Math.floor(Math.random() * 20) + 80} 分`
        coverage = '综合评估'
      }

      networkResult.value = {
        center: networkCenter.value,
        type: typeMap[networkAnalysisType.value],
        parameter: networkParameter.value,
        data,
        coverage
      }

      // 添加到分析记录
      addAnalysisRecord(`${networkCenter.value}${typeMap[networkAnalysisType.value]}`, '网络分析', '🌐')
    }

    return {
      activeCategory,
      toolCategories,
      currentCategory,
      routeStart,
      routeEnd,
      routeMode,
      routeResult,
      areaName,
      areaAnalysisType,
      areaRadius,
      areaResult,
      terrainArea,
      terrainAnalysisType,
      terrainAccuracy,
      terrainResult,
      networkCenter,
      networkAnalysisType,
      networkParameter,
      networkResult,
      recentAnalyses,
      calculateRoute,
      analyzeArea,
      analyzeTerrain,
      analyzeNetwork,
      viewAnalysisRecord
    }
  }
}
</script>

<style scoped>
.analysis-tools {
  padding: 20px;
  max-width: 1200px;
  margin: 0 auto;
}

.page-header {
  text-align: center;
  margin-bottom: 40px;
  padding-bottom: 20px;
  border-bottom: 1px solid var(--border-light);
}

.page-header h1 {
  font-size: 28px;
  color: var(--text-primary);
  margin-bottom: 10px;
}

.page-header p {
  font-size: 16px;
  color: var(--text-secondary);
  margin: 0;
}

/* 工具分类 */
.tool-categories {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-bottom: 40px;
}

.category-card {
  background: var(--bg-primary);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
  padding: 24px;
  cursor: pointer;
  transition: all var(--transition-normal);
  border: 2px solid transparent;
}

.category-card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
  border-color: var(--primary-light);
}

.category-card.active {
  border-color: var(--primary-color);
  background: var(--primary-light);
}

.category-icon {
  font-size: 48px;
  margin-bottom: 16px;
}

.category-content h3 {
  font-size: 18px;
  color: var(--text-primary);
  margin-bottom: 8px;
}

.category-content p {
  font-size: 14px;
  color: var(--text-secondary);
  margin: 0;
}

/* 工具详情 */
.tool-details {
  background: var(--bg-primary);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
  padding: 24px;
  margin-bottom: 40px;
}

.tool-panel {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 30px;
}

.tool-form {
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
  padding: 24px;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  font-size: 14px;
  font-weight: 500;
  color: var(--text-primary);
  margin-bottom: 8px;
}

.form-group input,
.form-group select {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid var(--border-normal);
  border-radius: var(--radius-md);
  font-size: 14px;
  transition: border-color var(--transition-fast);
}

.form-group input:focus,
.form-group select:focus {
  border-color: var(--primary-color);
  outline: none;
}

.btn {
  display: inline-block;
  padding: 10px 24px;
  border: none;
  border-radius: var(--radius-md);
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all var(--transition-normal);
}

.btn-primary {
  background: var(--primary-color);
  color: white;
}

.btn-primary:hover {
  background: var(--primary-dark);
}

.tool-result {
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
  padding: 24px;
}

.tool-result h3 {
  font-size: 16px;
  color: var(--text-primary);
  margin-bottom: 20px;
}

.result-placeholder {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 60px 20px;
  background: var(--bg-primary);
  border-radius: var(--radius-md);
  border: 2px dashed var(--border-light);
}

.result-placeholder span {
  font-size: 64px;
  margin-bottom: 16px;
}

.result-placeholder p {
  font-size: 16px;
  color: var(--text-secondary);
  margin: 0;
  text-align: center;
}

.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 80px 20px;
  text-align: center;
}

.empty-state .empty-icon {
  font-size: 72px;
  margin-bottom: 20px;
}

.empty-state h3 {
  font-size: 20px;
  color: var(--text-primary);
  margin-bottom: 8px;
}

.empty-state p {
  font-size: 16px;
  color: var(--text-secondary);
  margin: 0;
}

/* 最近分析记录 */
.recent-analyses {
  background: var(--bg-primary);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
  padding: 24px;
}

.analysis-records {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.record-item {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 16px;
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
  transition: all var(--transition-fast);
}

.record-item:hover {
  background: var(--bg-tertiary);
  transform: translateX(4px);
}

.record-icon {
  font-size: 24px;
  width: 48px;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--bg-primary);
  border-radius: 50%;
}

.record-content {
  flex: 1;
}

.record-title {
  font-size: 14px;
  font-weight: 500;
  color: var(--text-primary);
  margin-bottom: 4px;
}

.record-meta {
  display: flex;
  gap: 12px;
  font-size: 12px;
  color: var(--text-light);
}

.record-action {
  padding: 6px 12px;
  border: 1px solid var(--primary-color);
  border-radius: var(--radius-sm);
  background: var(--bg-primary);
  color: var(--primary-color);
  font-size: 12px;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.record-action:hover {
  background: var(--primary-color);
  color: white;
}

/* 分析结果 */
.result-content {
  background: var(--bg-primary);
  border-radius: var(--radius-md);
  padding: 20px;
  border: 1px solid var(--border-light);
}

.result-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0;
  border-bottom: 1px solid var(--border-light);
}

.result-item:last-child {
  border-bottom: none;
}

.result-label {
  font-size: 14px;
  font-weight: 500;
  color: var(--text-secondary);
}

.result-value {
  font-size: 14px;
  font-weight: 500;
  color: var(--text-primary);
}

/* 空记录状态 */
.empty-records {
  text-align: center;
  padding: 40px 20px;
  color: var(--text-secondary);
  font-size: 14px;
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
  border: 1px dashed var(--border-light);
}

/* 响应式设计 */
@media (max-width: 768px) {
  .analysis-tools {
    padding: 10px;
  }

  .tool-categories {
    grid-template-columns: 1fr;
  }

  .tool-panel {
    grid-template-columns: 1fr;
  }

  .category-card {
    padding: 20px;
  }

  .tool-form,
  .tool-result {
    padding: 20px;
  }
}
</style>