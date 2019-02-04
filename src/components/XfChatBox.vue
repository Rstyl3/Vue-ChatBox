<template>
  <div class="chat-box">
    <div class="text-area" >
      <xf-chat-thread :chatThread="chat" :users="users" v-for="chat in chatThread" :key="chat.id" />
    </div>
    <xf-chat-input :chatInput.sync="chatInput" :users="users" @btnClick="hdlReply" @itemSelected="hdlSelected"/>
  </div>
</template>

<script>
import XfChatInput from './XfChatInput.vue'
import XfChatThread from './XfChatThread.vue'
import users from '@/assets/users.js'

export default {
  name: 'xf-chat-box',
  components: {
    XfChatInput,
    XfChatThread,
  },
  data() {
    return {
      chatThread: [
        {
          text: 'Hi There!',
          author: 'Bob',
          date: '1/1 10:00',
          replyThread:[],
          reply: false,
        },
      ],
      chatInput: '',
      users: users,
    }
  },
  computed:{
  },
  methods: {
    hdlReply(){
      let date = new Date();
      let formatDate = (date.getMonth()+ 1) + '/' + date.getDay() + ' ' +  date.getHours() + ":" + date.getMinutes() 
      if (this.chatInput === '') {
        console.log('write a message', this.chatInput)
      } else {
        this.chatThread.push({
          text: this.chatInput,
          author: 'you',
          date: formatDate,
          replyThread:[],
          reply: false
        })
        this.chatInput = ''
      }  
    },
    hdlSelected(v) {
      //reparse object since its on Observer
      console.log(JSON.parse(JSON.stringify(v)))
      //remove string after @ to replace with selected value
      let data = this.chatInput.slice(0,this.chatInput.lastIndexOf('@'))
      let newInput =data+'@' + JSON.parse(JSON.stringify(v)).name
      this.chatInput = newInput
    },
  },
}
</script>

<style scoped>
.chat-box {
  width: 400px;
  margin: 0 auto;
  height: 500px;
}
.text-area {
  border: 1px solid black;
  height: 100%;
  max-height: 400px;
  padding: 10px;
  overflow: auto;
  background-color: #ebebeb;
}
.text-area::-webkit-scrollbar{
  width: 6px;
  height: 6px;
}
.text-area::-webkit-scrollbar-track{
  background-color: #ffffff;
}
.text-area::-webkit-scrollbar-thumb{
  background-color: #5d6e7b;
}
</style>
