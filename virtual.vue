<template>
  <div>
    <!-- <virtual-list 
    style="height: 360px; overflow-y: auto;" 
    :data-key="'uid'" :data-sources="items"
    :data-component="itemComponent" /> -->


  <!-- 数据传给了 VirtualList，但是用户需要拿到数据，所以需要作用域插槽 -->
  <!--  variable 高度不定-->
    <VirtualList :items="items" :remain="remain" :size="size" :variable="true">
      <myItem slot-scope="{item}" :item="item" />
    </VirtualList>
  </div>
</template>

<script>
import Item from './Item.vue'
import Mock from "mockjs"
// import VirtualList from 'vue-virtual-scroll-list'
import myItem from "./myItem.vue"

let items = []
for (let i = 0; i < 1000; i++) {
  items.push({ id: i, value: Mock.Random.sentence(5,50) })
}


export default {
  name: 'root',
  data() {
    return {
      remain: 8, // 可见多少个
      size: 40, // 每一个有多高


      items
    }
  },

  mounted() {
    return
    let arr = Array.from({ length: 1000 });
    let res = arr.map(a => {
      return {
        uid: 'unique_1', text: 'abc'
      }
    })

    this.items.push(...res)


  },
  components: { VirtualList: Item, myItem }
}
</script>
<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
</style>