<template>
  <div id="app">
    <xf-chat-box :threads="chatThreads" :users="users" />
  </div>
</template>

<script>
import users from '@/assets/users.js'

export default {
  name: 'app',
  data(){
    return{
      chatThreads: [
        {
          text: 'Hi There!',
          author: 'Bob',
          date: '1/1 10:00',
          replyThread:[],
          reply: false,
        },
      ],
       users: users,
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
