<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="字段ID" prop="fldid">
      <el-input v-model="dataForm.fldid" placeholder="字段ID"></el-input>
    </el-form-item>
    <el-form-item label="数据清洗运算ID" prop="dataWashCmpuid">
      <el-input v-model="dataForm.dataWashCmpuid" placeholder="数据清洗运算ID"></el-input>
    </el-form-item>
    <el-form-item label="检查项目ID" prop="chkProjid">
      <el-input v-model="dataForm.chkProjid" placeholder="检查项目ID"></el-input>
    </el-form-item>
    <el-form-item label="清洗记录数" prop="washRecQty">
      <el-input v-model="dataForm.washRecQty" placeholder="清洗记录数"></el-input>
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
          jobid: 0,
          fldid: '',
          dataWashCmpuid: '',
          chkProjid: '',
          washRecQty: ''
        },
        dataRule: {
          fldid: [
            { required: true, message: '字段ID不能为空', trigger: 'blur' }
          ],
          dataWashCmpuid: [
            { required: true, message: '数据清洗运算ID不能为空', trigger: 'blur' }
          ],
          chkProjid: [
            { required: true, message: '检查项目ID不能为空', trigger: 'blur' }
          ],
          washRecQty: [
            { required: true, message: '清洗记录数不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.jobid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.jobid) {
            this.$http({
              url: this.$http.adornUrl(`/wash/fldwashresult/info/${this.dataForm.jobid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.fldid = data.fldwashresult.fldid
                this.dataForm.dataWashCmpuid = data.fldwashresult.dataWashCmpuid
                this.dataForm.chkProjid = data.fldwashresult.chkProjid
                this.dataForm.washRecQty = data.fldwashresult.washRecQty
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
              url: this.$http.adornUrl(`/wash/fldwashresult/${!this.dataForm.jobid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'jobid': this.dataForm.jobid || undefined,
                'fldid': this.dataForm.fldid,
                'dataWashCmpuid': this.dataForm.dataWashCmpuid,
                'chkProjid': this.dataForm.chkProjid,
                'washRecQty': this.dataForm.washRecQty
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
