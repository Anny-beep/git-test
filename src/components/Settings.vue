<template>
  <div class="settings">
    <div class="page-header">
      <h1>系统设置</h1>
      <p>配置平台的各项参数和功能</p>
    </div>

    <!-- 设置导航 -->
    <div class="settings-nav">
      <div 
        class="nav-item" 
        v-for="nav in settingsNav" 
        :key="nav.value"
        :class="{ active: activeSettingsTab === nav.value }"
        @click="activeSettingsTab = nav.value"
      >
        <div class="nav-icon">{{ nav.icon }}</div>
        <div class="nav-label">{{ nav.label }}</div>
      </div>
    </div>

    <!-- 设置内容 -->
    <div class="settings-content">
      <div class="section-header">
        <h2>{{ currentNav?.label || '系统设置' }}</h2>
      </div>

      <!-- 地图设置 -->
      <div v-if="activeSettingsTab === 'map'" class="settings-panel">
        <form class="settings-form">
          <div class="form-section">
            <h3>API 配置</h3>
            <div class="form-group">
              <label>高德地图 API 密钥</label>
              <input 
                type="text" 
                v-model="mapSettings.apiKey" 
                placeholder="输入高德地图 API 密钥"
              />
              <p class="form-hint">获取 API 密钥: <a href="https://lbs.amap.com/dev/key/app" target="_blank">https://lbs.amap.com/dev/key/app</a></p>
            </div>
          </div>

          <div class="form-section">
            <h3>默认设置</h3>
            <div class="form-group">
              <label>默认中心点</label>
              <div class="coordinate-inputs">
                <input 
                  type="number" 
                  v-model="mapSettings.center[0]" 
                  placeholder="经度"
                  step="0.000001"
                />
                <input 
                  type="number" 
                  v-model="mapSettings.center[1]" 
                  placeholder="纬度"
                  step="0.000001"
                />
              </div>
            </div>
            <div class="form-group">
              <label>默认缩放级别</label>
              <input 
                type="number" 
                v-model="mapSettings.zoom" 
                min="1" 
                max="20"
                step="1"
              />
            </div>
            <div class="form-group">
              <label>默认地图样式</label>
              <select v-model="mapSettings.mapStyle">
                <option value="amap://styles/normal">标准地图</option>
                <option value="amap://styles/dark">暗色地图</option>
                <option value="amap://styles/light">浅色地图</option>
                <option value="amap://styles/whitesmoke">白烟地图</option>
              </select>
            </div>
          </div>

          <div class="form-section">
            <h3>控件设置</h3>
            <div class="form-group checkbox-group">
              <label>
                <input type="checkbox" v-model="mapSettings.controls.toolBar" />
                工具栏
              </label>
            </div>
            <div class="form-group checkbox-group">
              <label>
                <input type="checkbox" v-model="mapSettings.controls.scale" />
                比例尺
              </label>
            </div>
            <div class="form-group checkbox-group">
              <label>
                <input type="checkbox" v-model="mapSettings.controls.hawkEye" />
                鹰眼
              </label>
            </div>
          </div>
        </form>
      </div>

      <!-- 系统设置 -->
      <div v-else-if="activeSettingsTab === 'system'" class="settings-panel">
        <form class="settings-form">
          <div class="form-section">
            <h3>基本设置</h3>
            <div class="form-group">
              <label>平台名称</label>
              <input 
                type="text" 
                v-model="systemSettings.platformName" 
                placeholder="输入平台名称"
              />
            </div>
            <div class="form-group">
              <label>系统语言</label>
              <select v-model="systemSettings.language">
                <option value="zh-CN">简体中文</option>
                <option value="en-US">English</option>
              </select>
            </div>
          </div>

          <div class="form-section">
            <h3>外观设置</h3>
            <div class="form-group">
              <label>主题设置</label>
              <div class="theme-options">
                <div 
                  class="theme-option" 
                  :class="{ active: systemSettings.theme === 'light' }"
                  @click="systemSettings.theme = 'light'"
                >
                  <div class="theme-preview light"></div>
                  <span>浅色主题</span>
                </div>
                <div 
                  class="theme-option" 
                  :class="{ active: systemSettings.theme === 'dark' }"
                  @click="systemSettings.theme = 'dark'"
                >
                  <div class="theme-preview dark"></div>
                  <span>深色主题</span>
                </div>
                <div 
                  class="theme-option" 
                  :class="{ active: systemSettings.theme === 'auto' }"
                  @click="systemSettings.theme = 'auto'"
                >
                  <div class="theme-preview auto"></div>
                  <span>跟随系统</span>
                </div>
              </div>
            </div>
          </div>

          <div class="form-section">
            <h3>性能设置</h3>
            <div class="form-group">
              <label>数据缓存</label>
              <select v-model="systemSettings.cache">
                <option value="enabled">启用</option>
                <option value="disabled">禁用</option>
              </select>
            </div>
            <div class="form-group">
              <label>缓存大小限制 (MB)</label>
              <input 
                type="number" 
                v-model="systemSettings.cacheSize" 
                min="10" 
                max="500"
                step="10"
              />
            </div>
          </div>
        </form>
      </div>

      <!-- 用户设置 -->
      <div v-else-if="activeSettingsTab === 'user'" class="settings-panel">
        <form class="settings-form">
          <div class="form-section">
            <h3>用户信息</h3>
            <div class="form-group">
              <label>用户名</label>
              <input 
                type="text" 
                v-model="userSettings.username" 
                placeholder="输入用户名"
              />
            </div>
            <div class="form-group">
              <label>电子邮箱</label>
              <input 
                type="email" 
                v-model="userSettings.email" 
                placeholder="输入电子邮箱"
              />
            </div>
            <div class="form-group">
              <label>联系电话</label>
              <input 
                type="tel" 
                v-model="userSettings.phone" 
                placeholder="输入联系电话"
              />
            </div>
          </div>

          <div class="form-section">
            <h3>密码设置</h3>
            <div class="form-group">
              <label>当前密码</label>
              <input 
                type="password" 
                v-model="userSettings.currentPassword" 
                placeholder="输入当前密码"
              />
            </div>
            <div class="form-group">
              <label>新密码</label>
              <input 
                type="password" 
                v-model="userSettings.newPassword" 
                placeholder="输入新密码"
              />
            </div>
            <div class="form-group">
              <label>确认新密码</label>
              <input 
                type="password" 
                v-model="userSettings.confirmPassword" 
                placeholder="确认新密码"
              />
            </div>
          </div>

          <div class="form-section">
            <h3>通知设置</h3>
            <div class="form-group checkbox-group">
              <label>
                <input type="checkbox" v-model="userSettings.notifications.email" />
                邮件通知
              </label>
            </div>
            <div class="form-group checkbox-group">
              <label>
                <input type="checkbox" v-model="userSettings.notifications.sms" />
                短信通知
              </label>
            </div>
            <div class="form-group checkbox-group">
              <label>
                <input type="checkbox" v-model="userSettings.notifications.system" />
                系统通知
              </label>
            </div>
          </div>
        </form>
      </div>

      <!-- API 设置 -->
      <div v-else-if="activeSettingsTab === 'api'" class="settings-panel">
        <form class="settings-form">
          <div class="form-section">
            <h3>API 服务</h3>
            <div class="api-service-item" v-for="(service, index) in apiSettings.services" :key="index">
              <div class="service-header">
                <h4>{{ service.name }}</h4>
                <div class="service-status" :class="service.enabled ? 'enabled' : 'disabled'">
                  {{ service.enabled ? '已启用' : '已禁用' }}
                </div>
              </div>
              <div class="service-config">
                <div class="form-group">
                  <label>API 地址</label>
                  <input 
                    type="text" 
                    v-model="service.url" 
                    placeholder="输入 API 地址"
                  />
                </div>
                <div class="form-group">
                  <label>API 密钥</label>
                  <input 
                    type="text" 
                    v-model="service.apiKey" 
                    placeholder="输入 API 密钥"
                  />
                </div>
                <div class="form-group checkbox-group">
                  <label>
                    <input type="checkbox" v-model="service.enabled" />
                    启用此服务
                  </label>
                </div>
              </div>
            </div>
          </div>

          <div class="form-section">
            <h3>API 限制</h3>
            <div class="form-group">
              <label>请求频率限制 (次/分钟)</label>
              <input 
                type="number" 
                v-model="apiSettings.rateLimit" 
                min="1" 
                max="1000"
                step="1"
              />
            </div>
            <div class="form-group">
              <label>超时设置 (秒)</label>
              <input 
                type="number" 
                v-model="apiSettings.timeout" 
                min="1" 
                max="60"
                step="1"
              />
            </div>
          </div>
        </form>
      </div>

      <!-- 默认提示 -->
      <div v-else class="settings-panel">
        <div class="empty-state">
          <div class="empty-icon">⚙️</div>
          <h3>选择设置类别</h3>
          <p>从左侧选择一个设置类别进行配置</p>
        </div>
      </div>

      <!-- 操作按钮 -->
      <div class="settings-actions">
        <button class="btn btn-primary" @click="saveSettings">
          <span>💾</span> 保存设置
        </button>
        <button class="btn btn-secondary" @click="resetSettings">
          <span>🔄</span> 重置为默认值
        </button>
        <button class="btn btn-success" @click="importSettings">
          <span>📥</span> 导入设置
        </button>
        <button class="btn btn-danger" @click="exportSettings">
          <span>📤</span> 导出设置
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, watch } from 'vue'
import { mapConfig } from '../config/map'

export default {
  name: 'Settings',
  setup() {
    const activeSettingsTab = ref('map')

    // 设置导航
    const settingsNav = [
      {
        value: 'map',
        label: '地图设置',
        icon: '🗺️'
      },
      {
        value: 'system',
        label: '系统设置',
        icon: '⚙️'
      },
      {
        value: 'user',
        label: '用户设置',
        icon: '👤'
      },
      {
        value: 'api',
        label: 'API 设置',
        icon: '🔌'
      }
    ]

    // 当前导航
    const currentNav = ref(settingsNav[0])

    // 地图设置
    const mapSettings = ref({
      apiKey: mapConfig.key,
      center: [...mapConfig.center],
      zoom: mapConfig.zoom,
      mapStyle: mapConfig.mapStyle,
      controls: {
        toolBar: true,
        scale: true,
        hawkEye: true
      }
    })

    // 系统设置
    const systemSettings = ref({
      platformName: '地理信息数据平台',
      language: 'zh-CN',
      theme: 'light',
      cache: 'enabled',
      cacheSize: 100
    })

    // 用户设置
    const userSettings = ref({
      username: 'admin',
      email: 'admin@example.com',
      phone: '',
      currentPassword: '',
      newPassword: '',
      confirmPassword: '',
      notifications: {
        email: true,
        sms: false,
        system: true
      }
    })

    // API 设置
    const apiSettings = ref({
      services: [
        {
          name: '高德地图 API',
          url: 'https://restapi.amap.com',
          apiKey: mapConfig.key,
          enabled: true
        },
        {
          name: '自定义数据 API',
          url: '',
          apiKey: '',
          enabled: false
        }
      ],
      rateLimit: 60,
      timeout: 30
    })

    // 从本地存储加载设置
    const loadSettings = () => {
      try {
        const savedSettings = localStorage.getItem('geoPlatformSettings')
        if (savedSettings) {
          const parsedSettings = JSON.parse(savedSettings)
          
          // 加载地图设置
          if (parsedSettings.mapSettings) {
            mapSettings.value = { ...mapSettings.value, ...parsedSettings.mapSettings }
            // 确保center是数组
            if (parsedSettings.mapSettings.center) {
              mapSettings.value.center = [...parsedSettings.mapSettings.center]
            }
          }
          
          // 加载系统设置
          if (parsedSettings.systemSettings) {
            systemSettings.value = { ...systemSettings.value, ...parsedSettings.systemSettings }
          }
          
          // 加载用户设置
          if (parsedSettings.userSettings) {
            userSettings.value = { ...userSettings.value, ...parsedSettings.userSettings }
          }
          
          // 加载API设置
          if (parsedSettings.apiSettings) {
            apiSettings.value = { ...apiSettings.value, ...parsedSettings.apiSettings }
          }
          
          console.log('设置已从本地存储加载')
        }
      } catch (error) {
        console.error('加载设置失败:', error)
      }
    }

    // 保存设置到本地存储
    const saveSettingsToLocalStorage = () => {
      try {
        const settings = {
          mapSettings: mapSettings.value,
          systemSettings: systemSettings.value,
          userSettings: {
            ...userSettings.value,
            // 不保存密码信息
            currentPassword: '',
            newPassword: '',
            confirmPassword: ''
          },
          apiSettings: apiSettings.value
        }
        
        localStorage.setItem('geoPlatformSettings', JSON.stringify(settings, null, 2))
        console.log('设置已保存到本地存储')
      } catch (error) {
        console.error('保存设置失败:', error)
        throw error
      }
    }

    // 保存设置
    const saveSettings = () => {
      try {
        // 验证设置
        if (!mapSettings.value.apiKey || mapSettings.value.apiKey === '您的高德地图API密钥') {
          alert('请输入有效的高德地图API密钥')
          return
        }
        
        // 验证密码设置
        if (userSettings.value.newPassword) {
          if (userSettings.value.newPassword !== userSettings.value.confirmPassword) {
            alert('两次输入的密码不一致')
            return
          }
        }
        
        // 保存到本地存储
        saveSettingsToLocalStorage()
        
        // 应用主题设置
        applyTheme()
        
        alert('设置已保存')
      } catch (error) {
        console.error('保存设置失败:', error)
        alert('保存设置失败，请重试')
      }
    }

    // 重置设置
    const resetSettings = () => {
      if (confirm('确定要重置为默认设置吗？')) {
        try {
          // 重置地图设置
          mapSettings.value = {
            apiKey: '您的高德地图API密钥',
            center: [116.397428, 39.90923],
            zoom: 13,
            mapStyle: 'amap://styles/normal',
            controls: {
              toolBar: true,
              scale: true,
              hawkEye: true
            }
          }
          
          // 重置系统设置
          systemSettings.value = {
            platformName: '地理信息数据平台',
            language: 'zh-CN',
            theme: 'light',
            cache: 'enabled',
            cacheSize: 100
          }
          
          // 重置用户设置
          userSettings.value = {
            username: 'admin',
            email: 'admin@example.com',
            phone: '',
            currentPassword: '',
            newPassword: '',
            confirmPassword: '',
            notifications: {
              email: true,
              sms: false,
              system: true
            }
          }
          
          // 重置API设置
          apiSettings.value = {
            services: [
              {
                name: '高德地图 API',
                url: 'https://restapi.amap.com',
                apiKey: '您的高德地图API密钥',
                enabled: true
              },
              {
                name: '自定义数据 API',
                url: '',
                apiKey: '',
                enabled: false
              }
            ],
            rateLimit: 60,
            timeout: 30
          }
          
          // 保存重置后的设置
          saveSettingsToLocalStorage()
          
          // 应用主题设置
          applyTheme()
          
          alert('设置已重置为默认值')
        } catch (error) {
          console.error('重置设置失败:', error)
          alert('重置设置失败，请重试')
        }
      }
    }

    // 导出设置
    const exportSettings = () => {
      try {
        const settings = {
          mapSettings: mapSettings.value,
          systemSettings: systemSettings.value,
          userSettings: {
            ...userSettings.value,
            // 不导出密码信息
            currentPassword: '',
            newPassword: '',
            confirmPassword: ''
          },
          apiSettings: apiSettings.value
        }
        
        const blob = new Blob([JSON.stringify(settings, null, 2)], {
          type: 'application/json'
        })
        
        const url = URL.createObjectURL(blob)
        const a = document.createElement('a')
        a.href = url
        a.download = `settings-${new Date().toISOString().split('T')[0]}.json`
        a.click()
        URL.revokeObjectURL(url)
        
        alert('设置已导出')
      } catch (error) {
        console.error('导出设置失败:', error)
        alert('导出设置失败，请重试')
      }
    }

    // 导入设置
    const importSettings = () => {
      try {
        const input = document.createElement('input')
        input.type = 'file'
        input.accept = '.json'
        
        input.onchange = (e) => {
          const file = e.target.files[0]
          if (file) {
            const reader = new FileReader()
            
            reader.onload = (event) => {
              try {
                const importedSettings = JSON.parse(event.target.result)
                
                // 验证导入的数据结构
                if (!importedSettings.mapSettings || !importedSettings.systemSettings) {
                  alert('导入的设置文件格式不正确')
                  return
                }
                
                // 应用导入的设置
                if (importedSettings.mapSettings) {
                  mapSettings.value = { ...mapSettings.value, ...importedSettings.mapSettings }
                  if (importedSettings.mapSettings.center) {
                    mapSettings.value.center = [...importedSettings.mapSettings.center]
                  }
                }
                
                if (importedSettings.systemSettings) {
                  systemSettings.value = { ...systemSettings.value, ...importedSettings.systemSettings }
                }
                
                if (importedSettings.userSettings) {
                  userSettings.value = { ...userSettings.value, ...importedSettings.userSettings }
                }
                
                if (importedSettings.apiSettings) {
                  apiSettings.value = { ...apiSettings.value, ...importedSettings.apiSettings }
                }
                
                // 保存导入的设置
                saveSettingsToLocalStorage()
                
                // 应用主题设置
                applyTheme()
                
                alert('设置已导入')
              } catch (error) {
                console.error('解析设置文件失败:', error)
                alert('解析设置文件失败，请确保文件格式正确')
              }
            }
            
            reader.onerror = () => {
              alert('读取文件失败')
            }
            
            reader.readAsText(file)
          }
        }
        
        input.click()
      } catch (error) {
        console.error('导入设置失败:', error)
        alert('导入设置失败，请重试')
      }
    }

    // 应用主题设置
    const applyTheme = () => {
      const theme = systemSettings.value.theme
      
      // 移除现有的主题类
      document.body.classList.remove('theme-light', 'theme-dark')
      
      if (theme === 'dark') {
        document.body.classList.add('theme-dark')
      } else if (theme === 'auto') {
        // 检测系统主题
        const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches
        if (prefersDark) {
          document.body.classList.add('theme-dark')
        } else {
          document.body.classList.add('theme-light')
        }
      } else {
        document.body.classList.add('theme-light')
      }
    }

    // 监听设置变化
    watch(
      [mapSettings, systemSettings, userSettings, apiSettings],
      () => {
        // 可以在这里添加实时预览逻辑
      },
      { deep: true }
    )

    // 初始化
    onMounted(() => {
      // 加载保存的设置
      loadSettings()
      
      // 应用主题
      applyTheme()
      
      // 监听系统主题变化
      window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', () => {
        if (systemSettings.value.theme === 'auto') {
          applyTheme()
        }
      })
    })

    return {
      activeSettingsTab,
      settingsNav,
      currentNav,
      mapSettings,
      systemSettings,
      userSettings,
      apiSettings,
      saveSettings,
      resetSettings,
      exportSettings,
      importSettings
    }
  }
}
</script>

<style scoped>
.settings {
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

/* 设置导航 */
.settings-nav {
  display: flex;
  gap: 4px;
  margin-bottom: 40px;
  border-bottom: 1px solid var(--border-light);
  overflow-x: auto;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 24px;
  cursor: pointer;
  border-bottom: 2px solid transparent;
  transition: all var(--transition-fast);
  white-space: nowrap;
}

.nav-item:hover {
  color: var(--primary-color);
  background-color: var(--bg-secondary);
}

.nav-item.active {
  color: var(--primary-color);
  border-bottom-color: var(--primary-color);
  font-weight: 500;
  background-color: var(--bg-secondary);
}

.nav-icon {
  font-size: 16px;
}

.nav-label {
  font-size: 14px;
}

/* 设置内容 */
.settings-content {
  background: var(--bg-primary);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
  padding: 24px;
}

.settings-panel {
  margin-bottom: 40px;
}

.settings-form {
  display: flex;
  flex-direction: column;
  gap: 30px;
}

.form-section {
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
  padding: 20px;
}

.form-section h3 {
  font-size: 16px;
  color: var(--text-primary);
  margin-bottom: 20px;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--border-light);
}

.form-group {
  margin-bottom: 16px;
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

.checkbox-group {
  display: flex;
  align-items: center;
  gap: 8px;
}

.checkbox-group input[type="checkbox"] {
  width: auto;
  margin: 0;
}

.checkbox-group label {
  margin: 0;
  cursor: pointer;
}

/* 坐标输入 */
.coordinate-inputs {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

/* 主题选项 */
.theme-options {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  gap: 16px;
}

.theme-option {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  padding: 16px;
  border: 2px solid var(--border-light);
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: all var(--transition-fast);
}

.theme-option:hover {
  border-color: var(--primary-color);
}

.theme-option.active {
  border-color: var(--primary-color);
  background-color: var(--primary-light);
}

.theme-preview {
  width: 60px;
  height: 60px;
  border-radius: var(--radius-sm);
  border: 1px solid var(--border-normal);
}

.theme-preview.light {
  background: white;
}

.theme-preview.dark {
  background: #333;
}

.theme-preview.auto {
  background: linear-gradient(90deg, white 50%, #333 50%);
}

/* API 服务 */
.api-service-item {
  background: var(--bg-primary);
  border-radius: var(--radius-md);
  padding: 16px;
  margin-bottom: 16px;
  border: 1px solid var(--border-light);
}

.service-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--border-light);
}

.service-header h4 {
  font-size: 14px;
  color: var(--text-primary);
  margin: 0;
}

.service-status {
  padding: 4px 12px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: 500;
}

.service-status.enabled {
  background: #f0f9eb;
  color: var(--success-color);
}

.service-status.disabled {
  background: #fef0f0;
  color: var(--danger-color);
}

/* 操作按钮 */
.settings-actions {
  display: flex;
  gap: 12px;
  justify-content: center;
  padding-top: 30px;
  border-top: 1px solid var(--border-light);
}

.btn {
  display: flex;
  align-items: center;
  gap: 8px;
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

.btn-secondary {
  background: var(--bg-secondary);
  color: var(--text-primary);
  border: 1px solid var(--border-normal);
}

.btn-secondary:hover {
  background: var(--bg-tertiary);
}

.btn-danger {
  background: var(--bg-secondary);
  color: var(--danger-color);
  border: 1px solid var(--danger-color);
}

.btn-danger:hover {
  background: var(--danger-color);
  color: white;
}

.btn-success {
  background: var(--bg-secondary);
  color: var(--success-color);
  border: 1px solid var(--success-color);
}

.btn-success:hover {
  background: var(--success-color);
  color: white;
}

/* 空状态 */
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

/* 响应式设计 */
@media (max-width: 768px) {
  .settings {
    padding: 10px;
  }

  .settings-nav {
    flex-direction: column;
    gap: 8px;
  }

  .nav-item {
    border-bottom: none;
    border-left: 3px solid transparent;
    padding: 12px 16px;
  }

  .nav-item.active {
    border-bottom: none;
    border-left-color: var(--primary-color);
  }

  .coordinate-inputs {
    grid-template-columns: 1fr;
  }

  .theme-options {
    grid-template-columns: 1fr;
  }

  .settings-actions {
    flex-direction: column;
  }

  .btn {
    justify-content: center;
  }
}
</style>