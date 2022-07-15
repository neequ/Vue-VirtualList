<template>
  <!--
    固定高度 
    <div style="height:40px;border:1px solid red">
      {{ item.value}}
  </div> -->

  <div style="border:1px solid red;padding:20px">
      {{ item.value}}
  </div>
</template>

<script>
  export default {
    name: 'myItem',
    props:{
      item:Object
    },
    mounted(){
      
    }
  }
</script>
