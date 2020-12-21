<template> 
 <div class="block">   
	 	<el-col :span="16" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				<el-form-item style="width:300px">
					<el-input v-model="remark"  @keyup.enter.native="query"  placeholder="请输入备注" style="width:300px"></el-input>
				</el-form-item>
                <el-form-item style="width:200px">

                    <el-select  v-model="classes" placeholder="请选择分类">    
                     <el-option
                        v-for="item in options1"
                        :key="item.classes"
                        :label="item.classes"
                        :value="item.classes"
                        >
                                </el-option>
                     </el-select>    
					
                            <!-- <el-option value="" label="">全部</el-option>
                            <el-option value="水果" label="水果">水果</el-option>
                            <el-option value="蔬菜" label="蔬菜">蔬菜</el-option>
                            <el-option value="肉食" label="肉食">肉食</el-option>
                            <el-option value="熟食" label="熟食">熟食</el-option>
                            <el-option value="海鲜" label="海鲜">海鲜</el-option>
                            <el-option value="河鲜" label="河鲜">河鲜</el-option> -->
                             
                   
				</el-form-item>
                <el-form-item style="width:200px">
					<el-select  v-model="userId" placeholder="请选择显示素材类型">
                            <el-option value="" label="">全部</el-option>
                            <el-option value="10" label="只显示自己"></el-option>
                             
                    </el-select>
				</el-form-item>
                <!-- <el-form-item style="width:200px">
					<el-select  v-model="type" placeholder="请选择素材类型">
                            <el-option value="" label="">全部</el-option>
                            <el-option value="10" label="图片">图片</el-option>
                            <el-option value="20" label="视频">视频</el-option>
                             
                    </el-select>
				</el-form-item>

                <el-form-item style="width:200px">
					<el-select  v-model="standards" placeholder="请选择素材规格">
                            <el-option value="" label="">全部</el-option>
                            <el-option value="10" label="427*400">427*400</el-option>
                            <el-option value="20" label="640*400">640*400</el-option>
                            <el-option value="30" label="853*400">853*400</el-option>
                            <el-option value="40" label="1280*400">1280*400</el-option>
                             
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
        <el-col :span="8" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				
				<el-form-item  style="text-align:right">
					<el-button type="primary" @click="add()">上传素材</el-button>
				</el-form-item>

                <el-form-item  style="text-align:right">
					<el-button type="primary" @click="add1()">上传广告</el-button>
				</el-form-item>

                 <el-form-item  style="text-align:right">
					<el-button type="primary" @click="add2()">上传视频</el-button>
				</el-form-item>

                <!-- <el-form-item  style="text-align:right">
					<el-button type="primary" @click="updateDevice()">绑定设备</el-button>
				</el-form-item> -->
			</el-form>
		</el-col>
      
        
		<el-table :data="datalist" highlight-current-row v-loading="listLoading" style="width: 100%;">
		
			<!-- <el-table-column prop="type" label="素材类型" width="120" sortable>
                <template slot-scope="scope">{{scope.row.type =='10'?'图片':'视频' }}</template>
			</el-table-column> -->
			 <el-table-column prop="remark" label="名称" width="170">
				
			</el-table-column>
			
            <el-table-column prop="equipmentName" label="素材规格" width="200" sortable>
                <template>600*600</template>
			</el-table-column>
            <el-table-column prop="fileUrl" label="文件地址" width="200" sortable>
                <template slot-scope="scope">

                    <div v-if="scope.row.standards=='10' || scope.row.standards=='20'"><img :src="files+scope.row.fileUrl" style="height:30%; width:30%"></div>
                    <div v-if="scope.row.standards=='30'"><video  :src="files+scope.row.fileUrl" style="height:30%; width:30%"/></div>
                    
                    <!-- <video  :src="files+scope.row.fileUrl" style="height:30%; width:30%"/> -->
                
                </template>
			</el-table-column>
            <el-table-column prop="userName" label="用户名称" width="160" sortable>
			</el-table-column>
             <el-table-column prop="classes" label="分类" width="160" sortable>
			</el-table-column>
            
            <el-table-column  label="创建时间" width="170">
				<template slot-scope="scope">{{ scope.row.createTime | moment('YYYY-MM-DD HH:mm:ss') }}</template>
			</el-table-column>
             <el-table-column  label="类型" width="120">
				<template slot-scope="scope">{{scope.row.standards=="10"?"产品图":"广告图"}}</template>
			</el-table-column>
            <el-table-column  label="状态" width="140">
				<template slot-scope="scope">{{ state(scope.row.state)}}</template>
			</el-table-column>
			 <!-- <el-table-column prop="remark" label="备注" width="200">
				
			</el-table-column>
			 -->
            <el-table-column label="操作" min-width="120">
				<template scope="scope">
				<!-- <el-button size="small" type="primary"  @click="edit(scope.$index,scope.row)">编辑</el-button>
				<el-button size="small" type="primary"  v-if='scope.row.sysUserId=="" ||  scope.row.sysUserId ==null' @click="addAdmin(scope.$index,scope.row)">新增物业</el-button>
				<el-button size="small" type="warning"  v-if='scope.row.sysUserId!="" ||  scope.row.sysUserId !=null' @click="editAdmin(scope.$index,scope.row)">修改物业</el-button> -->
                <el-button size="small" type="danger" v-if='scope.row.userName==userName'   @click="deleteRow(scope.$index, scope.row)">删除</el-button>
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

        <el-dialog   :title="formtitle" :visible.sync="dialogFormVisible" >
			<el-form ref="subData" :model="subData" label-width="100px" @submit.native.prevent  style="margin:20px;"  id="formid" action=""  method="post" enctype="multipart/form-data" target="frameName">
                    
                    <!-- <el-form-item label="设备类型">
                       <el-select  v-model="subData.equipmentType" placeholder="请选择设备类型" @change="selectEquType(subData.equipmentType)">
                           <el-option :label="item.label" :key="item.value" :value="item.value" v-for="item in options">{{item.label}}</el-option>
					    </el-select>
                    </el-form-item>  -->

                    <el-form-item label="文件类型">
					<el-radio-group v-model="subData.type">
						<el-radio label="10" >{{subData.standards=='30'?"视频":"图片"}}</el-radio>
						
					</el-radio-group>
                     </el-form-item>
                    <!-- <el-form-item label="规格">
					<el-radio-group v-model="subData.standards">
						<el-radio label="10">427*400</el-radio>
						<el-radio label="20">640*400</el-radio>
                        <el-radio label="30">853*400</el-radio>
                        <el-radio label="40">1280*400</el-radio>
					</el-radio-group>
                     </el-form-item>    -->

                    <!-- <el-form-item label="*设备编号">
                        <el-input v-model="subData.equNo" type="number"  placeholder="请输入6位设备编号"></el-input>
                    </el-form-item> -->
                   <el-form-item label="*上传素材"> 
                   <el-upload
                    class="upload-demo"
                    action="http://47.98.164.234:8080/cms/file/upload11"
                    :data="subData"
                    :multiple="false"
                    :limit="1"
                    :on-preview="handlePreview"
                    :on-success="uploadok"
                    :on-remove="handleRemove"
                    :file-list="fileList"
                    list-type="picture">
                    <el-button size="small" :disabled="disabledBtn" type="primary">点击上传</el-button>
                    <div slot="tip" class="el-upload__tip">只能上传图片jpg/png格式,分辨率为600*600</div>
                    </el-upload>        
                    </el-form-item>
                    
                 
                    <el-form-item label="状态">
                        <el-radio-group  name="state" v-model="subData.state">
                            <el-radio label="10">正常</el-radio>
                            <el-radio label="20">禁用</el-radio>
                        </el-radio-group>
                    </el-form-item>

                     <el-form-item label="分类">
                       

                        <el-select  v-model="subData.classes" placeholder="请选择分类">    
                                <el-option
                                    v-for="item in options1"
                                    :key="item.classes"
                                    :label="item.classes"
                                    :value="item.classes"
                                    >
                                </el-option>
                         </el-select>
                    </el-form-item>
        
                    <el-form-item label="名称">
                        <el-input name="remark"  @keyup.enter.native="open()"  v-model="subData.remark"  placeholder="请输入名称"></el-input>
                    </el-form-item>


			</el-form>	
			<div slot="footer" class="dialog-footer">
				<el-button @click="dialogFormVisible = false">取 消</el-button>
				<el-button type="primary"  @click="open()">确 定</el-button>
			</div>
        </el-dialog>


        <el-dialog   :title="formtitle" :visible.sync="dialogFormVisible2" >
			<el-form ref="subData" :model="subData" label-width="100px" @submit.prevent="onSubmit" style="margin:20px;"  id="formid" action=""  method="post" enctype="multipart/form-data" target="frameName">
                    
                    <!-- <el-form-item label="设备类型">
                       <el-select  v-model="subData.equipmentType" placeholder="请选择设备类型" @change="selectEquType(subData.equipmentType)">
                           <el-option :label="item.label" :key="item.value" :value="item.value" v-for="item in options">{{item.label}}</el-option>
					    </el-select>
                    </el-form-item>  -->

                    <el-form-item label="文件类型">
					<el-radio-group v-model="subData.type">
						<el-radio label="10">图片</el-radio>
						<!-- <el-radio label="20">视频</el-radio> -->
					</el-radio-group>
                     </el-form-item>
                    <!-- <el-form-item label="规格">
					<el-radio-group v-model="subData.standards">
						<el-radio label="10">427*400</el-radio>
						<el-radio label="20">640*400</el-radio>
                        <el-radio label="30">853*400</el-radio>
                        <el-radio label="40">1280*400</el-radio>
					</el-radio-group>
                     </el-form-item>    -->

                    <!-- <el-form-item label="*设备编号">
                        <el-input v-model="subData.equNo" type="number"  placeholder="请输入6位设备编号"></el-input>
                    </el-form-item> -->
                   <el-form-item label="*上传素材"> 
                   <el-upload
                    class="upload-demo"
                    action="http://47.98.164.234:8080/cms/file/upload11"

                    multiple="false"
                    
                    :data="subData"
                    limit="1"
                    :on-preview="handlePreview"
                    :on-success="uploadok"
                    :on-remove="handleRemove"
                    :file-list="fileList"
                    list-type="picture">
                    <el-button size="small" type="primary">点击上传</el-button>
                    <div slot="tip" class="el-upload__tip">只能上传图片jpg/png格式,分辨率为600*600</div>
                    </el-upload>        
                    </el-form-item>
                    
                 
                    <el-form-item label="状态">
                        <el-radio-group  name="state" v-model="subData.state">
                            <el-radio label="10">正常</el-radio>
                            <el-radio label="20">禁用</el-radio>
                        </el-radio-group>
                    </el-form-item>
        
                    <el-form-item label="备注">
                        <el-input name="remark"  v-model="subData.remark"  placeholder="请输入备注"></el-input>
                    </el-form-item>


			</el-form>	
			<div slot="footer" class="dialog-footer">
				<el-button @click="dialogFormVisible2 = false">取 消</el-button>
				<el-button type="primary" @click="open()">确 定</el-button>
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
	import { PageSize } from '../../api/api';
	import moment from 'moment';
  	export default {
	  
    methods: {
	  dateFormat,	
	  timeFormat,
      state,	
      


    handleRemove(file, fileList) {
       // alert(file+":::"+fileList);
       this.fileList = [];
       this.disabledBtn=false;
        console.log(file, fileList);
      },
      handlePreview(file) {
        alert(file);
        console.log(file);
      },
      uploadok(res,file){  //上传成功后调用方法
        this.paths = this.paths+res.data+",";
        this.disabledBtn=true;
         
          
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
       /**
        * 查询
        */
        query(){
            this.loadData();
        },

        addByEnterKey(e){
            alert(e);
            if (e.keyCode == 13) {
             this.open();
            }
                



        },
        

      add(){

           this.fileList = [];     
           this.disabledBtn  = false;
           this.dialogFormVisible = true;
		   this.formtitle ="新增素材";   
           this.subData = {}; 
          
            this.isEdit = true;
            this.subData = {
                 type:"10",
                 standards:"10",
                 state:"10",
                 classes:""
                
            };

            this.paths = "";
            this.subData.userName = sessionStorage.getItem("loginName");
           this.subData.userId = sessionStorage.getItem("userId"); 


      },

      
      add1(){

           this.fileList = [];     
           this.disabledBtn  = false;
           this.dialogFormVisible = true;
		   this.formtitle ="新增广告";   
           this.subData = {}; 
          
            this.isEdit = true;
            this.subData = {
                 type:"10",
                 standards:"20",
                 state:"10",
                 classes:""
                
            };

            this.paths = "";
            this.subData.userName = sessionStorage.getItem("loginName");
           this.subData.userId = sessionStorage.getItem("userId"); 


      },

       add2(){

           this.fileList = [];     
           this.disabledBtn  = false;
           this.dialogFormVisible = true;
		   this.formtitle ="新增视频";   
           this.subData = {}; 
          
            this.isEdit = true;
            this.subData = {
                 type:"10",
                 standards:"30",
                 state:"10",
                 classes:""
                
            };

            this.paths = "";
            this.subData.userName = sessionStorage.getItem("loginName");
           this.subData.userId = sessionStorage.getItem("userId"); 


      },

       deleteRow(index, rows) {
       this.$confirm('确认删除, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
           

           rows.userName = this.userName;

            rows.state = '20';        
            //删除功能暂时不开放
            RequestPost("/file/deleteAll",rows).then(response => {
						
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
      updateDevice(){
            this.fileList = [];     
           this.dialogFormVisible2 = true;
		   this.formtitle ="绑定设备";   
           this.subData = {}; 
          
            this.isEdit = true;
            this.subData = {
                 type:"10",
                 standards:"10",
                 state:"10"
                
            };

            this.paths = "";
            this.subData.userName = sessionStorage.getItem("loginName");
           this.subData.userId = sessionStorage.getItem("userId"); 
      },
     
      open(){
        this.subData.fileUrl =  this.paths.substr(0,this.paths.length-1);


        if(this.subData.classes.trim()=="" || this.subData.classes == null){
            this.$message({
                type: 'error',
                message: '分类不能为空'
            });         
            return false;
        }
         
        // var uploadfile = document.getElementById("uploadfile"); 
        // this.subData.file = uploadfile.files[0];
        // alert(this.subData.file);
        // $("#formid").attr("action",$base_path+"/file/upload");
        // $("#formid").submit();
        // this.subData.communityId = sessionStorage.getItem("communityId");
        // alert(sessionStorage.getItem("communityId"));

        this.subData.userName = this.userName;
        RequestPost("/file/addFile",this.subData).then(response => {
            if(response.code == '0000'){
                	this.$message({
                        message: response.message,
                        type: 'success'
                    });  
                    this.dialogFormVisible = false;
                    this.loadData();   
            }else{
                this.$message({
                type: 'error',
                message:  response.message,
            }); 
            }
					
		}).catch(error => {
						 this.$router.push({ path: '/login' });
						
		})  

      },


     
    

	
	loadData(){

        this.page.userName = sessionStorage.getItem("loginName");
        this.page.state = '10';
        this.files = fileUrls;    


        this.page.remark = this.remark;
        this.page.type = this.type;
        this.page.standards = this.standards;
        this.page.classes = this.classes;
        this.page.fileUrl=this.userId;

        this.userName = sessionStorage.getItem("loginName");
        

		RequestGet("/file/findAll",this.page).then(response => {
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
        // if(this.subData.equNo.trim()=="" || this.subData.equNo == null){
        //     this.$message({
        //         type: 'error',
        //         message: '设备编号不能为空'
        //     });         
        //     return false;
        // }
        
        // if(this.subData.equipmentName.trim()=="" || this.subData.equipmentName == null){
        //     this.$message({
        //         type: 'error',
        //         message: '设备名称不能为空'
        //     });         
        //     return false;
        // }

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
        // var _self = this;
        // document.onkeydown = function(e){
        //     if(window.event == undefined){
        //         var key = e.keyCode;
        //     }else{
        //         var key = window.event.keyCode;
        //     }
        //     if(key == 13){
               
        //        alert(key);
        //        //_self.loginEnter('loginData');
        //     }
        // }
            
		
	
	},
    data() {
      return {
		total:0,     //数据的总数量
		totalsize:0,  //总的页数 = 总数量/每页显示的条数
        currentPage:1,
        options1:[],
        remark:"",
        classes:"",
        type:"",
        userId:"",
        standards:"",
        userName:"",
		page:{
			pageSize:PageSize,   //一页显示的条数
            userName:''
		},
        datalist:[],
        formtitle:"",
        fileList: [],
        paths:"",
        files:"",
        
		
		listLoading: false,
        dialogFormVisible:false,
        dialogFormVisible2:false,
        disabledBtn:false,
      
        subData:{
            type:"",
            standards:""
        },

      };
    }
  }
</script>
<style>
	.el-dialog--small {
		 width: 30%; 
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
