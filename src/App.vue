<template>
  <div id="app">
    <DragToDismiss class="example-content" :dismissed.sync="dismissed">
      <h2>Drag to Dismiss</h2>
      This is my dismissable content. Drag me away from the center to dismiss me!
    </DragToDismiss>
    <transition name="fade">
      <a href="" v-if="dismissed" @click.prevent="dismissed=false">Reset</a>
    </transition>
  </div>
</template>

<script>
import DragToDismiss from './components/DragToDismiss.vue'

export default {
  name: 'App',
  data() {
    return {
      dismissed: false
    }
  },
  components: {
    DragToDismiss
  }
}
</script>

<style>
html, body {margin: 0; height: 100%; overflow: hidden}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 200px;
}

.example-content {
  user-select: none;
  margin-left: auto;
  margin-right: auto;
  width: 250px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  border-radius: 4px;
  padding: 10px 20px 30px 20px;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
