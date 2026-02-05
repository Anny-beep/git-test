<template>
  <div class="chart-container">
    <div class="chart-header">
      <h3>{{ title }}</h3>
      <select v-model="selectedDataType" class="data-select">
        <option v-for="type in dataTypes" :key="type.value" :value="type.value">
          {{ type.label }}
        </option>
      </select>
    </div>
    <div class="chart-content">
      <div class="data-stats">
        <div class="stat-item" v-for="(stat, index) in statistics" :key="index">
          <div class="stat-value">{{ stat.value }}</div>
          <div class="stat-label">{{ stat.label }}</div>
        </div>
      </div>
      <div class="data-table">
        <table>
          <thead>
            <tr>
              <th v-for="column in tableColumns" :key="column.key">
                {{ column.label }}
              </th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in tableData" :key="index">
              <td v-for="column in tableColumns" :key="column.key">
                {{ item[column.key] }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, watch } from 'vue'

export default {
  name: 'DataChart',
  props: {
    title: {
      type: String,
      default: '地理数据统计'
    },
    data: {
      type: Array,
      default: () => []
    }
  },
  setup(props) {
    const selectedDataType = ref('population')

    // 数据类型选项
    const dataTypes = [
      { label: '人口分布', value: 'population' },
      { label: '经济指标', value: 'economy' },
      { label: '交通流量', value: 'traffic' },
      { label: '环境监测', value: 'environment' }
    ]

    // 表格列定义
    const tableColumns = [
      { key: 'name', label: '区域名称' },
      { key: 'value', label: '数据值' },
      { key: 'unit', label: '单位' },
      { key: 'updateTime', label: '更新时间' }
    ]

    // 模拟表格数据
    const tableData = computed(() => {
      return props.data.length > 0 ? props.data : [
        { name: '北京市', value: '2189', unit: '万人', updateTime: '2023-12-31' },
        { name: '上海市', value: '2487', unit: '万人', updateTime: '2023-12-31' },
        { name: '广州市', value: '1530', unit: '万人', updateTime: '2023-12-31' },
        { name: '深圳市', value: '1756', unit: '万人', updateTime: '2023-12-31' },
        { name: '杭州市', value: '1220', unit: '万人', updateTime: '2023-12-31' }
      ]
    })

    // 统计数据
    const statistics = computed(() => {
      const data = tableData.value
      const total = data.reduce((sum, item) => sum + parseFloat(item.value), 0)
      const average = total / data.length
      const max = Math.max(...data.map(item => parseFloat(item.value)))
      const min = Math.min(...data.map(item => parseFloat(item.value)))

      return [
        { label: '总量', value: total.toFixed(1) },
        { label: '平均值', value: average.toFixed(1) },
        { label: '最大值', value: max.toFixed(1) },
        { label: '最小值', value: min.toFixed(1) }
      ]
    })

    // 监听数据类型变化
    watch(selectedDataType, (newType) => {
      console.log('切换数据类型:', newType)
      // 这里可以根据数据类型切换不同的数据源
    })

    return {
      selectedDataType,
      dataTypes,
      tableColumns,
      tableData,
      statistics
    }
  }
}
</script>

<style scoped>
.chart-container {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
  padding: 20px;
  margin-top: 20px;
}

.chart-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding-bottom: 10px;
  border-bottom: 1px solid #eee;
}

.chart-header h3 {
  margin: 0;
  font-size: 18px;
  color: #333;
  font-weight: 600;
}

.data-select {
  padding: 6px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
  background: white;
  cursor: pointer;
  transition: border-color 0.3s ease;
}

.data-select:hover {
  border-color: #409eff;
}

.chart-content {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.data-stats {
  display: flex;
  gap: 20px;
  flex-wrap: wrap;
}

.stat-item {
  flex: 1;
  min-width: 120px;
  background: #f8f9fa;
  padding: 16px;
  border-radius: 6px;
  text-align: center;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.stat-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.stat-value {
  font-size: 24px;
  font-weight: bold;
  color: #409eff;
  margin-bottom: 4px;
}

.stat-label {
  font-size: 14px;
  color: #666;
}

.data-table {
  overflow-x: auto;
}

.data-table table {
  width: 100%;
  border-collapse: collapse;
  font-size: 14px;
}

.data-table th,
.data-table td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #eee;
}

.data-table th {
  background-color: #f8f9fa;
  font-weight: 600;
  color: #333;
  white-space: nowrap;
}

.data-table tr:hover {
  background-color: #f5f7fa;
}

.data-table tr:nth-child(even) {
  background-color: #fafafa;
}
</style>