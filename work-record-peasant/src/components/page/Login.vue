<template>
    <div class="login-wrap">
        <div class="ms-title">民工系统</div>
        <div class="ms-login">
            <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="0px" class="demo-ruleForm">
                <el-form-item prop="username">
                    <el-input v-model="ruleForm.username" placeholder="用户名"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input type="password" placeholder="密码" v-model="ruleForm.password" @keyup.enter.native="submitForm('ruleForm')"></el-input>
                </el-form-item>
                <div class="login-btn">
                    <el-button type="primary" @click="submitForm('ruleForm')">登录</el-button>
                </div>
                <div class="login-btn">
                    <el-button type="primary" @click="jumptosignup()">注册</el-button>
                </div>
            </el-form>
        </div>
    </div>
</template>

<script>
    export default {
        data: function(){
            return {
                ruleForm: {
                    username: '',
                    password: ''
                },
                rules: {
                    username: [
                        { required: true, message: '请输入用户名', trigger: 'blur' }
                    ],
                    password: [
                        { required: true, message: '请输入密码', trigger: 'blur' }
                    ]
                },
                loginurl: this.$domin + '/?_action=login'

            }
        },
        methods: {
            jumptosignup(){
                this.$router.push('/signup');
            },
            submitForm1(formName) {
                const self = this;
                self.$refs[formName].validate((valid) => {
                    if (valid) {
                        localStorage.setItem('ms_username',self.ruleForm.username);
                        self.$router.push('/seerecord');
                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },
            submitForm(formName) {
                const self = this;
                console.log(this);
                self.$refs[formName].validate((valid) => {
                    if (valid) {
                            this.$ajax.get(this.loginurl + '&phone=' + this.ruleForm.username +
                            '&password=' + this.ruleForm.password +
                            '&type=1'
                            , {
                        }
                        ).then((res) => {
                            // self.tableData = res.data.data;
                            console.log(res)
                            console.log(res.data.data)
                            if (res.data.code === 0) {
                            // localStorage.setItem('ms_username', res.data.data.user_name)
                            if (res.data.data.status === 1) {
                                localStorage.setItem('token', res.data.data.token)
                                this.loading = false
                                this.$message.success('登陆成功~')
                                this.$router.push('/readme');
                            } else if (res.data.data.status === 0) {
                                this.$message.error('该手机号未注册~')
                            } else if (res.data.data.status === -1) {
                                this.$message.error('密码错误~')
                            }
                            // localStorage.setItem('has_email', 1)
                            }
                            this.loading = false
                        })
                        // this.$store.dispatch('LoginByUsername', this.ruleForm).then(() => {
                        //   this.loading = false
                        //   this.$router.push({ path: '/' })
                        // }).catch(() => {
                        //   this.loading = false
                        // })
                        } else {
                        console.log('error submit!!')
                        return false
                        }
                });
            }
        }
    }
</script>

<style scoped>
    .login-wrap{
        position: relative;
        width:100%;
        height:100%;
    }
    .ms-title{
        position: absolute;
        top:50%;
        width:100%;
        margin-top: -230px;
        text-align: center;
        font-size:30px;
        color: #fff;

    }
    .ms-login{
        position: absolute;
        left:50%;
        top:50%;
        width:300px;
        height:160px;
        margin:-150px 0 0 -190px;
        padding:40px;
        border-radius: 5px;
        background: #fff;
    }
    .login-btn{
        text-align: center;
    }
    .login-btn button{
        width:40%;
        float:left;
        margin-left:20px;
        height:36px;
    }
</style>