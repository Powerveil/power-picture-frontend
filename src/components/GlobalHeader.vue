<template>

  <div id="globalHeader">
    <a-row :wrap="false">
      <a-col flex="200px">
        <router-link to="/">
          <div class="title-bar">
            <img class="logo" src="../assets/logo.ico" alt="logo"></img>
            <div class="title">power云图库</div>
          </div>
        </router-link>
      </a-col>
      <a-col flex="auto">
        <a-menu v-model:selectedKeys="current" mode="horizontal" :items="items" @click="doMenuClick"/>
      </a-col>

      <a-col flex="120px">
        <div class="user-login-status">
          <div v-if="loginUserStore.loginUser.id">
            <a-dropdown>
              <a-space>
                <a-avatar :src="loginUserStore.loginUser.userAvatar"></a-avatar>
                {{ loginUserStore.loginUser.userName ?? '无名'}}
              </a-space>
              <template #overlay>
                <a-menu>
                  <a-menu-item @click="doLogout">
                    <LogoutOutlined />
                    退出登录
                  </a-menu-item>
                </a-menu>
              </template>
            </a-dropdown>
          </div>
          <div v-else>
            <a-button type="primary" href="/user/login">登录</a-button>
          </div>
        </div>
      </a-col>
    </a-row>
  </div>

</template>
<script lang="ts" setup>
import { computed, h, ref } from 'vue'
import { HomeOutlined, LogoutOutlined } from '@ant-design/icons-vue'
import { MenuProps, message } from 'ant-design-vue'
import { useRouter } from 'vue-router'
import { useLoginUserStore } from '@/stores/useLoginUserStore.ts'
import { userLogoutUsingPost } from '@/api/userController.ts'


const loginUserStore = useLoginUserStore()

const originItems = [
  {
    key: '/',
    icon: () => h(HomeOutlined),
    label: '主页',
    title: '主页'
  },
  {
    key: '/about',
    label: '关于',
    title: '关于'
  },
  {
    key: '/admin',
    label: '用户管理',
    title: '用户管理'
  },
  {
    key: 'others',
    label: h('a', { href: 'http://81.70.86.227:8787', target: '_blank' }, '进入地球Online的男人'),
    title: '进入地球Online的男人'
  }
]

// 过滤菜单项
const filterMenus = (menus = [] as MenuProps['items']) => {
  return menus?.filter((menu) => {
    if (menu?.key?.startsWith('/admin')) {
      const loginUser = loginUserStore.loginUser
      if (!loginUser || loginUser.userRole !== 'admin') {
        return false
      }
    }
    return true
  })
}

const items = computed(() => filterMenus(originItems))

const router = useRouter()

const doMenuClick = ({ key }) => {
  router.push({
    path: key
  })
}

const current = ref<string[]>([])
router.afterEach((to, from, next) => {
  current.value = [to.path]
})


const doLogout  = async () => {
  const res = await userLogoutUsingPost()

  if (res.data.code === 0) {
    loginUserStore.setLoginUser({
      id: null,
      userName: '未登录',
      userAvatar: ''
    })
    message.success("退出登录成功")
    router.push({
      path: '/user/login',
      replace: true
    })

  } else {
    console.log("退出登录失败，" + res.data.message)
  }
}

</script>


<style scoped>
#globalHeader .title-bar {
  display: flex;
  align-items: center;
}

.title {
  color: black;
  font-size: 18px;
  margin-left: 16px;
}

.logo {
  height: 48px;
}

</style>

