<template>
  <button :style="{background:color}"  class="btn" v-on:click="onClick">{{text}}</button>
  <!--or v-on:click="$emit('toggle-add-task')"-->
</template>

<script>
export default {
  name: 'Button',
  props:{
    text:String,
    color:String,
  },
  methods:{
    onClick(){
    console.log("add task button clicked")
    this.$emit('btn-click')
    }
  }
}
</script>