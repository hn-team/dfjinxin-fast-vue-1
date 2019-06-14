<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="调度作业ID" prop="schdJobid">
      <el-input v-model="dataForm.schdJobid" placeholder="调度作业ID"></el-input>
    </el-form-item>
    <el-form-item label="作业状态" prop="jobStus">
      <el-input v-model="dataForm.jobStus" placeholder="作业状态"></el-input>
    </el-form-item>
    <el-form-item label="作业类型" prop="jobType">
      <el-input v-model="dataForm.jobType" placeholder="作业类型"></el-input>
    </el-form-item>
    <el-form-item label="启动时间" prop="startTm">
      <el-input v-model="dataForm.startTm" placeholder="启动时间"></el-input>
    </el-form-item>
    <el-form-item label="刷新时间" prop="rfrshTm">
      <el-input v-model="dataForm.rfrshTm" placeholder="刷新时间"></el-input>
    </el-form-item>
    <el-form-item label="数据表ID" prop="dataTblid">
      <el-input v-model="dataForm.dataTblid" placeholder="数据表ID"></el-input>
    </el-form-item>
    <el-form-item label="数据起始日期" prop="dataStartDt">
      <el-input v-model="dataForm.dataStartDt" placeholder="数据起始日期"></el-input>
    </el-form-item>
    <el-form-item label="数据终止日期" prop="dataTerminateDt">
      <el-input v-model="dataForm.dataTerminateDt" placeholder="数据终止日期"></el-input>
    </el-form-item>
    <el-form-item label="数据抽样百分比" prop="dataSplPct">
      <el-input v-model="dataForm.dataSplPct" placeholder="数据抽样百分比"></el-input>
    </el-form-item>
    <el-form-item label="处理记录总数" prop="procRecTotalQty">
      <el-input v-model="dataForm.procRecTotalQty" placeholder="处理记录总数"></el-input>
    </el-form-item>
    <el-form-item label="完全重复记录数" prop="allDuplRecQty">
      <el-input v-model="dataForm.allDuplRecQty" placeholder="完全重复记录数"></el-input>
    </el-form-item>
    <el-form-item label="主键重复记录数" prop="pkDuplRecQty">
      <el-input v-model="dataForm.pkDuplRecQty" placeholder="主键重复记录数"></el-input>
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
          schdJobid: '',
          jobStus: '',
          jobType: '',
          startTm: '',
          rfrshTm: '',
          dataTblid: '',
          dataStartDt: '',
          dataTerminateDt: '',
          dataSplPct: '',
          procRecTotalQty: '',
          allDuplRecQty: '',
          pkDuplRecQty: ''
        },
        dataRule: {
          schdJobid: [
            { required: true, message: '调度作业ID不能为空', trigger: 'blur' }
          ],
          jobStus: [
            { required: true, message: '作业状态不能为空', trigger: 'blur' }
          ],
          jobType: [
            { required: true, message: '作业类型不能为空', trigger: 'blur' }
          ],
          startTm: [
            { required: true, message: '启动时间不能为空', trigger: 'blur' }
          ],
          rfrshTm: [
            { required: true, message: '刷新时间不能为空', trigger: 'blur' }
          ],
          dataTblid: [
            { required: true, message: '数据表ID不能为空', trigger: 'blur' }
          ],
          dataStartDt: [
            { required: true, message: '数据起始日期不能为空', trigger: 'blur' }
          ],
          dataTerminateDt: [
            { required: true, message: '数据终止日期不能为空', trigger: 'blur' }
          ],
          dataSplPct: [
            { required: true, message: '数据抽样百分比不能为空', trigger: 'blur' }
          ],
          procRecTotalQty: [
            { required: true, message: '处理记录总数不能为空', trigger: 'blur' }
          ],
          allDuplRecQty: [
            { required: true, message: '完全重复记录数不能为空', trigger: 'blur' }
          ],
          pkDuplRecQty: [
            { required: true, message: '主键重复记录数不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/meta/dataprocjob/info/${this.dataForm.jobid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.schdJobid = data.dataprocjob.schdJobid
                this.dataForm.jobStus = data.dataprocjob.jobStus
                this.dataForm.jobType = data.dataprocjob.jobType
                this.dataForm.startTm = data.dataprocjob.startTm
                this.dataForm.rfrshTm = data.dataprocjob.rfrshTm
                this.dataForm.dataTblid = data.dataprocjob.dataTblid
                this.dataForm.dataStartDt = data.dataprocjob.dataStartDt
                this.dataForm.dataTerminateDt = data.dataprocjob.dataTerminateDt
                this.dataForm.dataSplPct = data.dataprocjob.dataSplPct
                this.dataForm.procRecTotalQty = data.dataprocjob.procRecTotalQty
                this.dataForm.allDuplRecQty = data.dataprocjob.allDuplRecQty
                this.dataForm.pkDuplRecQty = data.dataprocjob.pkDuplRecQty
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
              url: this.$http.adornUrl(`/meta/dataprocjob/${!this.dataForm.jobid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'jobid': this.dataForm.jobid || undefined,
                'schdJobid': this.dataForm.schdJobid,
                'jobStus': this.dataForm.jobStus,
                'jobType': this.dataForm.jobType,
                'startTm': this.dataForm.startTm,
                'rfrshTm': this.dataForm.rfrshTm,
                'dataTblid': this.dataForm.dataTblid,
                'dataStartDt': this.dataForm.dataStartDt,
                'dataTerminateDt': this.dataForm.dataTerminateDt,
                'dataSplPct': this.dataForm.dataSplPct,
                'procRecTotalQty': this.dataForm.procRecTotalQty,
                'allDuplRecQty': this.dataForm.allDuplRecQty,
                'pkDuplRecQty': this.dataForm.pkDuplRecQty
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
