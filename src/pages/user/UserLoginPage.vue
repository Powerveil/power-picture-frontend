<template>
  <div id="userLoginPage">
    <h2 class="title">power云图库 -- 用户登录</h2>
    <dic class="desc">企业级智能协同云图库</dic>
    <a-form :model="formState" name="basic" @finish="handleSummit">
      <a-form-item name="userAccount" :rules="[{ required: true, message: '请输入用户账号' }]">
        <a-input v-model:value="formState.userAccount" />
      </a-form-item>

      <a-form-item
        name="userPassword"
        :rules="[
          { required: true, message: '请输入密码' },
          { min: 8, message: '密码长度不能小于8位' },
        ]"
      >
        <a-input-password v-model:value="formState.userPassword" />
      </a-form-item>

      <div class="tips">
        没有账号
        <RouterLink to="/user/register">去注册</RouterLink>
      </div>

      <a-form-item>
        <a-button type="primary" html-type="submit" style="width: 100%">登录</a-button>
      </a-form-item>
    </a-form>
  </div>
</template>
<script lang="ts" setup>
import { reactive } from 'vue'
import { message } from 'ant-design-vue'
import { loginUsingPost, userLoginUsingPost, userLogoutUsingPost } from '@/api/userController.ts'
import { useLoginUserStore } from '@/stores/useLoginUserStore.ts'
import { useRouter } from 'vue-router'

// 用于接受表单
const formState = reactive<API.UserLoginRequest>({
  userAccount: '',
  userPassword: ''
})
// const handleSummit = (values: any) => {
//   // console.log('Success:', values)
// }

const router = useRouter()
const loginUserStore = useLoginUserStore()

/**
 * 接受表单
 */
const handleSummit = async (values: any) => {
  const res = await userLoginUsingPost(values)
  // 登录成功
  if (res.data.code === 0 && res.data.data) {
    await loginUserStore.fetchLoginUser()
    message.success('登录成功')
    router.push({
      path: '/',
      replace: true
    })
  } else {
    message.error('登录失败' + res.data.message)
  }
}

</script>


<style  scoped>
#userLoginPage {
  max-width: 360px;
  margin: 0 auto;
}

.title {
  text-align: center;
  margin-bottom: 16px;
}

.desc {
  text-align: center;
  color: #bbb;
  margin-bottom: 16px;
}

.tips {
  text-align: right;
  color: #bbb;
  font-size: 13px;
  margin-bottom: 16px;
}
</style>
