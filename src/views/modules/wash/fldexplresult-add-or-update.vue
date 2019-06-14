<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="字段ID" prop="fldid">
      <el-input v-model="dataForm.fldid" placeholder="字段ID"></el-input>
    </el-form-item>
    <el-form-item label="检查项目ID" prop="chkProjid">
      <el-input v-model="dataForm.chkProjid" placeholder="检查项目ID"></el-input>
    </el-form-item>
    <el-form-item label="问题记录数" prop="isuRecQty">
      <el-input v-model="dataForm.isuRecQty" placeholder="问题记录数"></el-input>
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
          chkProjid: '',
          isuRecQty: ''
        },
        dataRule: {
          fldid: [
            { required: true, message: '字段ID不能为空', trigger: 'blur' }
          ],
          chkProjid: [
            { required: true, message: '检查项目ID不能为空', trigger: 'blur' }
          ],
          isuRecQty: [
            { required: true, message: '问题记录数不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/wash/fldexplresult/info/${this.dataForm.jobid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.fldid = data.fldexplresult.fldid
                this.dataForm.chkProjid = data.fldexplresult.chkProjid
                this.dataForm.isuRecQty = data.fldexplresult.isuRecQty
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
              url: this.$http.adornUrl(`/wash/fldexplresult/${!this.dataForm.jobid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'jobid': this.dataForm.jobid || undefined,
                'fldid': this.dataForm.fldid,
                'chkProjid': this.dataForm.chkProjid,
                'isuRecQty': this.dataForm.isuRecQty
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
