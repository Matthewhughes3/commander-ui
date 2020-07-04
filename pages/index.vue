<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8 md6>
      <commands />
    </v-flex>
  </v-layout>
</template>

<script>
import Commands from "../components/commands"

export default {
  components: {
    Commands
  }
}
</script>
