<template>
  <div class="thread">
    <div class="message-box">
      <h3> {{chatThread.author}} <span>{{chatThread.date}}</span> </h3>
      <p>{{chatThread.text}}</p>
    </div>

    <div class="reply-box" v-for="reply in chatThread.replyThread" :key="reply.id">
      <h3>{{reply.author}} <span>{{reply.date}}</span></h3>
      <p>{{reply.text}}</p>
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
      console.log('this is reply date', formatDate)
      if (this.replyInput === '') {
        console.log('write a message', this.replyInput)
      } else {
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
      console.log(JSON.parse(JSON.stringify(v)))
      //remove string after @ to replace with selected value
      let data = this.replyInput.slice(0,this.replyInput.lastIndexOf('@'))
      let newInput =data+'@' + JSON.parse(JSON.stringify(v)).name
      this.replyInput = newInput
    },
  },
}
</script>

<style scope>
.thread {
  margin-bottom: 5px;
  background-color: #ffffff;
}
.message-box {
  text-align: left;
  padding-left: 10px;
}
.reply-box {
  text-align: left;
  background-color: #f6f6f6;
}
.message-box span,
.reply-box span {
  font-weight: normal;
  font-size: 15px;
}
.reply-box p,
.reply-box h3 {
  margin: 0;
  padding: 5px 5px 5px 20px;
}
.reply-btn {
}
</style>
