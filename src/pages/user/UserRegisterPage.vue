<template>
  <div id="userRegisterPage">
    <h2 class="title">power云图库 -- 用户注册</h2>
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

      <a-form-item
        name="checkPassword"
        :rules="[
          { required: true, message: '请确认密码' },
          { min: 8, message: '密码长度不能小于8位' },
          {},
        ]"
      >
        <a-input-password v-model:value="formState.checkPassword" />
      </a-form-item>

      <div class="tips">
        已有账号
        <RouterLink to="/user/login">去登录</RouterLink>
      </div>

      <a-form-item>
        <a-button type="primary" html-type="submit" style="width: 100%">注册</a-button>
      </a-form-item>
    </a-form>
  </div>
</template>
<script lang="ts" setup>
import { reactive } from 'vue'
import { message } from 'ant-design-vue'
import {
  loginUsingPost,
  registerUsingPost,
  userLoginUsingPost,
  userLogoutUsingPost,
  userRegisterUsingPost
} from '@/api/userController.ts'
import { useLoginUserStore } from '@/stores/useLoginUserStore.ts'
import { useRouter } from 'vue-router'

// 用于接受表单
const formState = reactive<API.UserRegisterRequest>({
  userAccount: '',
  userPassword: '',
  checkPassword: ''
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

  if (values.userPassword !== values.checkPassword) {
    message.error('两次输入密码不一致')
    return
  }
  const res = await userRegisterUsingPost(values)
  // 注册成功
  if (res.data.code === 0 && res.data.data) {
    message.success('注册成功')
    router.push({
      path: '/user/login',
      replace: true
    })
  } else {
    message.error('注册失败，' + res.data.message)
  }
}

</script>


<style  scoped>
#userRegisterPage {
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
