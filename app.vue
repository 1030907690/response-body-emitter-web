<template>
  <div>
    <textarea style="width: 200px;height: 200px;border: 1px solid red;" v-model="text"></textarea>

    {{ tip }}
    <NuxtRouteAnnouncer />
    <NuxtWelcome />
  </div>
</template>
<script setup lang="ts">
import { onMounted,ref } from 'vue';
let text = ref<string>("")
let tip = ref<string>("")

//接收后台消息
const  receiveMessage = () =>{
  let eventSource  = new EventSource('http://127.0.0.1:8080/api/index/chat');
   
    eventSource.onopen = (event) =>{
      console.log("onopen ",event); 
    }
    //接收成功
    eventSource.onmessage = (event) => {
      console.log("onmessage ",event);
      
      text.value =  text.value + event.data;
    };

    
    
    eventSource.addEventListener('end', (event) => {
      console.log("服务器主动关闭连接");
      eventSource.close(); // 主动关闭连接
    });


    //接收失败
    eventSource.onerror = (error) => {
        console.error('SSE error:',eventSource.readyState, error);
        if (eventSource.readyState === EventSource.CLOSED) { 
          console.log("正常关闭"); // 应在此过滤已关闭状态
        } else {
          console.error("真实错误:");
        }
        // 如果不close，会自动重连
        eventSource.close()
    };

    
} 
onMounted(()=>{

  console.log("onMounted")
  receiveMessage()
  // $fetch("http://127.0.0.1:8080/api/index/chat",{method:'post',body:{
  //   query:"你是"
  // }}).then(res=>{
  //   console.log("res " ,res)
  //   text.value =  res
  // })

  

})

</script>