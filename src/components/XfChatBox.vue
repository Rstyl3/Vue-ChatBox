<template>
  <div class="chat-box">
    <div class="text-area" >
      <xf-chat-thread :chatThread="thread" :users="users" v-for="thread in Threads" :key="thread.id" />
    </div>
    <xf-chat-input :chatInput.sync="chatInput" :users="users" @btnClick="hdlSend" @itemSelected="hdlSelected"/>
  </div>
</template>

<script>
import XfChatInput from './XfChatInput.vue'
import XfChatThread from './XfChatThread.vue'


export default {
  name: 'xf-chat-box',
  components: {
    XfChatInput,
    XfChatThread,
  },
  props:['Threads','users'],
  data() {
    return {
      chatInput: '',
    }
  },
  computed:{
  },
  methods: {
    hdlSend(){
      let date = new Date();
      let formatDate = (date.getMonth()+ 1) + '/' + date.getDay() + ' ' +  date.getHours() + ":" + date.getMinutes() 
      if (this.chatInput === '') {
        console.log('write a message', this.chatInput)
      } 
      else {
        let mention = /\s(@[\w_-]+)/g
        if(this.chatInput.match(mention)){
          console.log(this.chatInput.match(mention))
          let matched = this.chatInput.match(mention)
          let i = -1
          this.chatInput = this.chatInput.replace(mention, function(){return '<b>'+ matched[++i] +'</b>'})
        }
        this.Threads.push({
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
  height: 100%;
  max-height: 400px;
  padding: 10px;
  overflow: auto;
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
