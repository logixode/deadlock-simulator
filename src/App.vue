<template>
  <div id="app" class="w-75 mx-auto">
    <h1 class="mt-5 text-center text-uppercase">Deadlock Simulator</h1>
    <h3 class="text-center mb-4">- Banker Algorithm -</h3>
    <hr>
    <TableDeadlock/>
  </div>
</template>

<script>
import TableDeadlock from './components/TableDeadlock.vue'

export default {
  name: 'app',
  components: {
    TableDeadlock
  }
}
</script>

<style scoped>
h1 {
  font-size: 50px;
}
</style>
