<template>
  <div>
    <!-- 底部链接 -->
    <div class="footer-links">
      <a href="#" @click.prevent="showAbout">关于我们</a>
      <a href="#" @click.prevent="showTerms">使用条款</a>
      <a href="#" @click.prevent="showPrivacy">隐私政策</a>
    </div>

    <!-- 模态对话框 -->
    <div v-if="showModal" class="modal-overlay" @click="closeModal">
      <div class="modal-content" @click.stop>
        <div class="modal-header">
          <h3>{{ modalTitle }}</h3>
          <button class="modal-close" @click="closeModal">&times;</button>
        </div>
        <div class="modal-body">
          <div v-if="activeModal === 'about'">
            <h4>关于我们</h4>
            <p>地理信息数据平台是一个专业的地理数据管理和分析系统，旨在为用户提供一站式的地理信息服务。</p>
            <h5>平台功能</h5>
            <ul>
              <li>地图可视化与交互</li>
              <li>多格式数据导入导出</li>
              <li>专业地理分析工具</li>
              <li>数据统计与可视化</li>
              <li>系统设置与个性化</li>
            </ul>
            <h5>技术栈</h5>
            <p>Vue 3 + Vite + 高德地图 JavaScript API</p>
            <h5>联系我们</h5>
            <p>邮箱: contact@geodata-platform.com</p>
            <p>电话: 400-123-4567</p>
          </div>
          <div v-else-if="activeModal === 'terms'">
            <h4>使用条款</h4>
            <p>欢迎使用地理信息数据平台。以下是使用本平台的条款和条件：</p>
            <h5>1. 接受条款</h5>
            <p>使用本平台即表示您同意遵守本条款。如您不同意，请停止使用本平台。</p>
            <h5>2. 用户责任</h5>
            <p>您负责维护您的账户安全，并对使用您账户的所有活动负责。</p>
            <h5>3. 数据使用</h5>
            <p>您上传的数据仅用于您的个人分析，我们不会未经许可使用您的数据。</p>
            <h5>4. 知识产权</h5>
            <p>平台上的所有内容和功能均受版权保护，未经许可不得复制或分发。</p>
            <h5>5. 免责声明</h5>
            <p>平台提供的分析结果仅供参考，不构成任何形式的专业建议。</p>
            <h5>6. 条款变更</h5>
            <p>我们保留随时修改本条款的权利，修改后将在平台上公布。</p>
          </div>
          <div v-else-if="activeModal === 'privacy'">
            <h4>隐私政策</h4>
            <p>我们重视您的隐私，以下是我们如何收集、使用和保护您的信息：</p>
            <h5>1. 收集的信息</h5>
            <ul>
              <li>您上传的数据文件</li>
              <li>您的使用记录和偏好设置</li>
              <li>系统设置配置</li>
            </ul>
            <h5>2. 信息使用</h5>
            <p>我们仅将您的信息用于：</p>
            <ul>
              <li>提供和改进平台服务</li>
              <li>处理您的请求和查询</li>
              <li>维护平台的安全性</li>
            </ul>
            <h5>3. 信息保护</h5>
            <p>我们采取合理的安全措施保护您的信息，防止未授权访问、使用或披露。</p>
            <h5>4. 信息共享</h5>
            <p>我们不会向第三方分享您的个人信息，除非法律要求或得到您的明确许可。</p>
            <h5>5. 数据存储</h5>
            <p>您的数据存储在本地浏览器中，我们不会将其上传到服务器。</p>
            <h5>6. 您的权利</h5>
            <p>您有权访问、修改或删除您的个人信息。</p>
          </div>
        </div>
        <div class="modal-footer">
          <button class="btn btn-primary" @click="closeModal">关闭</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  name: 'FooterLinks',
  setup() {
    // 模态对话框相关
    const showModal = ref(false)
    const activeModal = ref('')
    const modalTitle = ref('')

    // 显示关于我们
    const showAbout = () => {
      activeModal.value = 'about'
      modalTitle.value = '关于我们'
      showModal.value = true
    }

    // 显示使用条款
    const showTerms = () => {
      activeModal.value = 'terms'
      modalTitle.value = '使用条款'
      showModal.value = true
    }

    // 显示隐私政策
    const showPrivacy = () => {
      activeModal.value = 'privacy'
      modalTitle.value = '隐私政策'
      showModal.value = true
    }

    // 关闭模态对话框
    const closeModal = () => {
      showModal.value = false
      activeModal.value = ''
      modalTitle.value = ''
    }

    return {
      showModal,
      activeModal,
      modalTitle,
      showAbout,
      showTerms,
      showPrivacy,
      closeModal
    }
  }
}
</script>

<style scoped>
.footer-links {
  display: flex;
  gap: 20px;
}

.footer-links a {
  color: #999;
  text-decoration: none;
  transition: color 0.3s ease;
}

.footer-links a:hover {
  color: #409eff;
}

/* 模态对话框样式 */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
}

.modal-content {
  background: white;
  border-radius: 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
  width: 90%;
  max-width: 600px;
  max-height: 80vh;
  overflow-y: auto;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  border-bottom: 1px solid #eee;
  background: #fafafa;
  border-radius: 8px 8px 0 0;
}

.modal-header h3 {
  margin: 0;
  font-size: 18px;
  color: #333;
  font-weight: 600;
}

.modal-close {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #999;
  padding: 0;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
  transition: all 0.3s ease;
}

.modal-close:hover {
  background: #f0f0f0;
  color: #333;
}

.modal-body {
  padding: 20px;
  line-height: 1.6;
}

.modal-body h4 {
  margin-top: 0;
  margin-bottom: 15px;
  color: #333;
  font-size: 16px;
}

.modal-body h5 {
  margin-top: 20px;
  margin-bottom: 10px;
  color: #666;
  font-size: 14px;
}

.modal-body p {
  margin-bottom: 15px;
  color: #666;
}

.modal-body ul {
  margin-bottom: 15px;
  padding-left: 20px;
}

.modal-body li {
  margin-bottom: 5px;
  color: #666;
}

.modal-footer {
  padding: 20px;
  border-top: 1px solid #eee;
  background: #fafafa;
  border-radius: 0 0 8px 8px;
  display: flex;
  justify-content: flex-end;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .footer-links {
    gap: 12px;
  }
  
  .footer-links a {
    font-size: 12px;
  }
  
  .modal-content {
    width: 95%;
    margin: 20px;
  }
  
  .modal-header,
  .modal-body,
  .modal-footer {
    padding: 16px;
  }
}
</style>