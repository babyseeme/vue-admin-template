<template>
    <div class="login-container">
        <vue-particles color="#66afe2"
                       :particleOpacity="0.7"
                       :particlesNumber="200"
                       shapeType="circle"
                       :particleSize="3"
                       linesColor="#66afe2"
                       :linesWidth="1"
                       :lineLinked="false"
                       :lineOpacity="0.4"
                       :linesDistance="250"
                       :moveSpeed="1"
                       :hoverEffect="true"
                       hoverMode="grab"
                       :clickEffect="true"
                       clickMode="repulse"
        ></vue-particles>
        <el-form id="login" class="card-box login-form" :class="classLoginChecking" autoComplete="on" :model="ruleForm" :rules="rules" ref="ruleForm"
                 label-position="left">
            <h1>
                <strong>美文后台管理系统</strong>
                <em class="en-font">Meiwen-Admin System</em>
            </h1>
            <el-form-item prop="username" class="item">
                <label>U</label>
                <el-input
                    placeholder="账号"
                    name="userName"
                    autoComplete="off"
                    v-model="ruleForm.userName"
                >
                    <i slot="prefix" class="el-input__icon"><icon-svg icon-class="user"/></i>
                </el-input>
            </el-form-item>
            <el-form-item prop="password" class="item">
                <label>P</label>
                <el-input
                    placeholder="密码"
                    name="pwd" :type="isShowPwd ? 'text' : 'password'"
                    @keyup.enter.native="handleLogin"
                    v-model="ruleForm.pwd"
                    autoComplete="off"
                >
                    <i slot="prefix" class="el-input__icon"><icon-svg icon-class="pwd"/></i>
                    <i slot="suffix" class="el-input__icon" @click='isShowPwd = !isShowPwd'><icon-svg icon-class="eye"/></i>
                </el-input>
            </el-form-item>
            <el-form-item prop="code" class="item">
                <label>V</label>
                <input type="text" name="captcha" value="" id="" class="en-font" autocomplete="off"
                       placeholder="验证码">
                <span class="vimg"><img src="http://admin_thinkphp.test/captcha.html" class="tooltip" /></span>
            </el-form-item>
            <div style="margin: 15px 30px;">
                <el-button type="primary" style="height: 40px;line-height: 40px;background: #65B1E2;margin: 0;padding: 0;width: 100%;border: 0;font-size: 16px;color: #f8f8f8;-webkit-transition: all .5s ease;transition: all .5s ease;" :loading="loading"
                           @click.native="handleLogin()">登录
                </el-button>
            </div>
            <!--<div>-->
                <!--<el-button type="primary" style="width:100%;margin-bottom:30px;"-->
                           <!--@click='showDialog = true'>-->
                    <!--第三方登录-->
                <!--</el-button>-->
            <!--</div>-->
            <div class="copy">
                <p>{$login.copy} © <a href="javascript:;" style="color:white; font-size:18px;" target="_blank">{$login.www}</a></p>

            </div>
        </el-form>
        <el-dialog title="验证中..." class="auth" :class="classLoginChecking">
            <div class="loading">
                <div class="loader-inner ball-clip-rotate-multiple"></div>
            </div>
        </el-dialog>

        <el-dialog title="第三方验证" :visible.sync="showDialog">
            邮箱登录成功,请选择第三方验证<br/>
        </el-dialog>

    </div>
</template>

<script>
export default {
    data() {
        let validatePwd = (rule, value, callback) => {
            if (value === "") {
                callback(new Error("请输入密码"));
            } else if (value.length < 5) {
                callback(new Error("密码不能小于5位"));
            } else {
                callback();
            }
        };
        return {
            ruleForm: {
                userName: "admin",
                pwd: "admin",
                checked: true
            },
            rules: {
                userName: [
                    { required: true, message: "请输入登录名", trigger: "blur" }
                ],
                pwd: [{ validator: validatePwd, trigger: "blur" }]
            },
            isShowPwd: false, // 是否显示密码
            loading: false, // 登录loading
            showDialog: false, // 显示dialog
            redirect: null, // 回调地址

            classLoginChecking: "" // 登陆样式
        };
    },
    methods: {
        handleLogin() {
            const that = this;
            that.classLoginChecking = "checking";
            setTimeout(function() {
                that.$refs["ruleForm"].validate(valid => {
                    if (valid) {
                        that.loading = true;
                        that.$store
                            .dispatch("loginName", that.ruleForm)
                            .then(response => {
                                that.loading = false;
                                if (response.code) {
                                    that.$message.error(response.message);
                                    return;
                                }
                                let path = "/";
                                if (that.redirect) {
                                    path = that.redirect;
                                }
                                // this.$router.push({
                                //     path: path
                                // });
                                window.location.replace(path);
                                // this.showDialog = true
                            })
                            .catch(() => {
                                that.loading = false;
                            });
                    } else {
                        return false;
                    }
                });
            }, 5500);
        }
    },
    created() {
        // 将参数拷贝进查询对象
        let query = this.$route.query;
        if (query.redirect) {
            // URL Encode
            this.redirect = decodeURIComponent(query.redirect);
        }
    }
};
</script>

<style type="text/scss" lang="scss" scoped>
@import "../../styles/mixin";
#login {
    -webkit-transition-timing-function: cubic-bezier(0.68, -0.25, 0.265, 0.85);
    transition-timing-function: cubic-bezier(0.68, -0.25, 0.265, 0.85);
    -webkit-transition-property: -webkit-transform, opacity, box-shadow, top, left, margin-top;
    transition-property: transform, opacity, box-shadow, top, left, margin-top;
    -webkit-transition-duration: 0.5s;
    transition-duration: 0.5s;
    -webkit-transform-origin: 50% 100%;
    -ms-transform-origin: 50% 100%;
    transform-origin: 50% 100%;
    -webkit-transform: rotateX(0deg);
    transform: rotateX(0deg);
    font-size: 14px;
    background: rgba(0, 0, 0, 0.6);
    z-index: 10;
    position: absolute;
    width: 360px;
    height: 420px;
    left: 50%;
    top: 50%;
    margin: -210px 0 0 -180px;
    border-radius: 8px;
    box-shadow: 0 -15px 30px #66afe2;
    .copy{
        text-align: center;
        color: #3575b1;
        margin-top: 25px;
    }
    .copy a{
        color: #3575b1;
    }
    h1 {
        text-align: center;
        padding: 25px 0 10px;
        color: #4e9dd8;
        font-weight: bolder;
        text-shadow: 0 0 1px #023a84;
        margin: 0;
    }
    h1 strong {
        font-size: 25px;
    }
    h1 em {
        display: block;
        font-size: 16px;
        margin-top: 8px;
    }
}
#login.checking {
    pointer-events: none;
    -webkit-transform: rotateX(60deg) scale(0.9) !important;
    transform: rotateX(60deg) scale(0.9) !important;
    opacity: 0.9 !important;
    margin-top: -280px;
}

.auth {
    width: 200px;
    height: 80px;
    background: #66afe2;
    padding-top: 40px;
    position: absolute;
    left: 50%;
    top: 50%;
    opacity: 0;
    margin: 0 0 0 -300px;
    -webkit-transition: all 0.5s ease;
    transition: all 0.5s ease;
    z-index: 9999;
    -webkit-transform: rotate(-270deg);
    transform: rotate(-270deg);
    .loading {
        width: 1px;
        height: 1px;
        margin: 0 auto;
    }
    p {
        text-align: center;
        margin-top: 40px;
        color: #fff;
        font-size: 11px;
    }
    .checking {
        margin-left: 20px;
        opacity: 0.9;
        -webkit-transform: rotate(0deg);
        transform: rotate(0deg);
    }
}

.el-form-item .el-input {
    background-color: #66afe2;
    color: #fff !important;
    opacity: 0.75;
}

$dark_gray: #889aa4;
$light_gray: #eee;
.login-container {
    @include relative;
    height: 100%;
    min-height: 440px;
    overflow: hidden;
    background: rgba(0, 0, 0, 0.8) url(../../assets/image/bluesky.jpg) left top no-repeat;
    background-size: cover;
    perspective: 800px;
    input:-webkit-autofill {
        -webkit-box-shadow: 0 0 0 1000px #293444 inset !important;
        -webkit-text-fill-color: #fff !important;
    }
    .item {
        margin: 15px 30px;
        height: 40px;
        line-height: 40px;
        background-color: #66afe2;
        padding-left: 40px;
        position: relative;
        label {
            position: absolute;
            margin-left: -40px;
            width: 40px;
            line-height: 40px;
            height: 40px;
            left: 0;
            top: 0;
            text-align: center;
            color: #65b1e2;
            font-size: 15px;
        }
        .vimg {
            position: absolute;
            right: 0;
            bottom: 0;
            height: 100%;
            width: 45%;
            background: #fff;
            overflow: hidden;
        }
        .vimg img {
            cursor: pointer;
            width: 100%;
            height: 100%;
        }
    }
    input {
        width: 96%;
        padding: 8px 2%;
        height: 24px;
        line-height: 24px;
        margin: 0;
        border: 1px;
        background: transparent;
        color: #ffffff;
        font-size: 15px;
        border-radius: 0.1666rem;
    }
    .el-input {
        display: inline-block;
    }
    .tips {
        font-size: 14px;
        color: #fff;
        margin-bottom: 0.13333rem;
    }
    .svg-container {
        padding: 0.08rem 0.0666rem 0.08rem 0.2rem;
        color: $dark_gray;
        vertical-align: middle;
        display: inline-block;
        &_login {
            font-size: 20px;
        }
    }
    .login-form {
        @include fxied-center;
        top: 40%;
        width: 22em;
        padding: 0.4666rem 0.4666rem 0.2rem 0.4666rem;
    }
    .el-form-item {
        border: 1px solid rgba(255, 255, 255, 0.1);
        background: rgba(0, 0, 0, 0.1);
        border-radius: 0.0666rem;
        color: #454545;
    }
    .show-pwd {
        position: absolute;
        right: 0.1333rem;
        top: 0.09333rem;
        font-size: 16px;
        color: $dark_gray;
        cursor: pointer;
        user-select: none;
    }
    .thirdparty-button {
        /*position: absolute;*/
        /*right: .4666rem;*/
        /*bottom: .37333rem;*/
    }
}
</style>
