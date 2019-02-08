<template>
  <div class="thread">
    <div class="message-box">
      <div class="chat-header"> {{chatThread.author}} <span>{{chatThread.date}}</span> </div>
      <p v-html="chatThread.text"></p>
    </div>

    <div class="reply-box" v-for="reply in chatThread.replyThread" :key="reply.id">
      <div class="chat-header">{{reply.author}} <span>{{reply.date}}</span></div>
      <p v-html="reply.text"></p>
    </div>
    <button class="reply-btn" v-show="!replybtn" @click="hdlbtn">Reply</button>
    <xf-chat-input ref="customInput" :chatInput.sync="replyInput" :users="users" v-show="replybtn" @btnClick="hdlReply" @itemSelected="hdlSelected" />
  </div>
</template>

<script>
import XfChatInput from './XfChatInput.vue'

export default {
  components: {
    XfChatInput,
  },
  props: ['chatThread', 'users'],
  data() {
    return {
      replybtn: false,
      replyInput: '',
    }
  },
  methods: {
    hdlbtn() {
      this.replybtn = !this.replybtn
      //focus to child Input
      this.$nextTick(() => {
       this.$refs.customInput.inputFocus();
      })
    },
    hdlReply(e) {
      this.replybtn = !this.replybtn
      let date = new Date()
      let formatDate = date.getMonth() + 1 + '/' + date.getDay() + ' ' + date.getHours() + ':' + date.getMinutes()
      if (this.replyInput === '') {
        console.log('write a message', this.replyInput)
      } else {
        let userlist = new RegExp(this.users.map( n => '@'+n.name).join('|'),'g')
        if(this.replyInput.match(userlist)){
        let matched = this.replyInput.match(userlist)
        let i = -1
        this.replyInput = this.replyInput.replace(userlist, function(){return '<b>'+ matched[++i] +'</b>'})
        //find selected mentions as object
        let selected = matched.map(n => this.users.find(u => u.name == n.substring(1)))
        console.log('selected mentions are: ', JSON.parse(JSON.stringify(selected)))
        }
        this.chatThread.replyThread.push({
          text: this.replyInput,
          author: 'you',
          date: formatDate,
          reply: false,
        })

        this.replyInput = ''
      }
    },
    hdlSelected(v) {
      //remove string after @ to replace with selected value
      let data = this.replyInput.slice(0,this.replyInput.lastIndexOf('@'))
      let newInput =data+ '@' + JSON.parse(JSON.stringify(v)).name
      this.replyInput = newInput
    },
  },
}
</script>

<style scope>
.thread {
  margin-bottom: 5px;
  background-color: rgba(255, 255, 255, 0.43);
}
.message-box,.reply-box {
  text-align: left;
  padding: 5px;
}
.reply-box {
  background-color: rgba(0, 0, 0, 0.09);
}
.chat-header{
 font-size: 1.17em;
 font-weight: bold;
}
.message-box span,
.reply-box span {
 font-weight: normal;
 font-size: 1em;
}
.reply-box p,.message-box p {
 margin: 0;
 padding: 5px 0;
}
.reply-box p >b{
font-weight: bold;
}
.reply-btn {
}
</style>
