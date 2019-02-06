<template>
  <div class="x-input">
    <input
      type="search"
      ref="input"
      :value="chatInput"
      @input="$emit('update:chatInput', $event.target.value)"
      @keyup.enter="$emit('btnClick')"
      @keydown.up="up"
      @keydown.down="down"
      @keydown.esc="esc"
      placeholder="Type @ to mention someone"
    >
    <button @click="$emit('btnClick')">Send</button>
    <ul class="pop-up" v-show="popUp || matches==[]" ref="optionsList" :style="{left: popUpPosition+'%'}" >
      <li
        v-for="(user,index) in matches"
        :key="user['name']"
        @click="itemClicked(index)"
        :class="{'selected': (selected == index)}"
      >{{user.name}}</li>
    </ul>
  </div>
</template>

<script>
export default {
  props: ['chatInput', 'users'],
  data() {
    return {
      popUp: false,
      selected: 0,
      selectedItems: null,
      itemHeight: 22.9,
      popUpPosition: 0
    }
  },
  watch: {
    chatInput() {

      if(this.chatInput.endsWith(' @')){
              console.log(this.chatInput.length)
              this.popUpPosition = this.chatInput.length * 1.65
        this.popUp = true
        }
      else if(!this.chatInput.match('@')){this.popUp = false}
      if(this.chatInput.match('@ ')){this.popUp = false}
    },
  },
  computed: {
    matches() {
      if (this.chatInput == '') {
        return []
      }        
      if (this.popUp) {
        //number of @ in input
        let splitCount= [...this.chatInput].filter(l => l === '@').length 
        console.log("number of mentions",splitCount)
        //Filter list after @ values
        if(  splitCount >= 1){
          return this.users.filter(item => item['name'].toLowerCase().includes(this.chatInput.split('@')[splitCount].toLowerCase()))
        }
      }
      return this.users.filter(item => item['name'].toLowerCase().includes(this.chatInput.toLowerCase()))
    },
  },
  methods: {
    itemClicked(i) {
      this.selected = i
      this.selectItem()
      this.popUp = false
    },
    selectItem() {
      let replaceInput = this.matches[this.selected]
      this.$emit('itemSelected', replaceInput)
      //focus back to input
      this.inputFocus()
    },
    esc(){
      this.popUp=false
    },
    up() {
      if (this.selected == 0) {
        return
      }
      this.selected -= 1
      this.scrollToItem();
    },
    down() {
      if (this.selected >= this.matches.length - 1) {
        return
      }
      this.selected += 1
      this.scrollToItem();
    },
    inputFocus(){
     this.$refs.input.focus()
    },
    scrollToItem() {
      this.$refs.optionsList.scrollTop = this.selected * this.itemHeight
    },
  },
}
</script>

<style scoped>
.x-input {
  position: relative;
  border: 1px solid darkgrey;
  display: inline-block;
  max-width: 500px;
  width: 100%;
}
.x-input input {
  width: 100%;
  box-sizing: border-box;
  border: none;
  bottom: 0;
  left: 0;
  margin: 0;
  outline: none;
  padding: 0 8px;
  height: 22px;
}
.x-input button {
  position: absolute;
  right: 0;
  top: 0;
}
.pop-up {
  list-style: none;
  position: absolute;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  padding: 0;
  left: 0;
  right: 0;
  width: 100%;
  max-width: 380px;
  bottom: 18px;
  margin: 0 auto;
  background-color: #fff;
  overflow-x: hidden;
  max-height: 90px;
  /*Scroll bar issue fix*/
  transform: translateZ(0);
  -webkit-transform: translateZ(0);
}
.pop-up li{
  padding: 2px 2px 2px 5px;
}
.pop-up li:hover {
  background: #ebebeb;
  cursor: pointer;
}
.pop-up::-webkit-scrollbar{
  width: 6px;
  height: 6px;
}
.pop-up::-webkit-scrollbar-track{
  background-color: #ffffff;
}
.pop-up::-webkit-scrollbar-thumb{
  background-color: #5d6e7b;
}
.pop-up li.selected {
  background: #ebebeb;
}
</style>
