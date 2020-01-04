<template>
    <div>
        <!-- 上方按钮 -->
        <el-button size="small" type="primary" @click="toAddHandler">添加</el-button>
        <el-button size="small" type="danger">批量删除</el-button>
                
        <el-table :data="address">    
            <!-- //修改绑定数据 -->
            <!-- 动态绑定数据 -->
            <el-table-column  fixed="left" type="selection" >
                
            </el-table-column>
            <el-table-column  prop="id" label="编号" fixed="left">

            </el-table-column>
            <el-table-column  prop="customerId" label="顾客编号">

            </el-table-column>
            <el-table-column prop="province" label="省" fixed="left">

            </el-table-column>
            <el-table-column  prop="city" label="市">
                
            </el-table-column>
            <el-table-column  prop="area" label="区域">

            </el-table-column>
            <el-table-column  prop="address" label="具体地址">

            </el-table-column>
            <el-table-column  prop="telephone" label="手机号">

            </el-table-column>


            <el-table-column label="操作" fixed="right">
                <template v-slot="slot">    
                    
                    <a href="" @click.prevent="delet(slot.row.id)">删除</a>
                        <a href="" @click.prevent="updata(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
    
        <!-- 对话框 -->
        <el-dialog :title="title" :visible.sync="visible" width="60%" >
        
        <el-form label-width="80px" :model="form">
            <el-form-item label="顾客编号">
                <el-input v-model="form.customerId"/>
            </el-form-item>
            <el-form-item label="省">
                <el-input  v-model="form.province"/>
            </el-form-item>
            <el-form-item label="市">
                <el-input  v-model="form.city"/>
            </el-form-item>
            <el-form-item label="区域">
                <el-input  v-model="form.area"/>
            </el-form-item>
            <el-form-item label="具体地址">
                <el-input  v-model="form.address"/>
            </el-form-item>
            <el-form-item label="电话">
                <el-input  v-model="form.telephone"/>
            </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
            <el-button size="small" @click="close">取 消</el-button>
            <el-button size="small" type="primary" @click="submit">确 定</el-button>
        </span>
        </el-dialog>
        <!-- 对话框 -->
    </div>
</template>
<script>
import request from '@/utils/request'   //第三方库
import querystring from 'querystring'   //系统库
export default {
      created(){
          this.loadData();
      },
      methods:{//用于存放网页中需要的方法
       
        delet(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                //调用后台接口
                let url = "http://localhost:6677/address/deleteById?id="+id;
                request.get(url).then((response)=>{
                    //1.刷新数据，2.提示结果，3.
                this.loadData();
                this.$message({
                                type: 'success',
                                message:response.message
                            });
                })

            
        })
        },
        loadData(){
            let url="http://localhost:6677/address/findAll"
            request.get(url).then((response)=>{
                this.address=response.data;
            })
        },
        submit(){
            let url ="http://localhost:6677/address/saveOrUpdate  "
            //前端向后台发送请求，完成数据的保存数据
            request({
                url,
                method:'post',
                //告诉后台我的请求体中放的是查询字符串
                Headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据,将this.form转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求结束，模态框关闭
                this.close();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toAddHandler(){
            this.title="添加",
            this.form={},
            this.visible=true;
        },
        updata(row){
            this.form=row;
            this.visible=true;
        },
        close(){
            this.visible = false;
        }
    },
    data(){//用于存放网页中显示的数据
        return{
            visible:false,
            address:[],
            form:{},
            
        }
    }
}
</script>

<style scoped>

</style>