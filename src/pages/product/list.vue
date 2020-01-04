<template>
    <div>
        <h3>产品管理<br></h3>   
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
                { required: true, message: '请输入产品编号', trigger: 'blur' },
                { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
                ],
                name: [
                { required: true, message: '请输入产品名称', trigger: 'blur' },
                { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
                ],
                price: [
                { required: true, message: '请输入产品价格', trigger: 'blur' },
                { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
                ],
                description: [
                { required: true, message: '请输入产品描述', trigger: 'blur' },
                { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
                ],
                categoryId: [
                { required: true, message: '请输入所属类别序号', trigger: 'blur' },
                { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
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
            this.form={
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