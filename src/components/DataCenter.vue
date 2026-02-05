<template>
  <div class="data-center">
    <div class="page-header">
      <h1>数据中心</h1>
      <p>管理和分析地理信息数据</p>
    </div>

    <!-- 数据概览 -->
    <div class="data-overview">
      <div class="overview-card" v-for="(stat, index) in dataOverview" :key="index">
        <div class="overview-icon" :style="{ backgroundColor: stat.color + '20' }">
          <span :style="{ color: stat.color }">{{ stat.icon }}</span>
        </div>
        <div class="overview-content">
          <div class="overview-value">{{ stat.value }}</div>
          <div class="overview-label">{{ stat.label }}</div>
        </div>
      </div>
    </div>

    <!-- 数据管理 -->
    <div class="data-management">
      <div class="section-header">
        <h2>数据管理</h2>
        <div class="section-actions">
          <button class="btn btn-primary" @click="importData">
            <span>+</span> 导入数据
          </button>
          <button class="btn btn-secondary" @click="exportData">
            <span>⬇</span> 导出数据
          </button>
        </div>
      </div>

      <div class="data-tabs">
        <div 
          class="tab-item" 
          v-for="tab in dataTabs" 
          :key="tab.value"
          :class="{ active: activeDataTab === tab.value }"
          @click="activeDataTab = tab.value"
        >
          {{ tab.label }}
        </div>
      </div>

      <div class="data-table-container">
        <table class="data-table">
          <thead>
            <tr>
              <th>数据名称</th>
              <th>数据类型</th>
              <th>数据量</th>
              <th>更新时间</th>
              <th>状态</th>
              <th>操作</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in dataList" :key="index">
              <td>{{ item.name }}</td>
              <td>{{ item.type }}</td>
              <td>{{ item.size }}</td>
              <td>{{ item.updateTime }}</td>
              <td>
                <span class="status-badge" :class="item.status">{{ item.statusText }}</span>
              </td>
              <td>
                <div class="action-buttons">
                  <button class="action-btn view" @click="viewData(item)">查看</button>
                  <button class="action-btn edit" @click="editData(item)">编辑</button>
                  <button class="action-btn delete" @click="deleteData(item, index)">删除</button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- 数据可视化 -->
    <div class="data-visualization">
      <div class="section-header">
        <h2>数据可视化</h2>
      </div>
      <div class="visualization-cards">
        <div class="visualization-card">
          <h3>人口分布热力图</h3>
          <div class="visualization-placeholder">
            <span>📊</span>
            <p>人口密度热力图</p>
          </div>
        </div>
        <div class="visualization-card">
          <h3>经济指标趋势</h3>
          <div class="visualization-placeholder">
            <span>📈</span>
            <p>GDP增长趋势图</p>
          </div>
        </div>
        <div class="visualization-card">
          <h3>交通流量分析</h3>
          <div class="visualization-placeholder">
            <span>🚗</span>
            <p>交通流量分布图</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: 'DataCenter',
  setup() {
    const activeDataTab = ref('all')

    // 数据概览
    const dataOverview = [
      { icon: '📊', value: '1,245', label: '数据集', color: '#409eff' },
      { icon: '📍', value: '8,762', label: '地理点位', color: '#67c23a' },
      { icon: '📅', value: '32', label: '最近更新', color: '#e6a23c' },
      { icon: '🔍', value: '98%', label: '数据质量', color: '#f56c6c' }
    ]

    // 数据标签页
    const dataTabs = [
      { label: '全部数据', value: 'all' },
      { label: '人口数据', value: 'population' },
      { label: '经济数据', value: 'economy' },
      { label: '交通数据', value: 'traffic' },
      { label: '环境数据', value: 'environment' }
    ]

    // 数据列表
    const dataList = ref([
      {
        name: '北京市人口分布',
        type: '人口数据',
        size: '1.2 MB',
        updateTime: '2026-02-01',
        status: 'normal',
        statusText: '正常'
      },
      {
        name: '上海市经济指标',
        type: '经济数据',
        size: '856 KB',
        updateTime: '2026-02-02',
        status: 'normal',
        statusText: '正常'
      },
      {
        name: '广州市交通流量',
        type: '交通数据',
        size: '2.3 MB',
        updateTime: '2026-02-03',
        status: 'warning',
        statusText: '需要更新'
      },
      {
        name: '深圳市环境监测',
        type: '环境数据',
        size: '1.8 MB',
        updateTime: '2026-02-04',
        status: 'normal',
        statusText: '正常'
      },
      {
        name: '杭州市城市规划',
        type: '规划数据',
        size: '3.1 MB',
        updateTime: '2026-01-30',
        status: 'error',
        statusText: '数据异常'
      }
    ])

    // 筛选后的数据列表
    const filteredDataList = computed(() => {
      if (activeDataTab.value === 'all') {
        return dataList.value
      } else {
        const typeMap = {
          population: '人口数据',
          economy: '经济数据',
          traffic: '交通数据',
          environment: '环境数据'
        }
        const targetType = typeMap[activeDataTab.value]
        return dataList.value.filter(item => item.type === targetType)
      }
    })

    // 导入数据功能
    const importData = () => {
      // 创建文件输入元素
      const input = document.createElement('input')
      input.type = 'file'
      input.accept = '.csv,.json,.xlsx,.geojson'
      
      input.onchange = (e) => {
        const file = e.target.files[0]
        if (file) {
          console.log('导入文件:', file)
          
          // 根据文件类型选择不同的读取方式
          const reader = new FileReader()
          
          reader.onload = (event) => {
            try {
              if (file.name.endsWith('.json') || file.name.endsWith('.geojson')) {
                // 解析JSON文件
                const parsedData = JSON.parse(event.target.result)
                console.log('解析的数据:', parsedData)
                
                // 如果是GeoJSON格式，转换为数据集格式
                if (parsedData.type === 'FeatureCollection') {
                  const newItem = {
                    name: file.name.replace('.geojson', ''),
                    type: '地理数据',
                    size: (JSON.stringify(parsedData).length / 1024).toFixed(2) + ' KB',
                    updateTime: new Date().toISOString().split('T')[0],
                    status: 'normal',
                    statusText: '正常'
                  }
                  dataList.value.push(newItem)
                  alert(`成功导入GeoJSON数据: ${file.name}\n添加了新的数据集`)
                } else if (Array.isArray(parsedData)) {
                  // 如果是数据数组，批量添加
                  parsedData.forEach((item, index) => {
                    if (item.name && item.type) {
                      dataList.value.push({
                        ...item,
                        updateTime: item.updateTime || new Date().toISOString().split('T')[0],
                        status: item.status || 'normal',
                        statusText: item.statusText || '正常'
                      })
                    }
                  })
                  alert(`成功导入JSON数据: ${file.name}\n添加了 ${parsedData.length} 个数据集`)
                } else {
                  alert(`成功导入文件: ${file.name}\n文件已就绪`)
                }
              } else if (file.name.endsWith('.csv')) {
                // 解析CSV文件
                const csvContent = event.target.result
                const lines = csvContent.split('\n')
                const headers = lines[0].split(',')
                const data = []
                
                for (let i = 1; i < lines.length; i++) {
                  if (lines[i].trim()) {
                    const values = lines[i].split(',')
                    const row = {}
                    headers.forEach((header, index) => {
                      row[header.trim()] = values[index]?.trim()
                    })
                    data.push(row)
                  }
                }
                
                if (data.length > 0) {
                  const newItem = {
                    name: file.name.replace('.csv', ''),
                    type: '表格数据',
                    size: (csvContent.length / 1024).toFixed(2) + ' KB',
                    updateTime: new Date().toISOString().split('T')[0],
                    status: 'normal',
                    statusText: '正常'
                  }
                  dataList.value.push(newItem)
                  alert(`成功导入CSV数据: ${file.name}\n包含 ${data.length} 条记录`)
                }
              } else {
                // 其他文件类型
                alert(`成功导入文件: ${file.name}\n文件大小: ${(file.size / 1024).toFixed(2)} KB`)
              }
            } catch (error) {
              console.error('文件解析错误:', error)
              alert(`文件解析失败: ${error.message}`)
            }
          }
          
          reader.onerror = () => {
            alert('文件读取失败')
          }
          
          reader.readAsText(file)
        }
      }
      
      input.click()
    }

    // 导出数据功能
    const exportData = () => {
      // 模拟导出数据
      const exportData = {
        timestamp: new Date().toISOString(),
        data: dataList.value,
        total: dataList.value.length
      }
      
      const blob = new Blob([JSON.stringify(exportData, null, 2)], {
        type: 'application/json'
      })
      
      const url = URL.createObjectURL(blob)
      const a = document.createElement('a')
      a.href = url
      a.download = `geodata-${new Date().toISOString().split('T')[0]}.json`
      a.click()
      URL.revokeObjectURL(url)
      
      alert('数据导出成功')
    }

    // 查看数据功能
    const viewData = (item) => {
      console.log('查看数据:', item)
      alert(`查看数据: ${item.name}\n类型: ${item.type}\n大小: ${item.size}\n更新时间: ${item.updateTime}\n状态: ${item.statusText}`)
    }

    // 编辑数据功能
    const editData = (item) => {
      console.log('编辑数据:', item)
      const newName = prompt('请输入新的数据名称:', item.name)
      if (newName && newName.trim() !== '') {
        item.name = newName.trim()
        
        // 允许编辑其他字段
        const newType = prompt('请输入新的数据类型:', item.type)
        if (newType && newType.trim() !== '') {
          item.type = newType.trim()
        }
        
        const newSize = prompt('请输入新的文件大小:', item.size)
        if (newSize && newSize.trim() !== '') {
          item.size = newSize.trim()
        }
        
        item.updateTime = new Date().toISOString().split('T')[0]
        item.status = 'normal'
        item.statusText = '正常'
        
        alert('数据编辑成功')
      }
    }

    // 删除数据功能
    const deleteData = (item, index) => {
      if (confirm(`确定要删除数据: ${item.name}吗？`)) {
        dataList.value.splice(index, 1)
        alert('数据删除成功')
      }
    }

    return {
      activeDataTab,
      dataOverview,
      dataTabs,
      dataList: filteredDataList,
      importData,
      exportData,
      viewData,
      editData,
      deleteData
    }
  }
}
</script>

<style scoped>
.data-center {
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

/* 数据概览 */
.data-overview {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
  margin-bottom: 40px;
}

.overview-card {
  background: var(--bg-primary);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
  padding: 20px;
  display: flex;
  align-items: center;
  gap: 16px;
  transition: all var(--transition-normal);
}

.overview-card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

.overview-icon {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
}

.overview-content {
  flex: 1;
}

.overview-value {
  font-size: 20px;
  font-weight: bold;
  color: var(--text-primary);
  margin-bottom: 4px;
}

.overview-label {
  font-size: 14px;
  color: var(--text-secondary);
}

/* 数据管理 */
.data-management {
  background: var(--bg-primary);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
  padding: 24px;
  margin-bottom: 40px;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
  padding-bottom: 16px;
  border-bottom: 1px solid var(--border-light);
}

.section-header h2 {
  font-size: 18px;
  color: var(--text-primary);
  margin: 0;
}

.section-actions {
  display: flex;
  gap: 12px;
}

.btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  border: none;
  border-radius: var(--radius-md);
  font-size: 14px;
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

.btn-secondary {
  background: var(--bg-secondary);
  color: var(--text-primary);
  border: 1px solid var(--border-normal);
}

.btn-secondary:hover {
  background: var(--bg-tertiary);
}

/* 数据标签页 */
.data-tabs {
  display: flex;
  gap: 4px;
  margin-bottom: 24px;
  border-bottom: 1px solid var(--border-light);
}

.tab-item {
  padding: 12px 20px;
  border-bottom: 2px solid transparent;
  cursor: pointer;
  font-size: 14px;
  color: var(--text-secondary);
  transition: all var(--transition-fast);
}

.tab-item:hover {
  color: var(--primary-color);
}

.tab-item.active {
  color: var(--primary-color);
  border-bottom-color: var(--primary-color);
  font-weight: 500;
}

/* 数据表格 */
.data-table-container {
  overflow-x: auto;
}

.data-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 14px;
}

.data-table th,
.data-table td {
  padding: 12px 16px;
  text-align: left;
  border-bottom: 1px solid var(--border-light);
}

.data-table th {
  background-color: var(--bg-tertiary);
  font-weight: 600;
  color: var(--text-primary);
  white-space: nowrap;
}

.data-table tr:hover {
  background-color: var(--bg-secondary);
}

.status-badge {
  padding: 4px 12px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: 500;
}

.status-badge.normal {
  background: #f0f9eb;
  color: var(--success-color);
}

.status-badge.warning {
  background: #fdf6ec;
  color: var(--warning-color);
}

.status-badge.error {
  background: #fef0f0;
  color: var(--danger-color);
}

.action-buttons {
  display: flex;
  gap: 8px;
}

.action-btn {
  padding: 4px 10px;
  border: 1px solid var(--border-normal);
  border-radius: var(--radius-sm);
  background: var(--bg-primary);
  font-size: 12px;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.action-btn.view {
  color: var(--primary-color);
  border-color: var(--primary-light);
}

.action-btn.view:hover {
  background: var(--primary-light);
}

.action-btn.edit {
  color: var(--warning-color);
  border-color: #fdf6ec;
}

.action-btn.edit:hover {
  background: #fdf6ec;
}

.action-btn.delete {
  color: var(--danger-color);
  border-color: #fef0f0;
}

.action-btn.delete:hover {
  background: #fef0f0;
}

/* 数据可视化 */
.data-visualization {
  background: var(--bg-primary);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
  padding: 24px;
}

.visualization-cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 24px;
}

.visualization-card {
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
  padding: 20px;
  text-align: center;
  transition: all var(--transition-normal);
}

.visualization-card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-sm);
}

.visualization-card h3 {
  font-size: 16px;
  color: var(--text-primary);
  margin-bottom: 20px;
}

.visualization-placeholder {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px 20px;
  background: var(--bg-primary);
  border-radius: var(--radius-md);
  border: 2px dashed var(--border-light);
}

.visualization-placeholder span {
  font-size: 48px;
  margin-bottom: 16px;
}

.visualization-placeholder p {
  font-size: 14px;
  color: var(--text-secondary);
  margin: 0;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .data-center {
    padding: 10px;
  }

  .data-overview {
    grid-template-columns: 1fr;
  }

  .visualization-cards {
    grid-template-columns: 1fr;
  }

  .section-header {
    flex-direction: column;
    align-items: stretch;
    gap: 12px;
  }

  .section-actions {
    justify-content: center;
  }

  .data-tabs {
    overflow-x: auto;
    white-space: nowrap;
    padding-bottom: 8px;
  }

  .tab-item {
    white-space: nowrap;
  }
}
</style>