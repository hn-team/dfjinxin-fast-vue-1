<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="租户名称" prop="tnmtNm">
      <el-input v-model="dataForm.tnmtNm" placeholder="租户名称"></el-input>
    </el-form-item>
    <el-form-item label="租户中文名称" prop="tnmtCnNm">
      <el-input v-model="dataForm.tnmtCnNm" placeholder="租户中文名称"></el-input>
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
          tnmtid: 0,
          tnmtNm: '',
          tnmtCnNm: ''
        },
        dataRule: {
          tnmtNm: [
            { required: true, message: '租户名称不能为空', trigger: 'blur' }
          ],
          tnmtCnNm: [
            { required: true, message: '租户中文名称不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.tnmtid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.tnmtid) {
            this.$http({
              url: this.$http.adornUrl(`/meta/tnmt/info/${this.dataForm.tnmtid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.tnmtNm = data.tnmt.tnmtNm
                this.dataForm.tnmtCnNm = data.tnmt.tnmtCnNm
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
              url: this.$http.adornUrl(`/meta/tnmt/${!this.dataForm.tnmtid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'tnmtid': this.dataForm.tnmtid || undefined,
                'tnmtNm': this.dataForm.tnmtNm,
                'tnmtCnNm': this.dataForm.tnmtCnNm
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
