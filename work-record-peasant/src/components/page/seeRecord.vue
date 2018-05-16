<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>基础表格</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <el-table :data="data" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="work_id" label="工作id">
            </el-table-column>
            <el-table-column prop="num" label="时长（小时）" sortable width="150">
            </el-table-column>
            <el-table-column prop="date" label="时间" sortable width="150">
            </el-table-column>
            <el-table-column prop="add_time" label="添加时间" sortable width="150">
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
            this.getallrecord();
        },
        computed: {
            data(){
                const self = this;
                return self.tableData.filter(function(d){
                    console.log(d);
                        d.add_time =  (new Date(d.add_time*1000)).Format("yyyy-M-d h:m:s.S");

                    return d;
                })
            }
        },
        methods: {

            getallrecord(tablerowdata){
                let self = this;
                // console.log(tablerowdata['user_id']);
                // var user_id = tablerowdata['user_id'];
                let token = localStorage.getItem('token');
                var url = this.$domin + '/?_action=getRecord&token='+token;
                console.log(url);
                self.$axios.get(url).then((res) => {
                    self.tableData = res.data.data;
                    console.log(res);
                   // console.log(self.tableData);
                });
                console.log(user_id);
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