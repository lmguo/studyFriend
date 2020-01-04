<template>
    <div>
        <h3>产品管理<br></h3>
        <el-button type="primary" @click="toAddHandler" size="small">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>

        <el-table ref="multipleTable" :data="product" tooltip-effect="dark" stripe>
            <el-table-column type="selection"></el-table-column>
            <el-table-column prop="id" label="编号">
                <!-- <template slot-scope="scope">{{ scope.row.date }}</template> -->
            </el-table-column>
            <el-table-column prop="name" label="产品名称" width="240"></el-table-column>
            <el-table-column prop="price" label="价格" width="240" show-overflow-tooltip></el-table-column>
            <el-table-column prop="description" label="描述" width="240" show-overflow-tooltip></el-table-column>
            <el-table-column prop="categoryId" label="所属产品" width="240" show-overflow-tooltip></el-table-column>
            <el-table-column prop="address" label="操作" width="240" show-overflow-tooltip>
                <template v-slot="slot">
                    <i @click="upDataHandler(slot.row)" class="el-icon-edit-outline" ></i>
                    <i @click="deleteHandler(slot.row.id)" class="el-icon-delete" ></i>
                    <!-- <el-button >详情</el-button> -->
                </template>
            </el-table-column>
        </el-table>
         <el-dialog
            title="添加项目"
            :visible.sync="visible"
            width="60%">
             <el-form :model="ruleform" :rules="rules" ref="ruleform" label-width="80px">
                <el-form-item prop="id" label="编号">
                    <el-input v-model="ruleform.id"></el-input>
                </el-form-item>
                <el-form-item prop="name" label="产品名称">
                    <el-input v-model="ruleform.name"></el-input>
                </el-form-item>
                <el-form-item prop="price" label="价格">
                    <el-input v-model="ruleform.price"></el-input>
                </el-form-item>
                <el-form-item prop="description" label="描述">
                    <el-input v-model="ruleform.description"></el-input>
                </el-form-item>
                <el-form-item prop="categoryId" label="所属类别">
                    <el-input v-model="ruleform.categoryId"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="closeModelHandler">取 消</el-button>
                <el-button type="primary" @click="submitHandler('ruleform')">确 定</el-button>
            </span>
         </el-dialog>
   </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            product:[],
            ruleform:{},
            visible:false,
            delivery: false,
            rules:{
                id: [
                { required: true, message: '请输入产品编号', trigger: 'blur' }
                ],
                name: [
                { required: true, message: '请输入产品名称', trigger: 'blur' }
                ],
                price: [
                { required: true, message: '请输入产品价格', trigger: 'blur' }
                ],
                description: [
                { required: true, message: '请输入产品描述', trigger: 'blur' }
                ],
                categoryId: [
                { required: true, message: '请输入所属类别序号', trigger: 'blur' }
                
                ],
            }

        }
    },
    //方法块
    methods:{
        loadData(){
            let url = "http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
                this.product = response.data;
            })
        },
        upDataHandler(row){
            this.ruleform=row;
            this.visible=true;
        },
        deleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
            }).then(() => {
                    let url = "http://localhost:6677/product/deleteById"
                    request.get(url+"?id="+id).then((response)=>{
                        this.loadData()
                        this.$message({
                        type: 'success',
                        message:response.message
                        });
                    })
                    
                })
        },
        toAddHandler(){
            this.visible=true
            this.ruleform={
                type:"product"
            }
        },
        closeModelHandler(){
            this.visible=false
        },
        submitHandler(formName){
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    let url="http://localhost:6677/product/saveOrUpdate"
                    request({
                        url,
                        method:"post",
                        Headers:{
                        "Content-Type":"application/x-www-form-urlencoded"
                        },
                        data:querystring.stringify(this.ruleform)
                    }).then((response)=>{
                        this.loadData()
                        this.closeModelHandler()
                        this.$message({
                            type:"success",
                            message:response.message
                        })
                     })
                } else {
                    console.log('error submit!!');
                    return false;
                }
            });           
        }
    },
    created(){
        this.loadData()
    },
}
</script>
<style scoped>

</style>