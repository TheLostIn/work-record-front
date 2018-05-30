<template>
<div>

    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>基础表格</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
         <div class="handle-box">
            <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">批量删除</el-button>
              <el-button class="button" @click="downloadFile(data)">导出</el-button>
            <el-select v-model="select_cate" placeholder="筛选工作id" class="handle-select mr10" @change="getrecordbyworkid">
                <!-- <el-option key="1" label="广东省" value="广东省"></el-option>
                <el-option key="2" label="湖南省" value="湖南省"></el-option> -->
                <el-option v-for="work in worklist" :key="work.work_id" :label="work.work_id" :value="work.work_id"></el-option>
               
            </el-select>
            <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
            <el-button type="primary" icon="search" @click="search">搜索</el-button>
        </div>
        <!-- <el-table :data="data" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange"> -->
        <el-table :data="data" border style="width: 100%" ref="multipleTable">
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
            <!-- <el-pagination
                    @current-change ="handleCurrentChange"
                    layout="prev, pager, next"
                    :total="1000">
            </el-pagination> -->
            <el-pagination
                    layout="prev, pager, next"
                    :total="1000">
            </el-pagination>
        </div>
    </div>
</div>

</template>

<script>
import axios from 'axios'
import JSZip from 'jszip'
import FileSaver from 'file-saver'
var XLSX = require('xlsx')

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
        name: 'Index',
        data() {
            return {
                url: '',
                tableData: [],
                token:'',
                worklist:[],
                select_cate:'',
                select_word:'',
                fullscreenLoading: false, // 加载中
                imFile: '', // 导入文件el
                outFile: '',  // 导出文件el
                errorDialog: false, // 错误信息弹窗
                errorMsg: '', // 错误信息内容
                outdata:[],
                excelData: [  // 测试数据
    {
      name: '红烧鱼', size: '大', taste: '微辣', price: '40', remain: '100'
    },
    {
      name: '麻辣小龙虾', size: '大', taste: '麻辣', price: '138', remain: '200'
    },
    {
      name: '清蒸小龙虾', size: '大', taste: '清淡', price: '138', remain: '200'
    },
    {
      name: '香辣小龙虾', size: '大', taste: '特辣', price: '138', remain: '200'
    },
    {
      name: '十三香小龙虾', size: '大', taste: '中辣', price: '138', remain: '108'
    },
    {
      name: '蒜蓉小龙虾', size: '大', taste: '中辣', price: '138', remain: '100'
    },
    {
      name: '凉拌牛肉', size: '中', taste: '中辣', price: '48', remain: '60'
    },
    {
      name: '虾仁寿司', size: '大', taste: '清淡', price: '29', remain: '无限'
    },
    {
      name: '海苔寿司', size: '大', taste: '微辣', price: '26', remain: '无限'
    },
    {
      name: '金针菇寿司', size: '大', taste: '清淡', price: '23', remain: '无限'
    },
    {
      name: '泡菜寿司', size: '大', taste: '微辣', price: '24', remain: '无限'
    },
    {
      name: '鳗鱼寿司', size: '大', taste: '清淡', price: '28', remain: '无限'
    },
    {
      name: '肉松寿司', size: '大', taste: '清淡', price: '22', remain: '无限'
    },
    {
      name: '三文鱼寿司', size: '大', taste: '清淡', price: '30', remain: '无限'
    },
    {
      name: '蛋黄寿司', size: '大', taste: '清淡', price: '20', remain: '无限'
    }]
            }
        },
        created(){
            this.token = localStorage.getItem('token');
            this.getallrecord();
        },
          mounted () {
            this.imFile = document.getElementById('imFile')
            this.outFile = document.getElementById('downlink')
            },
        computed: {
            data(){
                const self = this;
                return self.tableData.filter(function(d){
                    console.log(d);
                        d.add_time =  (new Date(d.add_time*1000)).Format("yyyy-M-d h:m:s.S");
                        if(self.select_cate=='')
                        {

                        }else{
                            d.work_id = self.select_cate;
                        }
                    return d;
                })
            }
        },
         methods: {
            outputXlsxFile (data, wscols, xlsxName) {
  /* convert state to workbook */
  const ws = XLSX.utils.aoa_to_sheet(data)
  ws['!cols'] = wscols
  const wb = XLSX.utils.book_new()
  XLSX.utils.book_append_sheet(wb, ws, xlsxName)
  /* generate file and send to client */
  XLSX.writeFile(wb, xlsxName + ".xlsx")
},

            getrecordbyworkid(){
                let self = this;
                // console.log(tablerowdata['user_id']);
                // var user_id = tablerowdata['user_id'];
                let token = localStorage.getItem('token');
                var url = this.$domin + '/?_action=getRecordsBy2Id&token='+token+'&work_id='+self.select_cate;
                console.log(url);
                /*
                * 这里 等接口
                */
                self.$axios.get(url).then((res) => {
                    self.tableData = res.data.data;
                    console.log(res);
                   // console.log(self.tableData);
                });
                // var a = '{"code":0,"data":[{"date":"1","num":"1","add_time":"1"},{"date":"1","num":"1","add_time":"2"},{"date":"20180911","num":"10","add_time":"12132"}]}';
                // a = eval('('+a+')');
                // console.log(a);
                // self.tableData = a.data;
            },
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
                var url = this.$domin + '/?_action=getWorkids&token='+token;
                self.$axios.get(url).then((res) => {
                    self.worklist = res.data.data;
                    console.log(res);
                   // console.log(self.tableData);
                });
            },
            downloadFile: function (rs) { // 点击导出按钮
  let data = [{}]
  for (let k in rs[0]) {
    data[0][k] = k
  }
  data = data.concat(rs)
  this.downloadExl(data, '菜单')
},
importFile: function () { // 导入excel
  this.fullscreenLoading = true
  let obj = this.imFile
  if (!obj.files) {
    this.fullscreenLoading = false
    return
  }
  var f = obj.files[0]
  var reader = new FileReader()
  let $t = this
  reader.onload = function (e) {
    var data = e.target.result
    if ($t.rABS) {
      $t.wb = XLSX.read(btoa(this.fixdata(data)), {  // 手动转化
        type: 'base64'
      })
    } else {
      $t.wb = XLSX.read(data, {
        type: 'binary'
      })
    }
    let json = XLSX.utils.sheet_to_json($t.wb.Sheets[$t.wb.SheetNames[0]])
    console.log(typeof json)
    $t.dealFile($t.analyzeData(json)) // analyzeData: 解析导入数据
  }
  if (this.rABS) {
    reader.readAsArrayBuffer(f)
  } else {
    reader.readAsBinaryString(f)
  }
},
downloadExl: function (json, downName, type) {  // 导出到excel
  let keyMap = [] // 获取键
  for (let k in json[0]) {
    keyMap.push(k)
  }
  console.info('keyMap', keyMap, json)
  let tmpdata = [] // 用来保存转换好的json
  json.map((v, i) => keyMap.map((k, j) => Object.assign({}, {
    v: v[k],
    position: (j > 25 ? this.getCharCol(j) : String.fromCharCode(65 + j)) + (i + 1)
  }))).reduce((prev, next) => prev.concat(next)).forEach(function (v) {
    tmpdata[v.position] = {
      v: v.v
    }
  })
  let outputPos = Object.keys(tmpdata)  // 设置区域,比如表格从A1到D10
  let tmpWB = {
    SheetNames: ['mySheet'], // 保存的表标题
    Sheets: {
      'mySheet': Object.assign({},
        tmpdata, // 内容
        {
          '!ref': outputPos[0] + ':' + outputPos[outputPos.length - 1] // 设置填充区域
        })
    }
  }
  let tmpDown = new Blob([this.s2ab(XLSX.write(tmpWB,
    {bookType: (type === undefined ? 'xlsx' : type), bookSST: false, type: 'binary'} // 这里的数据是用来定义导出的格式类型
  ))], {
    type: ''
  })  // 创建二进制对象写入转换好的字节流
  var href = URL.createObjectURL(tmpDown)  // 创建对象超链接
  this.outFile.download = downName + '.xlsx'  // 下载名称
  this.outFile.href = href  // 绑定a标签
  this.outFile.click()  // 模拟点击实现下载
  setTimeout(function () {  // 延时释放
    URL.revokeObjectURL(tmpDown) // 用URL.revokeObjectURL()来释放这个object URL
  }, 100)
},
analyzeData: function (data) {  // 此处可以解析导入数据
  return data
},
dealFile: function (data) {   // 处理导入的数据
  console.log(data)
  this.imFile.value = ''
  this.fullscreenLoading = false
  if (data.length <= 0) {
    this.errorDialog = true
    this.errorMsg = '请导入正确信息'
  } else {
    this.excelData = data
  }
},
s2ab: function (s) { // 字符串转字符流
  var buf = new ArrayBuffer(s.length)
  var view = new Uint8Array(buf)
  for (var i = 0; i !== s.length; ++i) {
    view[i] = s.charCodeAt(i) & 0xFF
  }
  return buf
},
getCharCol: function (n) { // 将指定的自然数转换为26进制表示。映射关系：[0-25] -> [A-Z]。
  let s = ''
  let m = 0
  while (n > 0) {
    m = n % 26 + 1
    s = String.fromCharCode(m + 64) + s
    n = (n - m) / 26
  }
  return s
},
fixdata: function (data) {  // 文件流转BinaryString
  var o = ''
  var l = 0
  var w = 10240
  for (; l < data.byteLength / w; ++l) {
    o += String.fromCharCode.apply(null, new Uint8Array(data.slice(l * w, l * w + w)))
  }
  o += String.fromCharCode.apply(null, new Uint8Array(data.slice(l * w)))
  return o
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