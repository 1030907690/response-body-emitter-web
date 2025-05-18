<template>
  <div>
    <textarea style="width: 200px;height: 200px;border: 1px solid red;" v-model="text"></textarea>
    <NuxtRouteAnnouncer />
    <NuxtWelcome />
  </div>
</template>
<script setup lang="ts">
import { onMounted,ref } from 'vue';
let text = ref<string>("")
let eventSource = ref<EventSource>();

//接收后台消息
const  receiveMessage = () =>{
    eventSource.value = new EventSource('http://127.0.0.1:8080/api/index/chat');
    //接收成功
    eventSource.value.onmessage = (event) => {
      text.value =  text.value + event.data;
    };
    //接收失败
    eventSource.value.onerror = (error) => {
        console.error('SSE error:', error);
    };
} 
onMounted(()=>{

  console.log("onMounted")
  receiveMessage()

  // $fetch("http://127.0.0.1:8080/api/index/chat",{method:'post',body:{
  //   prompt:"你是"
  // }}).then(res=>{
  //   console.log("res " ,res)
  //   text.value =  res
  // })

  

})

</script>