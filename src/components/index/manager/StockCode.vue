<template>
  <div :style="{'height':fullWindowPx }">
    <div class="leftsider" :class={active:isActiveStatis} >
      <div class="split">
        <div slot="top" class="split-pane ">
          <p class="pstyle" >
            <!--选择文件中必须有｛经度、纬度｝或｛X、Y｝或｛lng、lat｝,列数不超过10行，不超过1M-->
            导入数据第一列为股票编码，第二列为股票名称
          </p>
          <br>
          <a href="javascript:;" class="uploadfile">选择文件
            <input type="file" ref="upload" accept=".xls,.xlsx"  name="" id="">
          </a>
          <Button class="uploadbutton" @click = "clearData">清除数据</Button>
          <Divider dashed />
          <Button type="primary" size="large" @click="exportData(1)"><Icon type="ios-download-outline"></Icon> 导出数据</Button>
        </div>
        <div slot="bottom" class="split-pane">
        </div>
      </div>
    </div>
    <div class = "rightsider" :class={active:isActiveStatis} >
      <div class="spin-container"  >
        <Table border :loading="loading" :height="getClientHeight"  :columns="columnsInput" :data="dataInput" ref="table">
        </Table>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable eqeqeq */

import XLSX from 'xlsx'
export default {
  props: ['isActiveStatis'],
  name: 'StatisWindow',
  computed: {
    getClientHeight: function () {
      let clientHeight = document.documentElement.clientHeight
      window.onresize = () => {
        clientHeight = document.documentElement.clientHeight
        return clientHeight
      }
      clientHeight = clientHeight - 100
      console.log(clientHeight)
      return clientHeight
    },
    halfClientHeight: function () {
      return this.getClientHeight / 2
    },
    fullWindowPx: function () {
      return this.getClientHeight + 'px'
    }
  },
  data () {
    return {
      columnsInput: [
      ],
      dataInput: [
      ],
      split2: 0.5,
      file: null,
      loadingStatus: false,
      screenHeight: 1000,
      loading: false
    }
  },
  mounted () {
    this.$refs.upload.addEventListener('change', e => { // 绑定监听表格导入事件
      this.readExcel(e)
    })
    this.screenHeight = document.documentElement.clientHeight
    console.log(this.screenHeight)
  },
  methods: {
    readExcel (e) { // 表格导入
      try {
        this.dataInput = []// 清空接收数据
        this.columnsInput = []
        var files = null
        var that = this
        files = e.target.files
        that.loading = true
        var max = files[0].size
        if (max > 1 * 1024 * 1024) {
          this.$Message.error('上传文件不能超过1M')
          that.loading = false
          return
        }
        if (files.length <= 0) { // 如果没有文件名
          return false
        } else if (!/\.(xls|xlsx)$/.test(files[0].name.toLowerCase())) {
          this.$Message.error('上传格式不正确，请上传xls或者xlsx格式')
          that.loading = false
          return false
        }
        const fileReader = new FileReader()
        var ws = []
        fileReader.readAsBinaryString(files[0])
        fileReader.onloadend = (ev) => {
          const data = ev.target.result
          const workbook = XLSX.read(data, {
            type: 'binary'
          })
          const wsname = workbook.SheetNames[0]// 取第一张表
          ws = XLSX.utils.sheet_to_json(workbook.Sheets[wsname])// 生成json表格内容
          console.log(ws)
          if (ws.length > 5000) {
            this.$Message.error('上传数据不超过5000行')
            return
          }
          const range = XLSX.utils.decode_range(workbook.Sheets[wsname]['!ref'])
          for (let c = range.s.c; c <= range.e.c; c++) {
            const header = XLSX.utils.encode_col(c) + '1'
            var colName = workbook.Sheets[wsname][header].v
            var headCol = {
              title: colName,
              key: colName
              /* width: 100 */
            }
            if (c == 1) {
              headCol['fixed'] = 'left'
            }
            that.columnsInput.push(headCol)
          }
          that.dataInput = ws
          that.loading = false
        }
      } catch (err) {
        that.loading = false
        console.log(err)
      }
    },
    exportData (type) {
      if (type === 1) {
        this.$refs.table.exportCsv({
          filename: 'exportdata_new'
        })
      }
    },
    clearData () {
      this.dataInput = []// 清空接收数据
      this.columnsInput = []
      this.$refs.upload.value = null
      this.loading = false
    },
    locationPoints () {
      if (!this.loading) {
        this.loading = true
        setTimeout(this.locationPointsEx, 500)
      } else {
        this.$Message.error('正在执行')
      }

      // this.locationPointsEx()
    }
  }
}
</script>

<style scoped>
  .pstyle{
    font-family:Simsun;
    font-size:10px;
  }
  .select {
    width: 200px;
    color: #555;
  }
  .leftsider{
    width: 230px;
    height: 100%;
    background: #E0E0E0;
    float: left;
    border:2px solid palegreen;
  }
  .leftsider.active{
    width: 0px;
    margin-left: -230px;
  }
  .rightsider{
    margin-left: 230px;
  }
  .rightsider.active{
    margin-left: 0px;
  }
  .split{
    /* height: 1200px;*/
    border: 1px solid #dcdee2;
  }
  .split-pane{
    padding: 10px;
  }
  .uploadbutton{
    display: inline-block;
    background: #D0EEFF;
    border: 1px solid #99D3F5;
    border-radius: 4px;
    padding: 4px 12px;
    float:right;
  }
  .uploadfile {
    position: relative;
    display: inline-block;
    background: #D0EEFF;
    border: 1px solid #99D3F5;
    border-radius: 4px;
    padding: 4px 12px;
    overflow: hidden;
    color: #1E88C7;
    text-decoration: none;
    text-indent: 0;
    line-height: 20px;
  }
  .uploadfile input {
    position: absolute;
    font-size: 100px;
    right: 0;
    top: 0;
    opacity: 0;
  }
  .uploadfile:hover {
    background: #AADFFD;
    border-color: #78C3F3;
    color: #004974;
    text-decoration: none;
  }
  .spin-container{
    width: 100%;
    hight: 300px;
    display: inline-block;
    position: relative;
    border: 1px solid #eee;
  }
</style>
