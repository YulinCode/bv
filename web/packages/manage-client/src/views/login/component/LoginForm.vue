<template>
    <div class="login-form">
        <!-- 账号登录 -->
        <n-tabs default-value="account" size="large" justify-content="space-evenly" @update:value="loginTypeChange">
            <n-tab-pane class="form-container" name="account" tab="账号登录">
                <n-form ref="accountFormRef" :rules="rules" :model="loginForm" label-placement="left" label-width="70">
                    <n-form-item path="account">
                        <n-input placeholder="请输入邮箱" v-model:value="loginForm.email" />
                    </n-form-item>
                    <n-form-item path="password">
                        <n-input placeholder="请输入密码" v-model:value="loginForm.password" type="password" />
                    </n-form-item>
                </n-form>
            </n-tab-pane>
            <!-- 邮箱登录 -->
            <n-tab-pane class="form-container" name="email" tab="邮箱验证">
                <n-form ref="emailFormRef" :rules="rules" :model="loginForm" label-placement="left" label-width="70">
                    <n-form-item path="email">
                        <n-input placeholder="请输入邮箱" v-model:value="loginForm.email" />
                    </n-form-item>
                    <n-form-item path="code">
                        <n-input placeholder="请输入验证码" v-model:value="loginForm.code" />
                        <n-button :disabled="disabledSend" @click="beforeSendCode">{{ sendBtnText }}</n-button>
                    </n-form-item>
                </n-form>
            </n-tab-pane>
        </n-tabs>
        <div class="login-btn">
            <n-button type="primary" @click="sendLoginRequest">登录</n-button>
        </div>
    </div>
    <slider-captcha v-model:show="showCaptcha" :email="loginForm.email" @success="captchaSuccess"></slider-captcha>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue';
import useSendCode from '@/hooks/send-code-hooks';

import type { UserLoginType } from "@leaf/apis";
import { loginAPI, emailLoginAPI, getUserInfoAPI } from "@leaf/apis";
import { storageData, statusCode } from '@leaf/utils';

import type { FormRules, FormInst } from 'naive-ui';
import { NTabs, NTabPane, NForm, NFormItem, NInput, NButton, useNotification } from 'naive-ui';

import { SliderCaptcha } from "@leaf/components";
import { useRouter } from 'vue-router';

const emits = defineEmits(["success"]);

const router = useRouter();
//通知组件
const notification = useNotification();

// 显示滑块验证
const showCaptcha = ref(false);

//登录表单
const loginForm = reactive<UserLoginType>({
    email: "",
    code: "",
    password: "",
});


//校验规则
const rules: FormRules = {
    email: [
        { required: true, message: "请输入邮箱", trigger: ['blur', 'input'] },
        { type: "email", message: "请输入正确的邮箱地址", trigger: ['blur', 'input'] },
    ],
    password: { required: true, message: '请输入密码', trigger: ['blur', 'input'] },
    code: { required: true, message: '请输入验证码', trigger: ['blur', 'input'] },
}

// 滑块验证通过
let captchaUsers = "";// 人机验证使用者
const captchaSuccess = () => {
    if (captchaUsers === "login") {
        sendLoginRequest();
    } else {
        beforeSendCode();
    }
}

//发送验证码相关
const { disabledSend, sendBtnText, sendEmailCodeAsync } = useSendCode();
const beforeSendCode = () => {
    if (!loginForm.email) {
        return;
    }
    sendEmailCodeAsync(loginForm.email).then((res) => {
        if (res === statusCode.CAPTCHA_REQUIRED) {
            captchaUsers = "emailcode";
            showCaptcha.value = true;
        }
    })
}

// 登录方式切换
const currentTabName = ref("account");
const loginTypeChange = (tabName: string) => {
    currentTabName.value = tabName;
}

//登录相关
const emailFormRef = ref<FormInst | null>(null);
const accountFormRef = ref<FormInst | null>(null);
//登录
const sendLoginRequest = () => {
    let currentRef: FormInst | null = null;
    switch (currentTabName.value) {
        case "account":
            emailLoginAPI
            currentRef = accountFormRef.value;
            break;
        case "email":
            currentRef = emailFormRef.value;
            break;
    }
    currentRef?.validate((err) => {
        if (!err) {
            const loginRequest = currentTabName.value === "account" ? loginAPI : emailLoginAPI;
            loginRequest(loginForm).then((res) => {
                switch (res.data.code) {
                    case statusCode.CAPTCHA_REQUIRED:
                        captchaUsers = "login";
                        showCaptcha.value = true;
                        break;
                    case statusCode.OK:
                        storageData.set("token", res.data.data.token, 14 * 24 * 60);
                        getUserInfoAPI().then((infoRes) => {
                            if (infoRes.data.code === statusCode.OK) {
                                const userInfo = infoRes.data.data.user_info;
                                storageData.set("user_info", userInfo, 14 * 24 * 60);
                                if (userInfo.role === 0) {
                                    notification.error({
                                        title: '暂无权限',
                                        duration: 3000,
                                    });
                                    return;
                                }
                            }
                            emits("success");
                        })
                        break;
                    default:
                        notification.error({
                            title: res.data.msg,
                            duration: 3000,
                        });
                }
            });
        } else {
            notification.error({
                title: '请检查输入的数据',
                duration: 3000,
            })
        }
    });
}
</script>

<style lang="less" scoped>
.login-form {
    .form-container {
        box-sizing: border-box;
        padding: 40px 30px 0 30px;
    }

    .login-btn {
        display: flex;
        width: 100%;
        margin: 20px 30px 0;

        button {
            width: calc(100% - 60px);
        }
    }
}
</style>