<template>
  <div class="hello">
      <el-form class="dataform" ref="form" :model="form" >
       <el-input v-model="form.name" placeholder="姓名"></el-input>
        <el-input v-model="form.age" placeholder="年龄"></el-input>
         <el-button style="width: 200px" type="primary" @click="add">添加</el-button>
      </el-form>

       <el-table
          :data="lists"
          style="width: 100%">
          <el-table-column
            prop="name"
            label="姓名"
            width="180">
          </el-table-column>
          <el-table-column
            prop="age"
            label="年龄"
            width="180">
          </el-table-column>

          <el-table-column label="操作">
            <template slot-scope="scope">
              <el-button
                size="mini"
                @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
              <el-button
                size="mini"
                type="danger"
                @click="handleDelete(scope.$index, scope.row)">删除</el-button>
            </template>
          </el-table-column>
          
        </el-table>



       <!--  嵌套表单 -->
        <el-dialog title="信息修改" :visible.sync="dialogFormVisible">
          <el-form :model="forms">
            <el-form-item label="姓名" :label-width="formLabelWidth">
              <el-input v-model="forms.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="年龄" :label-width="formLabelWidth">
              <el-input v-model="forms.age" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="dialogForm">确 定</el-button>
          </div>
        </el-dialog>

  </div>
</template>

<script>
 
  import axios from 'axios'
  import qs from 'qs'
export default {
  name: 'HelloWorld',
  data () {
    return {
       form:{
        name:'',
        age:'',
     },
      forms:{
        name:'',
        age:'',
        _id:''
     },
       lists:[],
       dialogFormVisible:false,
       formLabelWidth: '120px',
       name:'',
       age:''
    }
  },
    components: {

      },
    mounted(){
      this.sel();
    },
    methods:{  
        //信息添加
        add(){
            if(this.form.name&&this.form.age){
                this.$axios({
                method: 'post',
                url:'/api/user',
                data:{
                   name:this.form.name,
                   age:this.form.age
                }
                }).then((res)=>{
                    console.log(res);
                     this.sel()
                });
                }else{
                   this.$message({
                    message: '内容必填',
                    type: 'warning'
                  });
                }
        },

        //初始查找
        sel(){
        this.$axios.get('/api/user')
          .then((res)=>{
              this.lists = res.data
          })
        },


        //修改信息
        dialogForm(){
            this.$axios.put('api/user/'+this.forms._id,{
              name:this.forms.name,
              age:this.forms.age,
              sex: '',
              address: '',
              dowhat: '',
              favourite: '',
              explain: ''
            })
            .then((res)=>{
                      this.$message({
                      showClose: true,
                      message: '信息已更新',
                      type: 'success'
                    });
                      this.dialogFormVisible=false;
                     this.sel()
                });
        },



         handleEdit(index, row) {
          console.log(index, row);
          this.dialogFormVisible=true;
          this.forms.name=row.name;
          this.forms.age=row.age;
          this.forms._id=row._id
          },
          handleDelete(index, row) {

            this.$confirm('此操作将永久删除该条信息, 是否继续?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => {
                var _this= this;
                console.log(index, row);
                 this.$axios.delete('api/user/'+row._id)
                .then((res)=>{
                   console.log(res);
                   _this.sel()
                });
              this.$message({
                type: 'success',
                message: '删除成功!'
              });
            }).catch(() => {
              this.$message({
                type: 'info',
                message: '已取消删除'
              });          
            });
          
          }
          
        
      }

     
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  ul{
    list-style: none;
  }
  .hello{
    width: 600px;
    margin: auto;
  }
  .el-form-item__content{
    float: left;
  }
  .el-input{
    width: 20%
  }
  .dataform{
    position: fixed;
    width: 100%;
    bottom: 10%;
    left: 0;
    
    padding: 10px 0
  }
</style>
