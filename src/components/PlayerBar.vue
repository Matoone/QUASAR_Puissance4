<template>
  <!-- you define your Vue template here -->
  <q-chip class="shadow-10 text-bold row" square>
    <q-avatar>
      <img :src="player.avatarUrl">
    </q-avatar>
    <div>
      {{ player.token }}
    </div>
    <q-input dark borderless v-model="player.name" input-class="text-left text-bold text-accent" class="q-ml-xs">
    </q-input>
    Score: {{ player.score }} 
    <q-chip v-show="isAi">AI</q-chip>
  </q-chip>
      
</template>

<script>
// This is where your Javascript goes
// to define your Vue component, which
// can be a Layout, a Page or your own
// component used throughout the app.

export default {
  name: 'PlayerBar',
  props: ['player', 'isAi'],
  data: function () {
    return {
      
    }
  },
  methods: {
    
  }
}
</script>

<style>
/* This is where your CSS goes */
</style>