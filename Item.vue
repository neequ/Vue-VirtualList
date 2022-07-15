<template>
  <div class="viewport" ref="viewport" @scroll="handleScroll">
    <!-- 滚动条 -->
    <div class="scroll-bar" ref="scrollBar"></div>
    <!-- 真实位置 -->
    <div class="scroll-list" :style="{top:this.offset+'px'}"> 
      <div v-for="(item,index) in visiableData" :index="item.id" :vid="item.id" ref="items">
         <slot :item="item"/>
      </div>
    </div>
  </div>
</template>

<script>

  export default {
    name: 'item-component',
    props: {
      items: { // index of current item
        type: Array
      },
      size:{
        type:Number
      },
      remain:{
        type:Number
      },
      variable:{
        type:Boolean,
        default:false
      }
    },
    data(){
      return {
        start:0,
        end:this.remain,
        offset:0
      }
    },

    computed:{
      visiableData(){
        // 增大预留面积
        let start = this.start - this.prevCount
        let end = this.end + this.nextCount

        // return this.items.slice(this.start,this.end)
        return this.items.slice(start,end)
      },
      prevCount(){
        return Math.min( this.start,this.remain )
      },
      nextCount(){
        return Math.min( this.end, this.items.length - this.end )
      }
    },
    updated(){
        this.$nextTick(() => {
          let nodes = this.$refs.items
          nodes.forEach(node =>{
            let {height} = node.getBoundingClientRect()
            let id = node.getAttribute("vid") - 0;
            let oldHeight = this.positions[id].height;
            let val = oldHeight - height
            if(val){
              this.positions[id].height = height
              this.positions[id].bottom = this.positions[id].bottom - val // 顶部增加了
              // 后面所有人都需要增加高度
              for( let i = id+1; i < this.positions.length; i++ ){
                this.positions[i].top = this.positions[i-1].bottom
                this.positions[i].bottom = this.positions[i].bottom  - val
              }
            }
          })
          this.$refs.scrollBar.style.height = this.positions[this.positions.length-1].bottom + 'px'
        })
    },
  methods:{
    getIndex(value){
      let start = 0,end = this.positions.length-1, temp = null;
      while( start < end ){
        let middleIndex = parseInt( (start + end) / 2 )
        let middleValue = this.positions[ middleIndex ].bottom
        if(middleValue  == value ){
          return middleIndex
        }else if( middleValue < value ){
          start = middleIndex + 1
        }else { // 寻找的值 在 右边
          // TODO 不是很明白
          if( temp == null || temp > middleIndex ){
            temp = middleIndex  // 找到范围
          }
          end = middleIndex - 1
        }
      }
      return temp 
    },

    handleScroll(){
      // 滚动了多高，需要从哪个地方开始
      let scrollTop = this.$refs.viewport.scrollTop;
      if(this.variable){
        this.start = this.getIndex(scrollTop)
          this.end = this.start + this.remain;
        this.offset = this.positions[this.start - this.prevCount] ?   this.positions[this.start - this.prevCount].top : 0 ;
      }else {
        this.start = Math.floor( scrollTop / this.size )
        this.end = this.start + this.remain;
        // 需要把预留出来的偏移量 减去
        // 因为滚动的时候 start 提前了，会有一段时间重复数据
        this.offset = this.start * this.size - this.prevCount * this.size;
      }
    },
    cacheList(){
      this.positions = this.items.map((item,index)=>({
        height:this.size,
        top:index * this.size,
        bottom:(index+1) * this.size
      }))
    }
  },
    mounted(){
     this.$refs.viewport.style.height =  this.remain * this.size + 'px' 
      // 设置滚动条的高度
     this.$refs.scrollBar.style.height =this.items.length * this.size + 'px'
     this.cacheList()
    }
  }
</script>
<style lang="scss">
.viewport {
  overflow-y: scroll;
  position: relative;
}
.scroll-list {
  position: absolute;
  top: 0;
  width: 100%;
}
</style>