<template>
  <div class="app-container">
    <Header></Header>

    <!-- 循环渲染每一个商品的信息 -->
    <Goods
     v-for="item in list" 
     :key="item.id" 
     :id="item.id"
     :title="item.goods_name" 
     :pic="item.goods_img" 
     :price="item.goods_price"
     :state="item.goods_state"
     :count="item.goods_count"
     @state-change="getNewState"
     >
    </Goods>

    <!-- Footer 区域 -->
    <Footer :isfull="fullState" :amount="amt" :all="total" @full-change="getFullState" ></Footer>
  </div>
</template>

<script>
//导入 axios 请求的库
import axios from 'axios'
//导入需要的组件
import Header from '@/components/Header/Header.vue'
import Goods from '@/components/Goods/Goods.vue'
import Footer from '@/components/Footer/Footer.vue'
import bus from "@/components/eventBus.js"
export default {
  components:{
    Header,
    Goods,
    Footer
  },
  created(){
    //调用请求数据的方法
    this.initCartList()

    bus.$on('share',(val)=>{
      this.list.some(item=>{
        if(item.id===val.id){
          item.goods_count=val.value
          return true
        }
      })
    })
  },
  methods:{
    //封装请求列表的数据方法
    async initCartList(){
      // 调用 axios 的get 方法 ，请求列表数据
      const{data:res}=await axios.get('https://www.escook.cn/api/cart')
      //只要请求回来的数据，在页面渲染期间要用到，则必须转存到data中
      if(res.status ===200){
        this.list=res.list
      }
    },
    //接受子组件传递过来的数据
    // e的格式为{id：value}
    getNewState(e){
      // console.log('父组件接受到数据了！');
     this.list.some(item=>{
      if(item.id === e.id){
        item.goods_state=e.value
        //终止后续的循环
        return true
      }
     })
    },
    //接受Footer子组件传递过来的全选按钮状态
    getFullState(val){
      // console.log('在App中拿到了全选的状态');
      // console.log(val)
      this.list.forEach(item=>(item.goods_state=val))
    }
  },
  data(){
    return{
      //用来存储购物车的列表数据，默认为空数组
      list:[]
    }
  },

  //计算属性
  computed:{
    //动态计算出全选的状态是true还是false
    fullState(){
      return this.list.every(item=>item.goods_state)
    },
    //已勾选的商品总价格
    amt(){
      //1. 先filter 过滤
      //2. 在reduce 累加
      return this.list
      .filter(item=>item.goods_state)
      .reduce((total,item) =>(total += item.goods_price * item.goods_count),0)
      },
      //已勾选商品的总数量
      total(){
        return this.list.filter(item=>item.goods_state).reduce((t,item)=>(t+=item.goods_count),0)
      }
  }
}
</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
