<template>
  <div class="container">
    <div class="top">
      <div class="header">
        <a href="/">
          <img src="../assets/logo.png" class="logo" alt="logo" />
          <span class="title">聚光引擎</span>
        </a>
      </div>
    </div>
    <div class="main">
      <a-form
        :model="formState"
        name="basic"
        :label-col="{ span: 8 }"
        :wrapper-col="{ span: 16 }"
        autocomplete="off"
        @finish="onFinish"
        @finishFailed="onFinishFailed"
      >
        <a-form-item
          name="username"
          :rules="[{ required: true, message: 'Please input your username!' }]"
        >
          <a-input v-model:value="formState.username" />
        </a-form-item>

        <a-form-item
          name="password"
          :rules="[{ required: true, message: 'Please input your password!' }]"
        >
          <a-input-password v-model:value="formState.password" />
        </a-form-item>
        <a-form-item
          name="password"
          :rules="[{ required: true, message: 'Please input your password!' }]"
        >
          <a-input-password v-model:value="formState.password" />
        </a-form-item>
      </a-form>
    </div>
  </div>
</template>

<script setup>
import { reactive } from "vue";
const formState = reactive({
  username: "",
  password: "",
  remember: true,
});
</script>

<style lang="less" scoped>
.container {
  width: 100%;
  min-height: 100%;
  background: #f0f2f5 url(~@/assets/background.svg) no-repeat 50%;
  background-size: 100%;
  padding: 110px 0 144px;
  position: relative;
  height: 100vh;
}
.top {
  text-align: center;

  .header {
    height: 44px;
    line-height: 44px;

    .badge {
      position: absolute;
      display: inline-block;
      line-height: 1;
      vertical-align: middle;
      margin-left: -12px;
      margin-top: -10px;
      opacity: 0.8;
    }

    .logo {
      height: 44px;
      vertical-align: top;
      margin-right: 16px;
      border-style: none;
    }

    .title {
      font-size: 33px;
      color: rgba(0, 0, 0, 0.85);
      font-family: Avenir, "Helvetica Neue", Arial, Helvetica, sans-serif;
      font-weight: 600;
      position: relative;
      top: 2px;
    }
  }
  .desc {
    font-size: 14px;
    color: rgba(0, 0, 0, 0.45);
    margin-top: 12px;
    margin-bottom: 40px;
  }
}
.main {
  min-width: 260px;
  width: 368px;
  margin: 0 auto;
}
</style>
