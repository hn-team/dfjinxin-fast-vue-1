<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="数据表ID" prop="dataTblid">
      <el-input v-model="dataForm.dataTblid" placeholder="数据表ID"></el-input>
    </el-form-item>
    <el-form-item label="数据片日期" prop="dpDt">
      <el-input v-model="dataForm.dpDt" placeholder="数据片日期"></el-input>
    </el-form-item>
    <el-form-item label="数据片路径" prop="dpPath">
      <el-input v-model="dataForm.dpPath" placeholder="数据片路径"></el-input>
    </el-form-item>
    <el-form-item label="记录数" prop="recQty">
      <el-input v-model="dataForm.recQty" placeholder="记录数"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          dpid: 0,
          dataTblid: '',
          dpDt: '',
          dpPath: '',
          recQty: ''
        },
        dataRule: {
          dataTblid: [
            { required: true, message: '数据表ID不能为空', trigger: 'blur' }
          ],
          dpDt: [
            { required: true, message: '数据片日期不能为空', trigger: 'blur' }
          ],
          dpPath: [
            { required: true, message: '数据片路径不能为空', trigger: 'blur' }
          ],
          recQty: [
            { required: true, message: '记录数不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.dpid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.dpid) {
            this.$http({
              url: this.$http.adornUrl(`/meta/dp/info/${this.dataForm.dpid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.dataTblid = data.dp.dataTblid
                this.dataForm.dpDt = data.dp.dpDt
                this.dataForm.dpPath = data.dp.dpPath
                this.dataForm.recQty = data.dp.recQty
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/meta/dp/${!this.dataForm.dpid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'dpid': this.dataForm.dpid || undefined,
                'dataTblid': this.dataForm.dataTblid,
                'dpDt': this.dataForm.dpDt,
                'dpPath': this.dataForm.dpPath,
                'recQty': this.dataForm.recQty
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
