<template>
  <div class="number-container d-flex justify-content-center align-items-center">
    <!-- 减 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @click="sub">-</button>
    <!-- 购买的数量 -->
    <span class="number-box">{{ num }}</span>
    <!-- 加 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @click="add">+</button>
  </div>
</template>

<script>
import bus from '@/components/eventBus.js'
export default {
  props:{
    // 接受商品的 id 值 ，将来，使用 EventBus 方案
    //把数量传递到 App.vue 的时候，需要通知App组件，更新那个商品的数量
    id:{
      type:Number,
      required:true
    },
    // 接收到的num数量值
    num:{
      type:Number,
      default:1
    }
  },
  methods:{
    //点击按钮 ，数值+1
    add(){
      //要发送给App的数据格式为{id，value}
      //其中，id是商品的id;value 是商品最新的购买数量
      const obj={id:this.id,value:this.num+1}
      //要做的事情：通过EventBus 把obj对象，发送给App.vue组件
      // console.log(obj);
      bus.$emit('share',obj)
    },
    sub(){
      if(this.num-1===0) return
      //要发送给App的数据格式为{id，value}
      //其中，id是商品的id;value 是商品最新的购买数量
      const obj={id:this.id,value:this.num-1}
      //要做的事情：通过EventBus 把obj对象，发送给App.vue组件
      // console.log(obj);
      bus.$emit('share',obj)
    }
  }
}
</script>

<style lang="less" scoped>
.number-box {
  min-width: 30px;
  text-align: center;
  margin: 0 5px;
  font-size: 12px;
}

.btn-sm {
  width: 30px;
}
</style>
