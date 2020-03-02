<template>
  <!-- you define your Vue template here -->
  <div>
    <q-btn @click="increment()">
      {{ value }}
    </q-btn>
  </div>
</template>

<script>
// This is where your Javascript goes
// to define your Vue component, which
// can be a Layout, a Page or your own
// component used throughout the app.

export default {
  name: 'Counter',
  data: function () {
    return {
      value: 0
    }
  },
  methods: {
    increment () {
      this.value ++
    }
  }
}
</script>

<style>
/* This is where your CSS goes */
</style>