<template>
  <v-app>
    <v-content>
      <MainScreen/>
    </v-content>
  </v-app>
</template>

<script>
import MainScreen from './views/MainScreen'

export default {
  name: 'App',

  components: {
    MainScreen
  },

  data: () => ({
    //
  })
}
</script>
