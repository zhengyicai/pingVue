<template> 
 <div class="block">   
	 	<el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				<el-form-item style="width:300px">
					<el-input v-model="equipmentNo"  @keyup.enter.native="query"  placeholder="请输入设备[编号|名称]" style="width:300px"></el-input>
				</el-form-item>

                <!-- <el-form-item style="width:200px">
					<el-select  v-model="type" placeholder="请选择展示类型">
                            <el-option value="" label="">全部</el-option>
                             <el-option
                                        v-for="item in options"
                                        :key="item.value"
                                        :label="item.label"
                                        :value="item.value"
                                        :disabled="item.disabled">
                                </el-option>
                    </el-select>
				</el-form-item> -->



                 
				<el-form-item  >
					<el-button type="primary" v-on:click="query">查询</el-button>
				</el-form-item>
                <!-- <el-form-item  >
					<el-button type="success" v-on:click="query">刷新</el-button>
				</el-form-item> -->
				
				
			</el-form>
		</el-col>
        <!-- <el-col :span="4" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				
				<el-form-item  style="text-align:right">
					<el-button type="primary" @click="add()">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col> -->
      
        
		<el-table :data="datalist" highlight-current-row v-loading="listLoading" style="width: 100%;">
		
			<el-table-column prop="equipmentNo" label="设备编号" width="250" sortable>
                
			</el-table-column>
			<el-table-column prop="equipmentName1" label="设备名称" width="150" sortable>
			</el-table-column>
            <!-- <el-table-column prop="equipmentName" label="显示格式" width="200" sortable>
                <template slot-scope="scope">{{ showType(scope.row.type)}}</template>
			</el-table-column> -->
            <!-- <el-table-column prop="fileUrl" label="文件地址" width="250" sortable>
			</el-table-column> -->

            <el-table-column prop="imgUrl" label="设备logo" width="150" sortable>
                <template slot-scope="scope">

                    <div  v-if="scope.row.imgUrl!=null && scope.row.imgUrl!=''"><img :src="files+scope.row.imgUrl" style="height:30%; width:30%"></div>
                     <!-- <div  v-if="scope.row.imgUrl==''"><img :src="files+scope.row.imgUrl" style="height:30%; width:30%"></div> -->
                  
                </template>
			</el-table-column>


            <el-table-column  label="创建时间" width="170">
				<template slot-scope="scope">{{ scope.row.createTime | moment('YYYY-MM-DD HH:mm:ss') }}</template>
			</el-table-column>
            <el-table-column  label="状态" width="170">
				<template slot-scope="scope">{{ state(scope.row.state)}}</template>
			</el-table-column>
			 <el-table-column  label="备注" width="120">
				<template slot-scope="scope">{{ state(scope.row.remark)}}</template>
            </el-table-column>
            
            <el-table-column  label="代理商" width="120" v-if="adminUser ==1">
				<template slot-scope="scope">{{scope.row.agentUserName}}</template>
            </el-table-column>
            <el-table-column  label="用户" width="120" v-if="adminUser ==1">
				<template slot-scope="scope">{{scope.row.userName}}</template>
			</el-table-column>
			
            <el-table-column label="操作" min-width="260">
				<template scope="scope">
				<!-- 
				<el-button size="small" type="primary"  v-if='scope.row.sysUserId=="" ||  scope.row.sysUserId ==null' @click="addAdmin(scope.$index,scope.row)">新增物业</el-button>
				<el-button size="small" type="warning"  v-if='scope.row.sysUserId!="" ||  scope.row.sysUserId !=null' @click="editAdmin(scope.$index,scope.row)">修改物业</el-button> -->
                <el-button size="small" v-if="adminUser!=1" type="primary"  @click="addFile(scope.$index,scope.row)">绑定素材</el-button>
                <el-button size="small" v-if="adminUser!=1" type="primary"  @click="addFile1(scope.$index,scope.row)">绑定广告</el-button>
                 <el-button size="small" v-if="adminUser!=1" type="primary"  @click="addFile2(scope.$index,scope.row)">绑定视频</el-button>
                <el-button size="small"  v-if="adminUser!=1" type="primary"  @click="update(scope.$index,scope.row)">编辑</el-button>
                <el-button size="small"  v-if="adminUser==1" type="danger"  @click="deleteRow(scope.$index, scope.row)">删除</el-button>
                <!-- <el-button size="small" type="warning" @click="updateRow(scope.$index, scope.row)">替换</el-button> -->
				</template>
			</el-table-column>

            <!-- <el-table-column prop="fileUrl" label="文件地址" width="200" sortable>
                <template slot-scope="scope"><img :src="scope.row.fileUrl"></template>
			</el-table-column> -->
            
		
		</el-table>

		<el-pagination
		 	class="pull-right clearfix"
			@current-change="handleCurrentChange"
			:current-page="currentPage"
			:page-size="totalsize" 
			layout="total, prev, pager, next, jumper"
			:total="total" 
		>
		</el-pagination>

        <el-dialog  custom-class="large" :title="formtitle" :visible.sync="dialogFormVisible" >
			<el-form ref="subData" :model="subData" label-width="100px" @submit.prevent="onSubmit" style="margin:20px;"  id="formid" action=""  method="post" enctype="multipart/form-data" target="frameName">
                    
                    <!-- <el-form-item label="设备类型">
                       <el-select  v-model="subData.equipmentType" placeholder="请选择设备类型" @change="selectEquType(subData.equipmentType)">
                           <el-option :label="item.label" :key="item.value" :value="item.value" v-for="item in options">{{item.label}}</el-option>
					    </el-select>
                    </el-form-item>  -->
                    <el-form-item label="上传logo">
                    <el-upload
                    class="upload-demo"
                    action="http://47.98.164.234:8080/cms/file/upload11"
                    :data="subData2"
                    :limit="1"
                    :on-preview="handlePreview"
                    :on-success="uploadok"
                    :on-remove="handleRemove"
                    :file-list="fileList"
                    list-type="picture">
                    <el-button size="small" type="primary">点击上传logo</el-button>
                    <div slot="tip" class="el-upload__tip">只能上传图片jpg/png格式,分辨率为290*170</div>
                    </el-upload>
                    </el-form-item>

                    <el-form-item label="设备编号">
                        <el-input name="equipmentNo"  disabled="disabled" v-model="subData.equipmentNo"  placeholder="请输入设备编号"></el-input>
                    </el-form-item>
                    <el-form-item label="设备名称">
                        <el-input name="equipmentName"  v-model="subData.equipmentName1"  placeholder="请输入设备名称"></el-input>
                    </el-form-item>
                    <!-- <el-form-item label="展示类型">
                     
                        <el-select  v-model="subData.type" placeholder="请选择展示类型">
                             <el-option
                                        v-for="item in options"
                                        :key="item.value"
                                        :label="item.label"
                                        :value="item.value"
                                        :disabled="item.disabled">
                                </el-option>
                        </el-select>
                     </el-form-item>    -->

                    <el-form-item label="状态">
                        <el-radio-group  name="state" v-model="subData.state">
                            <el-radio label="10">正常</el-radio>
                            <el-radio label="20">禁用</el-radio>
                        </el-radio-group>
                    </el-form-item>

                    <el-form-item label="类型">
                        <el-radio-group  name="type" v-model="subData.type">
                            <el-radio label="10">商品显示</el-radio>
                            <el-radio label="20">广告显示</el-radio>
                            <el-radio label="30">视频显示</el-radio>
                        </el-radio-group>
                    </el-form-item>

                     <el-form-item  v-if="subData.type=='10'" label="每页显示个数">
                        <el-input name="showCount"  v-model="subData.showCount"  placeholder="请输入每页显示个数"></el-input>
                    </el-form-item>


                      <el-form-item  v-if="subData.type=='20'" label="广告轮播时长(秒)">
                        <el-input name="showMin"  v-model="subData.showMin"  placeholder="请输入广告轮播时长"></el-input>
                    </el-form-item>
                    <el-form-item label="备注">
                        <el-input name="remark"  v-model="subData.remark"  placeholder="请输入备注"></el-input>
                    </el-form-item>


			</el-form>	
			<div slot="footer" class="dialog-footer">
				<el-button @click="dialogFormVisible = false">取 消</el-button>
				<el-button type="primary" @click="open()">确 定</el-button>
			</div>
        </el-dialog>



         <el-dialog size="large"   title="绑定素材" :visible.sync="dialogFormTypeVisible" >
             <!-- {{rows.type}} -->
			<el-form  >


                <el-col :span="24">    
                   <el-col :span="24" style="text-align:center;margin-bottom:10px" v-if="showImg==true">
                        <el-col :span="6">
                            
                                <el-select  v-model="classes" placeholder="请选择分类">    
                                    <el-option
                                        v-for="item in options1"
                                        :key="item.classes"
                                        :label="item.classes"
                                        :value="item.classes"
                                        >
                                                </el-option>
                                 </el-select>    
                                
                           
                        </el-col>
                        <el-col :span="6"><el-input v-model="name"  placeholder="请输入商品名称"></el-input></el-col>
                        <el-col :span="4"><el-button  type="primary" @click="classes1()">分类筛选</el-button></el-col>
                        <el-col :span="2"></el-col>
                        <el-col :span="4"><el-button  type="danger" @click="deleteAll()">一键下架</el-button></el-col>
                      
                     
                   </el-col>             
                   <el-col :span="24" style="text-align:center">
                        <el-col :span="4">素材</el-col>
                        <el-col :span="4">状态</el-col>
                        <el-col :span="2">排序</el-col>
                        <el-col :span="3" v-if="showImg==true">商品名称</el-col>
                        <el-col :span="3" v-if="showImg==true">商品价格</el-col>
                        <el-col :span="3" v-if="showImg==true">优惠价格</el-col>
                        <el-col :span="3" v-if="showImg==true">单位</el-col>
                        <el-col :span="2">操作</el-col>
                   </el-col>              


                  <el-col :span="24" :label="item.id" :key="item.id" v-for="item in allFileListLeft"  style="text-align:center;line-height:40px" >
                      <el-col :span="4">
                          <img v-if="item.align!='30'" :src="files+item.fileId" style="height:40%; width:50%;vertical-align:middle; margin-right:10px">
                           <video v-if="item.align=='30'"  :src="files+item.fileId" style="height:30%; width:30%"/>
                      </el-col>
                      <el-col :span="4">
                           <el-radio-group v-model="item.state">
                                <el-radio label="10">上架</el-radio>
                                <el-radio label="20">下架</el-radio>
                            </el-radio-group> 


                      </el-col>
                      <el-col :span="2" style="padding-right:5px"><el-input v-model="item.sort"  placeholder="请输入排序"></el-input></el-col>
                      <el-col :span="3" v-if="showImg==true" style="padding-right:5px"><el-input v-model="item.name"  placeholder="请输入商品名称"></el-input></el-col>
                      <el-col :span="3" v-if="showImg==true" style="padding-left:5px"><el-input v-model="item.price" placeholder="请输入价格"></el-input></el-col>
                      <el-col :span="3" v-if="showImg==true" style="padding-left:5px"><el-input v-model="item.price1" placeholder="请输入优惠价格"></el-input></el-col>
                       <el-col :span="3" v-if="showImg==true" style="padding-left:5px"><el-input v-model="item.unit" placeholder="请输入单位"></el-input></el-col>
                      <el-col :span="2"><el-button type="primary" :disabled="item.type=='20'"  @click="updateRow1(item)">保存修改</el-button></el-col>
                 
                  
                  
                  </el-col> 
                 
                </el-col>




			</el-form>	
			<div slot="footer" class="dialog-footer" style="height:30px">
				<!-- <el-button @click="dialogFormTypeVisible = false">取 消</el-button> -->
				<!-- <el-button type="primary" @click="open1()">确 定</el-button> -->
			</div>
        </el-dialog>



      

  </div>
</template>
<script>
	//2
	import { RequestPost } from '../../api/api';
    import { RequestGet } from '../../api/api';
    import { url } from '../../api/api';
    import { fileUrls } from '../../api/api';
	import {timeFormat} from '../../api/format';
	import {dateFormat} from '../../api/format';
    import {state} from '../../api/format';
    import {showType} from '../../api/format';
	import { PageSize } from '../../api/api';
	import moment from 'moment';
  	export default {
	  
    methods: {
	  dateFormat,	
	  timeFormat,
      state,
      showType,	
      


    handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePreview(file) {
        alert(file);
        console.log(file);
      },
      uploadok(res,file){  //上传成功后调用方法
        this.paths = this.paths+res.data;
        // alert(this.paths);
          
      },
      handleSizeChange(val) {
		console.log(`每页 ${val} 条`);
		

      },
      
      handleCurrentChange(val) {
		console.log(`当前页: ${val}`);
		this.pageNumber = val;

		
		this.page.pageNumber = val;
		/**
		 * 1.get 请求传参数，必须是json前面加一个params
		 * 2.post传参数不需要在json对象传值前面带params
		 */
		this.loadData();


      },
      deleteAll(){
           this.$confirm('确认一键下架, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {

                     RequestGet("/sysEquipment/deleteAll",{equipmentId:this.equipmentNo1}).then(response => {
               if(response.code=='0000'){
                    this.$message({
                        message: "一键下架已完成",
                        type: 'success'
                    });  


                     this.allFileListLeft= [];
                    this.allFileListLeft2 = [];
                    this.allFileListLeft1 = [];
                    this.allFileListRight= [];


                    this.checkList1 = [];
                    this.checkList2 = [];
                    this.classes='';


                   
                    RequestGet("/sysEquipment/sysEquipmentFile/findAllType",{id:this.equipmentId,classes:''}).then(response1 => {
                        if(response1.code == '0000'){
                            this.allFileListLeft=response1.data;
                        }
                                
                    }).catch(error => {
                                    this.$router.push({ path: '/login' });
                                    
                    })  
                   // this.dialogFormTypeVisible = true;
                   
                }else{
                    this.$message({
                        message: response.message,
                        type: 'error'
                    });
                }
                        
            }).catch(error => {
                            this.$router.push({ path: '/login' });
                            
            })  






             
             }).catch(() => {
            this.$message({
                type: 'info',
                message: '已取消一键下架'
            });          
            }); 

          
        
         
      },
       /**
        * 查询
        */
        query(){
            this.loadData();
        },
        

      add(){

           this.fileList = [];     
           this.dialogFormVisible = true;
		   this.formtitle ="新增设备";   
           this.subData = {}; 
           this.show = "1";
          
            this.isEdit = true;
            this.subData = {
                 type:"10",
                 state:"10"
                
            };
           this.subData.userName = sessionStorage.getItem("loginName");
           this.subData.userId = sessionStorage.getItem("userId"); 


      },

      updateRow1(rows){
          console.log(rows);
          RequestPost("/sysEquipment/sysEquipmentFile/update",rows).then(response => {
						
					
                if(response.code=='0000'){
                    this.$message({
                        message: response.message,
                        type: 'success'
                    });  

                    rows.type='20';
                   
                }else{
                    this.$message({
                        message: response.message,
                        type: 'error'
                    });
                }
              //  this.loadData();
            }).catch(error => {
            this.$router.push({ path: '/login' });
            })
          
      },

       deleteRow(index, rows) {
       this.$confirm('确认删除, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
           


            //删除功能暂时不开放
            RequestPost("/sysEquipment/delete",rows).then(response => {
						
						//this.logining = false; 
                if(response.code=='0000'){
                    this.$message({
                        message: response.message,
                        type: 'success'
                    });  
                    this.dialogFormVisible = false;
                    this.loadData();
                }else{
                    this.$message({
                        message: response.message,
                        type: 'error'
                    });
                }
                this.loadData();
            }).catch(error => {
            this.$router.push({ path: '/login' });
            })


            
            this.dialogFormVisible = false;
            
            }).catch(() => {
            this.$message({
                type: 'info',
                message: '已取消删除'
            });          
            });
      },


      update(index, rows) {
        this.formtitle = "编辑";
        this.dialogFormVisible = true;
        this.show = "2";

        this.subData = rows;
        this.paths = "";
        this.subData2 = {};
         this.fileList = [];     
        this.subData2.userName = sessionStorage.getItem("loginName");
        this.subData2.userId = sessionStorage.getItem("userId"); 
            
      },

      addFile(index, rows){
            this.allFileListLeft= [];
            this.allFileListLeft2 = [];
            this.allFileListLeft1 = [];
            this.allFileListRight= [];


            this.checkList1 = [];
            this.checkList2 = [];
            this.classes='';
            this.name='';


            this.showImg = true;



            this.equipmentId = rows.id;
            this.equipmentNo1 = rows.equipmentNo;
            RequestGet("/sysEquipment/sysEquipmentFile/findAllType",{id:rows.id,classes:'',name:this.name,type:"10"}).then(response => {
                if(response.code == '0000'){
                    this.allFileListLeft=response.data;
                }
                        
            }).catch(error => {
                            this.$router.push({ path: '/login' });
                            
            })  
             this.dialogFormTypeVisible = true;



      },
      addFile1(index, rows){
            this.allFileListLeft= [];
            this.allFileListLeft2 = [];
            this.allFileListLeft1 = [];
            this.allFileListRight= [];


            this.checkList1 = [];
            this.checkList2 = [];
            this.classes='';
            this.name='';

            this.showImg = false;


            this.equipmentId = rows.id;
            this.equipmentNo1 = rows.equipmentNo;
            RequestGet("/sysEquipment/sysEquipmentFile/findAllType",{id:rows.id,classes:'',name:this.name,type:"20"}).then(response => {
                if(response.code == '0000'){
                    this.allFileListLeft=response.data;
                }
                        
            }).catch(error => {
                            this.$router.push({ path: '/login' });
                            
            })  
             this.dialogFormTypeVisible = true;



      },
       addFile2(index, rows){
            this.allFileListLeft= [];
            this.allFileListLeft2 = [];
            this.allFileListLeft1 = [];
            this.allFileListRight= [];


            this.checkList1 = [];
            this.checkList2 = [];
            this.classes='';
            this.name='';

            this.showImg = false;


            this.equipmentId = rows.id;
            this.equipmentNo1 = rows.equipmentNo;
            RequestGet("/sysEquipment/sysEquipmentFile/findAllType",{id:rows.id,classes:'',name:this.name,type:"30"}).then(response => {
                if(response.code == '0000'){
                    this.allFileListLeft=response.data;
                }
                        
            }).catch(error => {
                            this.$router.push({ path: '/login' });
                            
            })  
             this.dialogFormTypeVisible = true;



      },
      classes1(){
          this.allFileListLeft = [];
           RequestGet("/sysEquipment/sysEquipmentFile/findAllType",{id: this.equipmentId,classes:this.classes,name:this.name}).then(response => {
                if(response.code == '0000'){
                    this.allFileListLeft=response.data;
                }
                        
            }).catch(error => {
                 this.$router.push({ path: '/login' });
                            
            })  

      },

      open1(){

            

           if(this.allFileListLeft.length>0 && this.checkList1.length ==0){
                this.$message({
                    message: "请绑定图片素材",
                    type: 'error'
                });  
                return;
           }

           if(this.allFileListRight.length>0 && this.checkList2.length!=1){
                this.$message({
                    message: "绑定视频素材只能选一个",
                    type: 'error'
                });  
                return;
           }

           if(this.allFileListLeft2.length>0 && this.checkList1.length ==0){
                this.$message({
                    message: "请绑定图片素材",
                    type: 'error'
                });  
                return;
           }

            if(this.allFileListLeft2.length>0 && this.checkList2.length ==0){
                this.$message({
                    message: "请绑定图片素材",
                    type: 'error'
                });  
                return;
           }
           

           var person = {
              imageList:this.checkList1,
              videoList:this.checkList2,
              userId:sessionStorage.getItem("userId"),
              equipmentId:  this.equipmentId,
              align:""
           } ;


           if(this.allFileListLeft2.length>0){
               
               person.align = "10";
           }

           RequestPost("/sysEquipment/equipmentFile/updateFile",person).then(response => {
						
				
                if(response.code=='0000'){
                    this.$message({
                        message: response.message,
                        type: 'success'
                    });  
                    this.dialogFormTypeVisible = false;
                
                }else{
                    this.$message({
                        message: response.message,
                        type: 'error'
                    });
                }
                this.loadData();
            }).catch(error => {
            this.$router.push({ path: '/login' });
            })


       
      },
     
      open(){
        
         
        // var uploadfile = document.getElementById("uploadfile"); 
        // this.subData.file = uploadfile.files[0];
        //alert(this.subData.file);
        // $("#formid").attr("action",$base_path+"/file/upload");
		// $("#formid").submit();

        this.validate();


        if(this.show =="1"){

            RequestPost("/sysEquipment/addFile",this.subData).then(response => {
                if(response.code == '0000'){
                        this.$message({
                            message: response.message,
                            type: 'success'
                        });  
                        this.dialogFormVisible = false;
                        this.loadData();
                        
                }else{
                    this.$message({
                        message: response.message,
                        type: 'error'
                    });

                }
                        
            }).catch(error => {
                            this.$router.push({ path: '/login' });
                            
            })  

        }else{
            if(this.paths==""){
               // this.paths
            }else{
                this.subData.imgUrl =  sessionStorage.getItem("loginName")+"/"+this.paths;     
            }
             
             //删除功能暂时不开放
            RequestPost("/sysEquipment/update",this.subData).then(response => {
                      
                //this.logining = false; 
                if(response.code=='0000'){
                    this.$message({
                        message: response.message,
                        type: 'success'
                    });  
                    this.dialogFormVisible = false;
                    this.loadData();
                }else{
                    this.$message({
                        message: response.message,
                        type: 'error'
                    });
                }
                this.loadData();
            }).catch(error => {
            this.$router.push({ path: '/login' });
            })
        }    
            

      },


     
    

	
	loadData(){
        if(sessionStorage.getItem("loginName") == 'admin' || sessionStorage.getItem("loginName") == 'adminA' ){
            this.page.userName = "";
            this.adminUser = 1;
        }else{
            this.page.userName = sessionStorage.getItem("loginName");
            this.adminUser = 0;
        }
        
       // this.page.state = '10';
        this.files = fileUrls;    
        this.page.equipmentNo = this.equipmentNo;
            this.page.type = this.type;
		RequestGet("/sysEquipment/findAll",this.page).then(response => {
						if(response.code == '0000'){
								 this.datalist = response.data;
								 this.total = response.page.totalCount; 
								 this.totalsize  = response.page.pageSize;
						 }
					
		}).catch(error => {
						 this.$router.push({ path: '/login' });
						
        })  
        

        RequestGet("/sysClasses/findAllSelect","").then(response => {
						if(response.code == '0000'){
								 this.options1  = response.data;
						 }
					
		}).catch(error => {
						 this.$router.push({ path: '/login' });
						
		}) 
        
						
	
	},

  
    
 
    validate(){
        if(this.subData.equipmentNo.trim()=="" || this.subData.equipmentNo == null){
            this.$message({
                type: 'error',
                message: '设备编号不能为空'
            });         
            return false;
        }
        
        if(this.subData.equipmentName1.trim()=="" || this.subData.equipmentName1 == null){
            this.$message({
                type: 'error',
                message: '设备名称不能为空'
            });         
            return false;
        }

        // if(this.subData.communityId.trim()=="" || this.subData.communityId == null){
        //     this.$message({
        //         type: 'error',
        //         message: '所属小区不能为空'
        //     });         
        //     return false;
        // }

        // if(this.subData.equNo.trim().length>6){
        //     this.$message({
        //         type: 'error',
        //         message: '请输入6位设备序列号'
        //     });         
        //     return false;
        // }

        


    },




	},
	//1
	created:function(){
		//3
		this.loadData();
		
	
	},
    data() {
      return {
        total:0,     //数据的总数量
        show:"1", //1新增
		totalsize:0,  //总的页数 = 总数量/每页显示的条数
        currentPage:1,
        options1:[],
        type:"",
		page:{
			pageSize:PageSize,   //一页显示的条数
            userName:''
		},
        datalist:[],
        formtitle:"",
        fileList: [],
        paths:"",
        files:"",
        equipmentNo:"",
		listLoading: false,
        dialogFormVisible:false,
        dialogFormTypeVisible:false,
        equipmentId:"", //当前设备编号
        equipmentNo1:"",
        showImg:true,
        name:"",
       
        adminUser:0,  //是否为admin      
        subData:{
            type:""
            
        },
        subData2:{},

         checkList1: [],  //图片
         checkList2: [],  //视频
         allFileList:[], //所有图片资源
         allFileListLeft:[], //左边图片资源
         allFileListRight:[], //右边视频资源
         allFileListLeft1:[],
         allFileListLeft2:[], //右边图片资源
         classes:"",   
        options: [{
          value: '10',
          label: '图片全屏'
        }, {
          value: '20',
          label: '视频全屏',
        }, {
          value: '30',
          label: '图片视频1:2'
        }, {
          value: '40',
          label: '视频图片2:1'
        }, {
          value: '50',
          label: '图片1:2'
        },{
          value: '60',
          label: '图片2:1'
        },{
          value: '70',
          label: '图片1:1'
        }],

      };
    }
  }
</script>
<style>
	.el-dialog--small {
		 width: 50%; 
	}

    .el-dialog--large {
		 width: 50%; 
	}


    
    .span {
        position:relative;
        padding:8px;
    }
    .tip {
        display:block;
        background:#f00;
        border-radius:50%;
        width:8px;
        height:8px;
        top:12px;
        right:0px;
        position:absolute;
    }
</style>
