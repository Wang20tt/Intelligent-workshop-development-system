<template>
  <div id="modelAddForm-r">
    <el-form :model="modelAddForm" ref="model-add" label-position="right" label-width="80px" >
      <el-form-item label="模版分类" prop="classification"
                    :rules="{required: true, message: '请选择分类', trigger: 'blur'}">
        <el-select v-model="modelAddForm.classification" filterable placeholder="请选择" style="width: 100%;">
          <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="模版号" prop="templateId"
                    :rules="{required: true, message: '请输入模板号', trigger: 'change'}">
        <el-input v-model="modelAddForm.templateId" style="width: 100%;"></el-input>
      </el-form-item>

      <el-form-item label="模版名称" prop="templateName"
                    :rules="{required: true, message: '请输入名称', trigger: 'blur'}">
        <el-input v-model="modelAddForm.templateName" style="width: 100%;"></el-input>
      </el-form-item>


          <el-form-item v-for="(item, index) in modelAddForm.data"
                        :label="'属性'+(index+1)"
                        :key="item.id"
                        :prop="'data.' + index + '.content'"
                        :rules="{required: true, message: '请输入属性', trigger: 'blur'}">
            <el-input v-model="modelAddForm.data[index].content"
                      style="width: 250px"
                      placeholder="请输入属性"
                      filterable>
            </el-input>

            <el-button size="mini"
                       style="color: red; margin-left: 10px;"
                       @click="del(index)">删除</el-button>
          </el-form-item>


      <el-form-item>
        <el-button @click="add">添 加</el-button>
        <el-button type="primary" @click="onSubmit()">确 定</el-button>
      </el-form-item>
    </el-form>

  </div>
</template>

<script>
import Draggable from "vuedraggable"
import {request} from "@/network/request";

export default {
  name: "modelAdd",
  data() {
    return {
      num: 1,
      modelAddForm: {
        classification: '',//模版分类
        templateId: '',//模版号
        templateName: '',//模版名称
        data: [
          {id: 1, content: ''}
        ],
        comments: ''
      },

      dialogVisible: false,
      options: [
        {
          value: 'MaterialTemplate',
          label: '物料模版'
        }, {
          value: 'EquipmentTemplate',
          label: '设备模版'
        },
        // {
        //   value: 'ProductTemplate',
        //   label: '产品模版'
        // },
      ]
    }
  },

  methods: {
    add() {
      this.num++
      this.modelAddForm.data.push({id: this.num, content: ''})
    },
    del(index) {
      this.modelAddForm.data.splice(index, 1)
    },

    onSubmit() {
      this.validateForm()
    },

    resetForm(formName) {
      this.$refs[formName].resetFields();
    },

    validateForm() {
      let attributedata = []
      for(let i = 0; i < this.modelAddForm.data.length; i++)
      {
        attributedata.push(this.modelAddForm.data[i].content)
      }
      console.log(attributedata)
      let myurl=""
      if (this.modelAddForm.classification==="MaterialTemplate"){
        myurl="template/material/addMaterialTemplate"
      }
      if (this.modelAddForm.classification==="EquipmentTemplate"){
        myurl="template/equipment/addEquipmentTemplate"
      }
      if (this.modelAddForm.classification==="ProductTemplate"){
        myurl="/template/product/addProductTemplate"
      }
      console.log(myurl)
      this.$refs['model-add'].validate((valid) => {
        if (valid) {
          this.$confirm("是否提交？", '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {

            request({
              url: myurl,
              method: 'post',
              data: {
                id:Number(this.modelAddForm.templateId),//模板号
                name:this.modelAddForm.templateName,//模板名
                attribute:attributedata,//属性
                company_id:'',//公司
              }
            }).then(res => {
              if (res.data !== 'no') {
                this.resetForm('model-add')
                this.$message({
                  type: 'success',
                  message: '添加成功'
                })
              } else {
                this.$message({
                  type: 'error',
                  message: res.data
                })
              }
            }).catch(err => {
              console.log(err)
            });
          })
        } else {
          return false;
        }
      });
    },

  },
  components: {
    Draggable
  },

}
</script>

<style scoped>
#modelAddForm-r {
  margin-top: 50px;
  margin-left: 5%;
  margin-right: 40%;
}
</style>