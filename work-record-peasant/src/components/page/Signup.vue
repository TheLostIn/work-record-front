<template>
    <div class="login-wrap">
        <div class="ms-title">农名工注册</div>
        <div class="ms-login">
            <el-form :model="signupForm" :rules="rules" ref="signupForm" label-width="0px" class="demo-signupForm">
                <el-form-item prop="name">
            <span class="svg-container svg-container_Signup">
            <svg-icon icon-class="user" />
            </span>
            <el-input name="name" type="text" v-model="signupForm.name" autoComplete="on" placeholder="姓名" />
        </el-form-item>
        <el-form-item prop="phone">
        <span class="svg-container svg-container_Signup">
          <svg-icon icon-class="user" />
        </span>
        <el-input name="phone" type="text" v-model="signupForm.phone" autoComplete="on" placeholder="手机号" />
        </el-form-item>
        <el-form-item prop="age">
            <span class="svg-container svg-container_Signup">
            <svg-icon icon-class="user" />
            </span>
            <el-input name="age" type="text" v-model="signupForm.age" autoComplete="on" placeholder="年龄" />
        </el-form-item>
        <el-form-item>
             <!-- <el-input name="curexperience" type="text" v-model="signupForm.curexperience" autoComplete="on" placeholder="curexperience" /> -->
            <el-tag
                :key="tag"
                v-for="tag in signupForm.experience"
                closable
                :disable-transitions="false"
                @close="handleClose(tag)">
                {{tag.experience}}: {{tag.year}}年
            </el-tag>
        </el-form-item>
        <el-form-item prop="curexperience">
            <el-select  v-model="value" placeholder="从业经验">
                <el-option
                v-for="item in allexperiences"
                :key="item"
                :label="item"
                :value="item">
                </el-option>
            </el-select>
            <el-input type="number" size="middle" :min=1 :max=50 v-model="curyear"><template slot="append">年</template></el-input>
            <el-button type="primary" @click="addwork()">添加</el-button>
        </el-form-item>
        <el-form-item prop="password">
            <span class="svg-container">
            <svg-icon icon-class="password" />
            </span>
            <el-input name="password" :type="passwordType" @keyup.enter.native="handleSignup" v-model="signupForm.password" autoComplete="on" placeholder="password" />
            <span class="show-pwd" @click="showPwd">
            <svg-icon icon-class="eye" />
            </span>
        </el-form-item>

                <div class="login-btn">
                    <el-button type="primary" @click="handleSignup()">注册</el-button>
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
                 signupForm: {
                    name: 'admin',
                    password: '1111111',
                    age: 30,
                    phone: '',
                    experience: [],
                    type: 1
                },
                SignupRules: {
                    name: [{ required: true, trigger: 'blur' }],
                    password: [{ required: true, trigger: 'blur' }]
                },
                allexperiences: ['装修施工', '建筑施工', '家装主材安装', '门窗玻璃安装', '隔墙吊顶', '家具软装维修', '电器安装维修', '家政服务', '管道疏通', '园林绿化', '路桥建设', '其他'],
                passwordType: 'password',
                loading: false,
                showDialog: false,
                curexperience: '装修施工',
                value: '',
                curyear: 2,
                signupurl: this.$domin + '/?_action=signUp'
                        }
        },
        methods: {
            addwork() {
      var new_work = { experience: this.value, year: this.curyear }
      this.signupForm.experience.push(new_work)
    },
    handleChange(value) {
      console.log(value)
    },
    handleClose(tag) {
      this.signupForm.experience.splice(this.signupForm.experience.indexOf(tag), 1)
    },
    showPwd() {
      if (this.passwordType === 'password') {
        this.passwordType = ''
      } else {
        this.passwordType = 'password'
      }
    },
    handleSignup() {
      console.log('ppppok')
      this.$refs.signupForm.validate(valid => {
        if (valid) {
          this.loading = true
          // this.$store.dispatch('SignupByname', this.signupForm).then(() => {
          //   this.loading = false
          //   this.$router.push({ path: '/' })
          // }).catch(() => {
          //   this.loading = false
          // })
          console.log(this.$ajax)
          // this.signupForm.experience = 'p09808p'
          // this.$ajax.post(this.signupurl, this.signupForm).then((res) => {
          console.log(JSON.stringify(this.signupForm.experience))
          this.$ajax.get(this.signupurl + '&name=' + this.signupForm.name +
            '&password=' + this.signupForm.password +
            '&age=' + this.signupForm.age +
            '&phone=' + this.signupForm.phone +
            '&experience=' + JSON.stringify(this.signupForm.experience) +
            '&type=' + this.signupForm.type, {
          }
          ).then((res) => {
            // self.tableData = res.data.data;
            console.log(res)
            console.log(res.data.data)
            if (res.data.code === 0) {
              // localStorage.setItem('ms_username', res.data.data.user_name)
              if (res.data.data.status === 1) {
                                  const self = this;
                localStorage.setItem('token', res.data.data.token)
                this.loading = false
                this.$message.success('注册成功~')
                 self.$router.push('/login');
              } else {
                this.$message.error('该手机号已注册~')
              }
              // localStorage.setItem('has_email', 1)
            }
            this.loading = false
          })
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
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
        top:40%;
        width:100%;
        margin-top: -230px;
        text-align: center;
        font-size:30px;
        color: #fff;

    }
    .ms-login{
        position: absolute;
        left:50%;
        top:40%;
        width:300px;

        margin:-150px 0 0 -190px;
        padding:40px;
        border-radius: 5px;
        background: #fff;
    }
    .login-btn{
        text-align: center;
    }
    .login-btn button{
        width:100%;
        height:36px;
    }
</style>