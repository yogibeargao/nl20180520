<template>
  <r-page>
      <top title="签到查看" :showBack="true"/>
      <r-body>
                    <search :condition="condition" :callBack="search" :showClass='!isShowClass'/>
                     <r-card>
                        <r-selector  title="状态" :options="options" :model="this" value="status" :onChange="search"></r-selector>
                    </r-card>
                    <r-card>
                                <r-table :data="data" />
                    </r-card>  
      </r-body>  
  </r-page>
</template>

<script>
import Util from "../util/util";
import  Search from '../components/Search.vue';

export default {
  components: {
    Search
  },
  data() {
    return {
       condition:{},
       status:null,
       options: [{ key: 0, value: "未签到" }, { key: 1, value: "已签到" }],
        data:{
        "head":[
          [{'text':'姓名'},{'text':'位置'},{'text':'时间'}]
        ],
        "body":[]
      }
    };
  },
  computed :{
      disableButton(){
        return _.isEmpty(this.student);
      },
      isShowClass(){
        return Util.isCompany(this);
      }
    
  },
  methods:{
    async updateStatus(){
      //const status = await this.$http.post(`location/sharing/response`,param);
     // if(status){
       // this.toastText="响应成功";
       // this.showFlag=true;
        this.$router.back()

     // }else{
       // this.toastText="响应失败";
       // this.type = "warn";
        //this.showFlag=true;
      //}
   },
   async search(condition){
                  if(condition==0||condition==1){
                      this.condition.status = condition;
                  }
                  const identityId = Util.getIdentityId(this);
                  const param = {"status":this.condition.status,"identityId":identityId,"classId":this.condition.class,"studentNos":this.condition.student_Nos,"startDateStr":this.condition.startDateStr,"endDateStr":this.condition.endDateStr,"pageNo":1,"pageSize":50} 
                  const status = await this.$http.post(`online/signin/list`,param);
                  const status_data = [];
                  _.each(status.body,(student,index)=>{
                      status_data.push([{'text':student.studentName},{'text':student.signAddress?student.signAddress:'未签到',"link":"updateStatus"},{'text':student.signDate?student.signDate.substring(11,16):''}])
                  })
                  this.data.body = status_data;
          }
  }
};
</script>


