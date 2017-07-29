<template>
  <div class="pos">
    <!--element-ui提供的24栏布局-->
    <el-row>
      <el-col :span='7' class='pos-order' id='order-list'>
         <el-tabs>
           <el-tab-pane label='点餐'>
             <!--table中默认绑定数据:data  对用prop,饿了么框架提供-->
              <el-table :data='tableData' border style="width:100%">
                <el-table-column prop="goodsName" label="商品名称">

                </el-table-column>
                                <el-table-column prop="count" label="量" width="60">

                </el-table-column>
                                <el-table-column prop="price" label="金额" width="70">

                </el-table-column>
                                <el-table-column  label="操作" width="100" fixed="right">
                    <template scope="scope">
                        <!--饿了么框架此时的模板为scope,故添加时要给函数传入goods时要用到scope.row-->
                        <el-button type="text" size="small"  @click="delSingleGoods(scope.row)">删除</el-button>
                        <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                    </template>
                </el-table-column>
              </el-table>
              <div class='totalDiv'>
                  <small>数量</small>{{totalCount}}  &nbsp;&nbsp;&nbsp;&nbsp;     <small>金额：</small>{{totalMoney}}元
                  </div>
              <div class="divbtn">
                <el-button type="danger" @click="delAllGoods()">删除</el-button>
                <el-button type="success">结账</el-button>
              </div>
           </el-tab-pane>
           <el-tab-pane label='挂单'>
           挂单
           </el-tab-pane>
           <el-tab-pane label='外卖'>
             外卖
           </el-tab-pane>
         </el-tabs>
      </el-col>
      <!--常用商品栏-->
      <el-col :span='17'>
           <div class="often-goods">
              <div class="title">常用商品</div>
              <div class="often-goods-list">
                <ul>
                   <li v-for="oftenGood in oftenGoods"  @click="addOrderList(oftenGood)">
                     <span>{{oftenGood.goodsName}}</span>
                     <span class="o-price">${{oftenGood.price}}元</span>
                   </li>
                </ul>
              </div>
           </div>
<!--食品类型栏-->
           <div class="goods-type">
             <el-tabs>
               <el-tab-pane label="汉堡">
                   <div>
                        <ul class='cookList'>
    <!--绑定到各个循环里，循环里才有goods-->
                            <li v-for="goods in type0Goods"  @click="addOrderList(goods)">
                                   <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                   <span class="foodName">{{goods.goodsName}}</span>
                                   <span class="foodPrice">￥{{goods.price}}元</span>
                             </li>
                        </ul> 
                   </div>
               </el-tab-pane>
               <el-tab-pane label="小食">
                                      <div>
                        <ul class='cookList'>
                            <li v-for="goods in type1Goods"   @click="addOrderList(goods)">
                                 <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                 <span class="foodName">{{goods.goodsName}}</span>
                                 <span class="foodPrice">￥{{goods.price}}元</span>
                            </li>
                        </ul>
                   </div>
               </el-tab-pane>
               <el-tab-pane label="饮料">
                                      <div>
                                        <ul class='cookList'>
                                            <li v-for="goods in type2Goods"   @click="addOrderList(goods)">
                                             <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                             <span class="foodName">{{goods.goodsName}}</span>
                                             <span class="foodPrice">￥{{goods.price}}元</span>
                                            </li>
                                        </ul>
                   </div>
               </el-tab-pane>
               <el-tab-pane label="套餐">
                                      <div>
                                            <ul class='cookList'>
                                            <li v-for="goods in type3Goods"   @click="addOrderList(goods)">
                                             <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                             <span class="foodName">{{goods.goodsName}}</span>
                                             <span class="foodPrice">￥{{goods.price}}元</span>
                                        </li>
                                    </ul>
                   </div>
               </el-tab-pane>
             </el-tabs>
           </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'pos',
  data(){
       return {
         tableData:[],
        oftenGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],
        totalMoney:0,
        totalCount:0
       }
  },
  //实例创建完立马调用请求
  created:function(){
     axios.get('http://jspang.com/DemoApi/oftenGoods.php')
     .then(response=>{
        console.log(response);
        this.oftenGoods=response.data;
     })
     .catch(error=>{
         console.log(error);
         alert('网络错误，不能访问')
     })
       //读取分类商品列表
      axios.get('http://jspang.com/DemoApi/typeGoods.php')
      .then(response=>{
         console.log(response);
         //this.oftenGoods=response.data;
         this.type0Goods=response.data[0];
         this.type1Goods=response.data[1];
         this.type2Goods=response.data[2];
         this.type3Goods=response.data[3];
 
      })
      .catch(error=>{
          console.log(error);
          alert('网络错误，不能访问');
      })
  },
  //钩子函数 虚拟DOM加载完成后触发
  mounted:function(){
    var orderHeight=document.body.clientHeight;
    document.getElementById('order-list').style.height=orderHeight+'px';
  },
  methods:{
      addOrderList(goods){
          this.totalMoney=0;
          this.totalCount=0;
          //判断商品是否存在于商品列表中
           let isHave=false;
           for(let i=0;i<this.tableData.length;i++){
               if(this.tableData[i].goodsId==goods.goodsId){
                   isHave=true;
               }
           }
        //   根据判断值编写业务逻辑
        if(isHave){
            //改变列表中商品的数量
            let arr=this.tableData.filter(o=>o.goodsId==goods.goodsId);
            arr[0].count++;
        }else{
            let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
            this.tableData.push(newGoods);
        }
        this.getAllMoney();
        //方法封装汇总
        // this.tableData.forEach(function(element) {
        //     this.totalCount+=element.count;
        //     this.totalMoney=this.totalMoney+(element.price*element.count);
        // }, this);
      },
      //删除单个商品，要重新汇总金额
      delSingleGoods(goods){
         //所加的商品在tableData里面
         this.tableData=this.tableData.filter(o=>o.goodsId!=goods.goodsId);
         this.getAllMoney();
      },
      //删除全部商品
      delAllGoods(){
           this.totalCount=0;
           this.totalMoney=0;
           this.tableData=[];
      },
      //汇总数量和金额
      getAllMoney(){
          this.totalCount=0;
          this.totalMoney=0;
          //计算金额和数量
                 this.tableData.forEach(function(element) {
            this.totalCount+=element.count;
            this.totalMoney=this.totalMoney+(element.price*element.count);
        }, this); 
      },
      //结账功能思路：设置我们Aixos的Pos方法。接受返回值进行处理。如果成功，清空现有构造器里的tableData，totalMoney，totalCount数据。
      //进行用户的友好提示。
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-order{
  background-color: #f9fafc;
  border-right:1px solid #c0ccda;
}
.divbtn{
  margin-top:10px;
}
.title{
   height:20px;
   border-bottom: 1px solid #d3dce6;
   background-color: #f9fafc;
   padding:10px;
   text-align:left;
}
.often-goods-list ul li{
      list-style: none;
      float:left;
      border:1px solid #f9fafc;
      padding:10px;
      margin:10px;
      background-color: #fff;
      cursor:pointer;
}
.o-price{
  color:#58b7ff;
}
.goods-type{
   clear:both;

}
.cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       cursor:pointer;
   }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .totalDiv{
       background-color: #fff;
       padding:10px;
       border-bottom: 1px solid #d3dce6;
   }
</style>
