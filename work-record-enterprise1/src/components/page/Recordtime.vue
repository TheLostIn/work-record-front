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
                <el-breadcrumb-item><i class="el-icon-date"></i>工时记录</el-breadcrumb-item>
                <el-breadcrumb-item>工时记录</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="form-box">
         
            <el-form :model="recordForm" :rules="recordForm_rules" ref="recordForm" label-width="80px">
                <el-form-item  prop="id" label="工人id号">
                    <el-input v-model="recordForm.id"></el-input>
                </el-form-item>
                <el-form-item  prop="num" label="工时数">
                    <el-input v-model="recordForm.num"></el-input>
                </el-form-item>
                <el-form-item  prop="date" label="工作时间">
                    <el-input type="date" v-model="recordForm.date"></el-input>
                </el-form-item>
                <el-form-item  prop="work_id" label="工作id">
                    <el-input type="number" v-model="recordForm.work_id"></el-input>
                </el-form-item>

               
    <!-- <el-form-item label="活动名称" :label-width="formLabelWidth">
      <el-input v-model="work_form.teamname" auto-complete="off"></el-input>
    </el-form-item> -->
   
  <div slot="footer" class="dialog-footer">
    <el-button @click="dialogFormVisible = false">取 消</el-button>
    <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
  </div>

               
                <el-form-item>
                    <el-button type="primary" @click="onSubmit('recordForm')">提交</el-button>
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

                recordForm: {
                    id: '',
                    date: '',
                    num: '',
                    work_id: ''
                },
                recordForm_rules: {
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
                this.recordForm.wages.push(new_work)
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
                this.work_form.target_stu.splice(this.work_form.target_stu.indexOf(tag), 1);
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
                console.log(token)
                // Date.parse(this.start_time);
                var myDate = new Date();
                // console.log(myDate.getYear());        //获取当前年份(2位)
                // console.log(myDate.getFullYear());    //获取完整的年份(4位,1970-????)
                // console.log(myDate.getMonth());       //获取当前月份(0-11,0代表1月)
                // console.log(myDate.getDate()); 
                var year = myDate.getFullYear();
                var month = myDate.getMonth()+1;
                var date = myDate.getDate();
                self.$refs[formName].validate((valid) => {
                    if (valid) {

                        this.$ajax.get(this.$domin + '/?_action=addRecord&token='+token
                            + '&id='+ this.recordForm.id
                            + '&date='+ this.recordForm.date
                            + '&num='+ this.recordForm.num
                            + '&work_id='+ this.recordForm.work_id
                            + '&type=0'
                        ).then(re => {
                            console.log(re.data);
                            console.log(re);
                            if(re.data.code == 0){
                                //localStorage.setItem('ms_username',self.rule_Form.username);
                                // self.$router.push('/news');
                                this.$message.success('工时记录');
                            }else{
                                 //self.$router.push('/readme');
                                this.$message.error('工时记录失败~');
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
