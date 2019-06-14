<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="数据探查模板ID" prop="dataExplTmplid">
      <el-input v-model="dataForm.dataExplTmplid" placeholder="数据探查模板ID"></el-input>
    </el-form-item>
    <el-form-item label="项目类型" prop="projType">
      <el-input v-model="dataForm.projType" placeholder="项目类型"></el-input>
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
          fldid: 0,
          dataExplTmplid: '',
          projType: ''
        },
        dataRule: {
          dataExplTmplid: [
            { required: true, message: '数据探查模板ID不能为空', trigger: 'blur' }
          ],
          projType: [
            { required: true, message: '项目类型不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.fldid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.fldid) {
            this.$http({
              url: this.$http.adornUrl(`/wash/datafldexplproj/info/${this.dataForm.fldid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.dataExplTmplid = data.datafldexplproj.dataExplTmplid
                this.dataForm.projType = data.datafldexplproj.projType
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
              url: this.$http.adornUrl(`/wash/datafldexplproj/${!this.dataForm.fldid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'fldid': this.dataForm.fldid || undefined,
                'dataExplTmplid': this.dataForm.dataExplTmplid,
                'projType': this.dataForm.projType
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
