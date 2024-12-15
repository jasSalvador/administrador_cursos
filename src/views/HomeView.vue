<template>
  <div class="mb-5">
    <h1 class="text-center mx-auto my-5">Lista de cursos</h1>
    <!-- card -->
    <v-container>
      <v-row justify="space-around">
        <v-card width="300" v-for="curso in obtenerCursos" :key="curso.id" class="mb-5">
          <v-img
            width="180px"
            height="180px"
            :src="curso.img"
            class="text-center mx-auto my-2"
          >
          <v-app-bar flat color="rgba(0, 0, 0, 0)"> </v-app-bar>
          </v-img>

          <v-card-text>
            <div class="font-weight-bold ml-8 mb-2">
              {{ curso.nombre }}
              <h5>Informacion</h5>
            </div>

            <!--v-timeline-->
            <v-timeline align-top dense>
              <!-- Costo/duracion -->
              <v-timeline-item :key="'costo'" color="purple" small>
                <div class="f-flex flex-column">
                  <div class="font-weight-normal d-flex flex-wrap">
                    <p class="me-1 mb-0">Costo:</p>
                    <strong>{{ curso.costo }}</strong>
                  </div>
                  <div class="font-weight-normal d-flex flex-wrap">
                    <p class="me-1">Duración:</p>
                    <strong>{{ curso.duracion }}</strong>
                  </div>
                </div>
              </v-timeline-item>

              <!--cupos/completado-->
              <v-timeline-item :key="'cupos'" color="green" small>
                <div class="f-flex flex-column">
                  <div class="font-weight-normal d-flex flex-wrap">
                    <p class="me-1 mb-0">Cupos:</p>
                    <strong>{{ curso.cupos }}</strong>
                  </div>
                  <div class="font-weight-normal d-flex flex-wrap">
                    <p class="me-1">Completado:</p>
                      {{ curso.completado }}
                  </div>
                </div>
              </v-timeline-item>


              <!--fecha registro-->
              <v-timeline-item :key="'fecha_registro'" color="purple" small>
                <div>
                  <div class="font-weight-normal">
                    <p class="mb-0">Fecha de registro:</p>
                    {{ curso.fecha_registro }}
                  </div>
                </div>
              </v-timeline-item>

              <!-- Descripción -->
              <v-timeline-item :key="'descripcion'" color="green" small>
                <div>
                  <div class="font-weight-normal">
                    <p class="mb-0">Descripción:</p>
                  </div>
                  <div>{{ curso.descripcion }}</div>
                </div>
              </v-timeline-item>
            </v-timeline>
          </v-card-text>
        </v-card>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import Vue from "vue";
export default Vue.extend({
  name: "HomeView",
  data() {
    return {};
  },
  computed: {
    obtenerCursos() {
      return this.$store.getters.obtenerCursos.map((curso) => ({
        ...curso,
        completado: curso.completado ? "Si" : "No",
      }));
    },
  },
  created() {
    console.log(this.$store.getters.obtenerCursos);
  },
});
</script>
