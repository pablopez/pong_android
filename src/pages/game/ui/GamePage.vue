<template>
  <PongGame />
</template>

<script setup lang="ts">
// GamePage.vue resides in src/pages/game/ui. The PongGame component is
// located in src/widgets/pong-game/ui, which is three levels up from here.
// Using the correct relative path ensures Vite can resolve the import.
import PongGame from '../../../widgets/pong-game/ui/PongGame.vue'
</script>
