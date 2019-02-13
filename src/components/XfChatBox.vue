<template>
  <div class="chat-box">
    <div class="text-area" >
      <xf-chat-thread :chatThread="thread" :users="users" v-for="thread in threads" :key="thread.id" />
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
  props:['threads','users'],
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
        // let mention = /\s(@[\w_-]+)/g
        // let newMatch = userlist.filter(e => matched.indexOf(e) !== -1)
        let userlist = new RegExp(this.users.map( n => '@'+n.name).join('|'),'g')         //regex list of users with @ added
        if(this.chatInput.match(userlist)){
        let matched = this.chatInput.match(userlist)
        let i = -1
        this.chatInput = this.chatInput.replace(userlist, function(){return '<b>'+ matched[++i] +'</b>'})
        //find selected mentions as object
        let selected = matched.map(n => this.users.find(u => u.name == n.substring(1)))
        console.log('selected mentions are: ', JSON.parse(JSON.stringify(selected)))
        }
        this.threads.push({
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
      // console.log(JSON.parse(JSON.stringify(v)))
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
  width: 100%;
  margin: 0 auto;
  height: 100%;
}
.text-area {
  height: 100%;
  /* max-height: 400px; */
  padding: 26px 2px 5px 2px;
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
