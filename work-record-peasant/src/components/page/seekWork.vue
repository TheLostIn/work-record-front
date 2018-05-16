<template>
   <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-setting"></i> 公告</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="ms-doc">
            <h3>工作列表</h3>
            <article>
            <div class="handle-box">
                <el-select  v-model="select_cate" placeholder="工作类别" @change="selectionchange" class="handle-select mr10">
                    <el-option v-for="work in allexperiences" :key="work" :label="work" :value="work"></el-option>
                    <!-- <el-option key="2" label="湖南省" value="湖南省"></el-option> -->
                </el-select>
            </div>
            <div :data="works" class="card">
                <el-card  v-for="work in works" :key="work" class="box-card">
                    <div slot="header" class="clearfix">
                        <span>{{ work.field}}</span>
                       <div v-if="work.should_upload==true">
                            <div v-if="work.expired==false">
                                <div v-if="work.has_upload==true">
                                    <el-button style="float: right; padding: 3px 0" type="text">已提交</el-button>
                                </div>
                                <div v-if="work.has_upload==false">
                                    <el-button style="float: right; padding: 3px 0" type="text">未提交</el-button>
                                </div>
                            </div>
                            <div v-if="work.expired==true">
                                <el-button class="expire" style="float: right; padding: 3px 0" type="text">已过期</el-button>
                            </div>
                       </div>
                       <div v-if="work.should_upload==false">
                           <el-button style="float: right; padding: 3px 0" type="text">不需要提交</el-button>
                       </div>
                        
                    </div>
                    <div  class="text item">
                        task_id: {{ work.task_id }}
                    </div>
                    <div  class="text item">
                        工作类型: {{ work.field }}
                    </div>
                    <div  class="text item">
                        薪水: {{ work.wage }}
                    </div>
                    <div  class="text item">
                        地点: {{ work.address }}
                    </div>
                    <div  class="text item">
                        手机号码: {{ work.phone }}
                    </div>
                    <div  class="text item">
                        住房: {{ work.house }}
                    </div>
                    <div  class="text item">
                        补贴: {{ work.welfare }}
                    </div>
                    <div  class="text item">
                        开始时间: {{ work.start_time }}
                    </div>
                    <div  class="text item">
                        公司id: {{ work.company_id }}
                    </div>
                    <div class="text item">
                        
                            <div class="text item">公司信息</div>
                            <div class="text item">公司名: {{ work.comapny_info.name }}</div>
                            <div class="text item">电话: {{ work.comapny_info.phone }}</div>
                            <div class="text item">邮箱: {{ work.comapny_info.mail }}</div>
                            <div class="text item">地址: {{ work.comapny_info.address }}</div>
                            <div class="text item">企业号: {{ work.comapny_info.number }}</div>
                        
                    </div>
                    <div  class="text item">
                    <el-button type="primary" @click="getthejob(work.task_id)">我要应聘</el-button>
                    </div>

            

                    
                </el-card>
                

            </div>
                        </article>
        </div>
        <div class="pagination">
            <el-pagination
                    @current-change ="handleCurrentChange"
                    layout="prev, pager, next"
                    :total="1000">
            </el-pagination>
        </div>
    </div>
</template>
<style>
    .box-card{
        margin: 20px;
    }
    .expire{
        color: red;
    }
     .ms-doc article{
        padding: 45px;
        word-wrap: break-word;
        background-color: #fff;
        border: 1px solid #ddd;
        border-bottom-right-radius: 3px;
        border-bottom-left-radius: 3px;
    }
    .ms-doc{
        width:100%;
        max-width: 980px;
        font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
    }
     .ms-doc h3{
        padding: 9px 10px 10px;
        margin: 0;
        font-size: 14px;
        line-height: 17px;
        background-color: #f5f5f5;
        border: 1px solid #d8d8d8;
        border-bottom: 0;
        border-radius: 3px 3px 0 0;
    }
</style>

<script>

// // (new Date()).Format("yyyy-MM-dd hh:mm:ss.S") ==> 2006-07-02 08:09:04.423   
// // (new Date()).Format("yyyy-M-d h:m:s.S")      ==> 2006-7-2 8:9:4.18   
// Date.prototype.Format = function(fmt)   
// { //author: meizz   
//   var o = {   
//     "M+" : this.getMonth()+1,                 //月份   
//     "d+" : this.getDate(),                    //日   
//     "h+" : this.getHours(),                   //小时   
//     "m+" : this.getMinutes(),                 //分   
//     "s+" : this.getSeconds(),                 //秒   
//     "q+" : Math.floor((this.getMonth()+3)/3), //季度   
//     "S"  : this.getMilliseconds()             //毫秒   
//   };   
//   if(/(y+)/.test(fmt))   
//     fmt=fmt.replace(RegExp.$1, (this.getFullYear()+"").substr(4 - RegExp.$1.length));   
//   for(var k in o)   
//     if(new RegExp("("+ k +")").test(fmt))   
//   fmt = fmt.replace(RegExp.$1, (RegExp.$1.length==1) ? (o[k]) : (("00"+ o[k]).substr((""+ o[k]).length)));   
//   return fmt;   
// }  
    export default {
        data() {
            return {
                url: '../../../static/vuetable.json',
                works: [],
                cur_page: 1,
                multipleSelection: [],
                activeName: '1',
                token:'',
                uploadUrl:'',
                allexperiences: ['装修施工', '建筑施工', '家装主材安装', '门窗玻璃安装', '隔墙吊顶', '家具软装维修', '电器安装维修', '家政服务', '管道疏通', '园林绿化', '路桥建设', '其他'],
                select_word:'',
                select_cate:'',
            }
        },
        created(){
            this.token = localStorage.getItem('token');
            this.uploadUrl = this.$domin+'/work-system/api/index.php?_action=upload&token=' + this.token;
            console.log(this.uploadUrl);
            console.log('ppppqweq');
            // this.getData();
            //  console.log(this.works);
        },
        methods: {
            handleCurrentChange(val){
                this.cur_page = val;
                this.getData();
            },
            selectionchange(val){
                console.log('ppppp  change');
                console.log(val);
                this.select_cate = val;
                console.log(val)
                this.getData();

            
                
            },
            getthejob(task_id)
            {
                let token = localStorage.getItem('token');
                var url = this.$domin + '/?_action=applyJob&token='+token+'&work_id='+task_id;
               
                this.$ajax.get(url,{
                }).then(re => {
                    // this.works = re.data.data.work;
                   console.log(re);
                   var flag = re.data.data.status;
                    if(flag == 1)
                    {
                            this.$message.success('申请成功');
                    }
                    else if (flag==-1)
                    {
                            this.$message.error('重复申请');
                    }
                    else
                    {
                             this.$message.error('返回不知道是什么');
                    }
                //    console.log(this.works);
                    // console.log('ppp');
                });
            },
             imageuploaded(res) {
                console.log(res);
                this.getData();
            },
            handleError(){
                this.$notify.error({
                    title: '上传失败',
                    message: '图片上传接口上传失败，可更改为自己的服务器接口'
                });
            },
            upload(w_id){
                this.activeName = w_id;
            },
            getData(){
                let token = localStorage.getItem('token');
                var url = this.$domin + '/?_action=listWork&token='+token+'&type='+this.select_cate +'&page='+this.cur_page;
               
                this.$ajax.get(url,{
                    
                }).then(re => {
                    this.works = re.data.data.work;
                 //   console.log(re.data.data.works);
                   console.log(this.works);
                    console.log('ppp');
                })
            },
             open3() {
                const h = this.$createElement;
        this.$msgbox({
          title: '消息',
          message: h('p', null, [
            h('span', null, '内容可以是 '),
            h('i', { style: 'color: teal' }, 'VNode'),
            h('input',{type:'file'},'')
          ]),
          showCancelButton: true,
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          beforeClose: (action, instance, done) => {
            if (action === 'confirm') {
              instance.confirmButtonLoading = true;
              instance.confirmButtonText = '执行中...';
              setTimeout(() => {
                done();
                setTimeout(() => {
                  instance.confirmButtonLoading = false;
                }, 300);
              }, 3000);
            } else {
              done();
            }
          }
        }).then(action => {
          this.$message({
            type: 'info',
            message: 'action: ' + action
          });
        });

      }
           
        }
    }
</script>

<style scoped>
.handle-box{
    margin-bottom: 20px;
}
.handle-del{
    border-color: #bfcbd9;
    color: #999;
}
.handle-select{
    width: 120px;
}
.handle-input{
    width: 300px;
    display: inline-block;
}
  .text {
    font-size: 14px;
  }

  .item {
    margin-bottom: 18px;
  }

  .clearfix:before,
  .clearfix:after {
    display: table;
    content: "";
  }
  .clearfix:after {
    clear: both
  }

  .box-card {
    width: 480px;
  }
</style>
