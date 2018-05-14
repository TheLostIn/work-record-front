<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>基础表格</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="handle-box">
            <el-button type="primary" icon="el-icon-download" class="handle-del mr10" @click="downloadAll">批量下载(未上传作业自动提醒)</el-button>
            <el-select  v-model="select_cate" placeholder="作业id" @change="selectionchange" class="handle-select mr10">
                <el-option v-for="work in work_ids" :key="work.id" :label="work.work_name" :value="work.id"></el-option>
                <!-- <el-option key="2" label="湖南省" value="湖南省"></el-option> -->
            </el-select>
            <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
            <el-button type="primary" icon="search" @click="search">搜索</el-button>
        </div>
        <el-table :data="data" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="worker_id" label="worker_id">
            </el-table-column>
            <el-table-column prop="id" label="id" sortable width="150">
            </el-table-column>
            <el-table-column prop="status" label="status" sortable width="150">
            </el-table-column>
            <!-- <el-table-column prop="add_time" label="add_time" sortable width="150">
            </el-table-column> -->
            <!-- <el-table-column prop="has_uplaod" label="是否已提交" sortable width="150">
            </el-table-column> -->
            <el-table-column prop="同意应聘" label="同意应聘" width="120">
                <template scope="scope">
                    <el-button size="small"
                            @click="reply(scope.$index, scope.row,1)">同意</el-button>
                    <el-button size="small" type="danger"
                            @click="reply(scope.$index, scope.row,-1)">拒绝</el-button>
                </template>
            </el-table-column>

        </el-table>
        <div class="pagination">
            <el-pagination
                    @current-change ="handleCurrentChange"
                    layout="prev, pager, next"
                    :total="1000">
            </el-pagination>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import JSZip from 'jszip'
import FileSaver from 'file-saver'

const getFile = url => {
    return new Promise((resolve, reject) => {
        axios({
            method:'get',
            url,
            responseType: 'arraybuffer'
        }).then(data => {
            resolve(data.data)
        }).catch(error => {
            reject(error.toString())
        })
    })
}





    export default {
        data() {
            return {
                url: '',
                tableData: [],
                token:'',
            
            }
        },
        created(){
            this.token = localStorage.getItem('token');
            this.getallworkbyid();
        },
        computed: {
            data(){
                const self = this;
                return self.tableData.filter(function(d){
                    console.log(d);
                        // d.add_time =  (new Date(d.add_time*1000)).Format("yyyy-M-d h:m:s.S");

                    return d;
                })
            }
        },
        methods: {
            reply(index,row,status){
                let self = this;
                let token = localStorage.getItem('token');
                var url = this.$domin + '/?_action=handleApply&token='+token+'&apply_id='+row.id+'&sure='+status;
                console.log(row);
                self.$axios.get(url).then((res) => {
                    if(res.data.data.status==1&&status==1)
                    {
                        this.$message.success('接受应聘成功~')
                    }else if(res.data.data.status==1&&status==-1)
                    {
                        this.$message.success('拒绝应聘成功~')
                    }
                    // self.tableData = res.data.data;
                    console.log(res.data.data.statuss);
                   // console.log(self.tableData);
                });
                this.getallworkbyid()

            },
            getallworkbyid(tablerowdata){
                let self = this;
                // console.log(tablerowdata['user_id']);
                // var user_id = tablerowdata['user_id'];
                let token = localStorage.getItem('token');
                let task_id = localStorage.getItem('task_id');
                var url = this.$domin + '/?_action=allApplyByWorkId&token='+token+'&work_id='+task_id;
                console.log(url);
                self.$axios.get(url).then((res) => {
                    self.tableData = res.data.data;
                    console.log(self.tableData);
                   // console.log(self.tableData);
                });
                // console.log(user_id);
            }
        }
    }
</script>

<style scoped>
.handle-box{
    margin-bottom: 20px;
}
.handle-select{
    width: 120px;
}
.handle-input{
    width: 300px;
    display: inline-block;
}
</style>