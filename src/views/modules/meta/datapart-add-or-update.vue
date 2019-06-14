<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="分区类型ID" prop="partTypeid">
      <el-input v-model="dataForm.partTypeid" placeholder="分区类型ID"></el-input>
    </el-form-item>
    <el-form-item label="租户ID" prop="tnmtid">
      <el-input v-model="dataForm.tnmtid" placeholder="租户ID"></el-input>
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
          partid: 0,
          partTypeid: '',
          tnmtid: ''
        },
        dataRule: {
          partTypeid: [
            { required: true, message: '分区类型ID不能为空', trigger: 'blur' }
          ],
          tnmtid: [
            { required: true, message: '租户ID不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.partid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.partid) {
            this.$http({
              url: this.$http.adornUrl(`/meta/datapart/info/${this.dataForm.partid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.partTypeid = data.datapart.partTypeid
                this.dataForm.tnmtid = data.datapart.tnmtid
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
              url: this.$http.adornUrl(`/meta/datapart/${!this.dataForm.partid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'partid': this.dataForm.partid || undefined,
                'partTypeid': this.dataForm.partTypeid,
                'tnmtid': this.dataForm.tnmtid
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
