<template>
  <v-app>
    <v-app-bar
      app
      color="purple"
      dark
    >
      <div class="d-flex align-center">
        <router-link 
        to="/" 
        class="white--text text-decoration-none"
      >
        Cursos AlfaWeb
      </router-link>
      </div>

      <v-spacer></v-spacer>

      <v-btn 
        to="/" text
      >
        <!-- <span class="mr-2">Inicio</span> -->
        <v-icon>mdi-home</v-icon>
      </v-btn>

      <v-btn
        to="/admin" text
      >
        <!-- <span class="mr-2">Administracion</span> -->
        <v-icon>mdi-wrench</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <router-view/>
    </v-main>
  </v-app>
</template>

<script>
import Vue from "vue"
  export default Vue.extend({
    name: "NavbarComponent",
  })
</script>

<style lang="scss" scoped>

</style>