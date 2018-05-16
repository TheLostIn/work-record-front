<template>
<!-- +```
+## 3. 公司发布工作（get）
+### 接口地址
+http://api.logicjake.xyz/work-record/?_action=releaseWork
+### 接口参数
+| key        | value   |
+| :------:   | :-----: |
+| token       | e6971f7a692cbaa8b37aa7ad32875aaf       |
+|address|南京|
+|phone|13222222|
+|wages|[{"type":"家装主材安装","wage":100},{"type":"电器安装维修","wage":110}]|
+|house|板房|
+|welfare|高温补贴|
+### 说明
+* wages为招聘工种和对应工资  
+* house为住房条件
+* welfare为福利 
+### 返回值
+#### 成功，status=1
+```
+{
+    "code": 0,
+    "data": {
+        "status": 1
+    }
+} -->
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i>工作发布</el-breadcrumb-item>
                <el-breadcrumb-item>工作发布</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="form-box">
         
            <el-form :model="releaseForm" :rules="releaseForm_rules" ref="releaseForm" label-width="80px">
                <el-form-item  prop="address" label="工作地址">
                    <el-input v-model="releaseForm.address"></el-input>
                </el-form-item>
                <el-form-item  prop="phone" label="电话号码">
                    <el-input v-model="releaseForm.phone"></el-input>
                </el-form-item>
                <el-form-item  prop="house" label="住房条件">
                    <el-input v-model="releaseForm.house"></el-input>
                </el-form-item>
                <el-form-item  prop="welfare" label="福利">
                    <el-input v-model="releaseForm.welfare"></el-input>
                </el-form-item>
                <el-form-item  prop="start_time" label="开始时间">
                    <el-input type="date" v-model="releaseForm.start_time"></el-input>
                </el-form-item>
               <el-form-item>
                    <!-- <el-input name="curexperience" type="text" v-model="signupForm.curexperience" autoComplete="on" placeholder="curexperience" /> -->
                    <el-tag
                        :key="tag"
                        v-for="tag in releaseForm.wages"
                        closable
                        :disable-transitions="false"
                        @close="handleClose(tag)">
                        {{tag.type}}: {{tag.wage}}元
                    </el-tag>
                </el-form-item>
                <el-form-item prop="curexperience" label="从业经验">         
                    <el-select  v-model="value" placeholder="从业经验">
                        <el-option
                        v-for="item in allexperiences"
                        :key="item"
                        :label="item"
                        :value="item">
                        </el-option>
                    </el-select>
                    <el-input-number size="mini" :min=1 :max=50 v-model="curwage"></el-input-number>
                    <el-button type="primary" @click="addwork()">添加</el-button>
                </el-form-item>
               
    <!-- <el-form-item label="活动名称" :label-width="formLabelWidth">
      <el-input v-model="work_form.teamname" auto-complete="off"></el-input>
    </el-form-item> -->
   
  <div slot="footer" class="dialog-footer">
    <el-button @click="dialogFormVisible = false">取 消</el-button>
    <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
  </div>

               
                <el-form-item>
                    <el-button type="primary" @click="onSubmit('releaseForm')">提交</el-button>
                    <el-button>取消</el-button>
                </el-form-item>
            </el-form>
        </div>
    </div>
</template>

<script>
    export default {
        data: function(){
            return {
                checkList:[],
                inputVisible: false,
                inputValue: '',

                dialogTableVisible: false,
                dialogFormVisible: false,
                Allteamname:['pp'],
                formLabelWidth: '120px',
                Allstu:['161540205'],
                allexperiences: ['装修施工', '建筑施工', '家装主材安装', '门窗玻璃安装', '隔墙吊顶', '家具软装维修', '电器安装维修', '家政服务', '管道疏通', '园林绿化', '路桥建设', '其他'],
                curexperience: '装修施工',
                value: '',
                curwage: 2,
                work_form: {
                    work_name: '',
                    target_group: ['1615402','1615403'],
                    start_time: '',
                    end_time: '',
                    inform_all: true,
                    allow_ext: ['zip'],
                    target_stu:[],
                    // resource: '小天才',
                    attention_content: '',
                    download_format:'{name}_{num}',

                    teamname:'',
                    has_allteamname:0
                },
                releaseForm: {
                    token: '',
                    target_group: ['1615402','1615403'],
                    address: '',
                    phone: '',
                    wages: [],
                    house: '板房',
                    welfare:'高温补贴',
                    start_time:''
                },
                releaseForm_rules: {
                    // work_name: [
                    //     { required: true, message: '请输入作业名', trigger: 'blur' }
                    // ],
                    // target_group: [
                    //     { required: true, message: '请输入需提交者', trigger: 'blur' }
                    // ],
                    // allow_ext: [
                    //     { required: true, message: '请输入上传类型', trigger: 'blur' }
                    // ],
                    // resource: [
                    //     { required: true, message: '请输入xxx', trigger: 'blur' }
                    // ],
                    // attention_content: [
                    //     { required: true, message: '请输入作业须知', trigger: 'blur' }
                    // ]
                   
                }
            }
            
        },
        methods: {
            addwork() {
                var new_work = { type: this.value, wage: this.curwage }
                console.log(new_work)
                this.releaseForm.wages.push(new_work)
                },
            showInput() {
                this.inputVisible = true;
                this.$nextTick(_ => {
                this.$refs.saveTagInput.$refs.input.focus();
                });
            },
            handleInputConfirm() {
                let inputValue = this.inputValue;
                if (inputValue) {
                this.work_form.target_stu.push(inputValue);
                }
                this.inputVisible = false;
                this.inputValue = '';
            },
            handleClose(tag) {
                this.releaseForm.wages.splice(this.releaseForm.wages.indexOf(tag), 1);
            },
            getByteam(){
                const self = this;

                let token = localStorage.getItem('token');
                this.dialogFormVisible = true ;
 
                    this.$ajax.post(this.$domin+'/work-system/api/index.php?_action=team&action_type=getbyteam&token='+token+"&teamname="+self.work_form.teamname,{
                        "teamname":self.work_form.teamname
                    }).then(re => {
                        console.log(re.data);
                        console.log(re);
                        if(re.data.code == 0){
                            self.Allstu = re.data.data;
                        }else{
                            this.$message.error('没有分组~');
                        }
                    });
                
            },
            getAllteamname(){
                const self = this;

                let token = localStorage.getItem('token');
                this.dialogFormVisible = true ;
 
                    this.$ajax.post(this.$domin+'/work-system/api/index.php?_action=team&action_type=getallteam&token='+token).then(re => {
                        console.log(re.data);
                        console.log(re);
                        if(re.data.code == 0){
                            self.Allteamname = re.data.data;
                            self.work_form.has_allteamname = 1;
                        }else{
                            this.$message.error('没有分组~');
                        }
                    });
                
            },
            onSubmit(formName) {
                const self = this;
                // console.log(this);
                // console.log(Date.parse(new Date(this.work_form.start_time))/1000);
                // console.log(Date.parse(this.work_form.start_time));
                console.log(this.work_form);
                let token = localStorage.getItem('token');
                // Date.parse(this.start_time);
                var myDate = new Date();
                // console.log(myDate.getYear());        //获取当前年份(2位)
                // console.log(myDate.getFullYear());    //获取完整的年份(4位,1970-????)
                // console.log(myDate.getMonth());       //获取当前月份(0-11,0代表1月)
                // console.log(myDate.getDate()); 
                var year = myDate.getFullYear();
                var month = myDate.getMonth()+1;
                var date = myDate.getDate();
                var target_group_name = ''+ year + '_' + month + '_' + date + '_' + this.work_form.work_name;
                var new_work_name = ''+ year + '_' + month + '_' + date + '_' + this.work_form.work_name;
                self.$refs[formName].validate((valid) => {
                    if (valid) {
                        console.log(this.$domin);
                        // this.$ajax.post(this.$domin+'/work-system/api/index.php?_action=postWork&action_type=releaseNewwork&token='+token,{
                        //     'work_name': new_work_name,//this.work_form.work_name,
                        //     'target_group': target_group_name,//this.work_form.target_group.join("-"),
                        //     'start_time': Date.parse(new Date(this.work_form.start_time))/1000,
                        //     'end_time': Date.parse(new Date(this.work_form.end_time))/1000,
                        //     'inform_all': this.work_form.inform_all,
                        //     'allow_ext': this.work_form.allow_ext.join("-"),
                        //     'attention_content': this.work_form.attention_content,
                        //     "target_user_nums": this.work_form.target_stu,
                        //     "download_format": this.work_form.download_format
                        // }).then(re => {
                        //     console.log(re.data);
                        //     console.log(re);
                        //     if(re.data.code == 0){
                        //         //localStorage.setItem('ms_username',self.rule_Form.username);
                        //         self.$router.push('/news');
                        //         this.$message.success('作业发布成功');
                        //     }else{
                        //          //self.$router.push('/readme');
                        //         this.$message.error('发布作业失败~');
                        //     }
                        // })
                        console.log(this.releaseForm)
                        console.log(token)
                        console.log(JSON.stringify( this.releaseForm.wages ))
                        this.$ajax.get(this.$domin + '/?_action=releaseWork&token='+token
                            // + '&token='+ this.releaseForm.token
                            // + '&target_group='+ this.releaseForm.target_group
                            + '&address='+ this.releaseForm.address
                            + '&phone='+ this.releaseForm.phone
                            + '&wages='+ JSON.stringify( this.releaseForm.wages )
                            + '&house='+ this.releaseForm.house
                            + '&welfare='+ this.releaseForm.welfare
                            + '&start_time='+ this.releaseForm.start_time
                            + '&type=0'
                        ).then(re => {
                            console.log(re.data);
                            console.log(re);
                            if(re.data.code == 0){
                                //localStorage.setItem('ms_username',self.rule_Form.username);
                                self.$router.push('/news');
                                this.$message.success('作业发布成功');
                            }else{
                                 //self.$router.push('/readme');
                                this.$message.error('发布作业失败~');
                            }
                        })
                    }
                });
            }
        }
    }
</script>

<style>
  .el-tag + .el-tag {
    margin-left: 10px;
  }
</style>
