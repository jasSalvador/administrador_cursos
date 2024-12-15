<template>
  <v-container class="mx-auto pa-4">
    <div class="text-center mx-auto py-2">
      <h1 class="text-center mx-auto my-5">Administración</h1>
      <!--btn modal-->
      <ModalFormComponent
        :dialog.sync="dialog"
        :modalForm="modalForm"
        @guardar="guardarCurso"
        @actualizar="actualizarCurso"
        @limpiar="limpiarForm"
        class="my-4"
      />
    </div>
    <!--tabla-->
    <v-data-table
      :headers="headers"
      :items="cursos"
      :items-per-page="10"
      class="elevation-1"
    >
      <template v-slot:[`item.costo`]="{ item }">
        <span class="bg-green">
          {{ item.costo }}
        </span>
      </template>

      <template v-slot:[`item.fecha_registro`]="{ item }">
        <span class="bg-green">
          {{ item.fecha_registro }}
        </span>
      </template>

      <template v-slot:[`item.completado`]="{ item }">
        <span
          :class="{
            'terminado-true': item.completado === 'Si',
            'terminado-false': item.completado === 'No',
          }"
        >
          {{ item.completado }}
        </span>
      </template>

      <!--btn tabla-->
      <template v-slot:[`item.acciones`]="{ item }">
        <v-btn icon @click="editarCurso(item.id)">
          <v-icon color="yellow">mdi-pencil</v-icon>
        </v-btn>
        <v-btn icon @click="confirmarEliminarCurso(item)">
          <v-icon color="red">mdi-delete</v-icon>
        </v-btn>
      </template>
    </v-data-table>

    <!--mensajes informativos-->
    <!--alumnos permitidos-->
    <div class="my-5">
      <div class="purple--text purple-border pt-3 ps-3 my-2">
        <p>
          <v-icon color="purple">mdi-account-multiple</v-icon>
          Cantidad total de alumnos permitidos:
          <strong>{{ alumnosPermitidos }}</strong>
          alumnos.
        </p>
      </div>

      <!--alumnos inscritos-->
      <div class="info--text border info-border pt-3 ps-3 my-2">
        <p>
          <v-icon color="info">mdi-account-check</v-icon>
          Cantidad total de alumnos inscritos:
          <strong>{{ alumnosInscritos }}</strong>
          alumnos.
        </p>
      </div>

      <!--cupos restantes-->
      <div class="red--text red-border pt-3 ps-3 my-2">
        <p>
          <v-icon color="red">mdi-account-plus</v-icon>
          Cantidad total de cupos restantes:
          <strong>{{ cuposRestantes }}</strong>
          cupos.
        </p>
      </div>

      <!--cursos terminados-->
      <div class="pink--text pink-border pt-3 ps-3 my-2">
        <p>
          <v-icon color="pink">mdi-cancel</v-icon>
          Cantidad total de cursos terminados:
          <strong>{{ cursosTerminados }}</strong>
          cursos.
        </p>
      </div>

      <!--cursos activos-->
      <div class="green--text green-border pt-3 ps-3 my-2">
        <p>
          <v-icon color="green">mdi-bell-ring</v-icon>
          Cantidad total de cursos activos:
          <strong>{{ cursosActivos }}</strong>
          cursos.
        </p>
      </div>

      <!--total cursos-->
      <div class="orange--text orange-border pt-3 ps-3 my-2">
        <p>
          <v-icon color="orange">mdi-bell-ring</v-icon>
          Cantidad total de cursos:
          <strong>{{ totalCursos }}</strong>
          cursos.
        </p>
      </div>
    </div>
  </v-container>
</template>

<script>
import ModalFormComponent from "@/components/ModalFormComponent.vue";
import Vue from "vue";
export default Vue.extend({
  name: "AdminView",
  components: {
    ModalFormComponent,
  },
  data() {
    return {
      dialog: false,
      headers: [
        {
          text: "Curso",
          value: "nombre",
          align: "start",
          sortable: false,
        },
        {
          text: "Cupos",
          value: "cupos",
          sortable: false,
        },
        {
          text: "Inscritos",
          value: "inscritos",
          sortable: false,
        },
        {
          text: "Duración",
          value: "duracion",
          sortable: false,
        },
        {
          text: "Costo",
          value: "costo",
          sortable: false,
        },
        {
          text: "Terminado",
          value: "completado",
          sortable: false,
        },
        {
          text: "Fecha",
          value: "fecha_registro",
          sortable: false,
        },
        {
          text: "Acciones",
          value: "acciones",
          sortable: false,
        },
      ],
      modalForm: {
        nombre: "",
        imagen: "",
        cupos: "",
        inscritos: "",
        duracion: "",
        fecha: "",
        costo: "",
        descripcion: "",
      },
    };
  },
  computed: {
    cursos() {
      console.log("Cursos obtenidos:", this.$store.getters.obtenerCursos);
      return this.$store.getters.obtenerCursos.map((curso) => ({
        ...curso,
        completado: curso.completado ? "Si" : "No",
      }));
    },
    alumnosPermitidos() {
      return this.$store.getters.obtenerCursos.reduce(
        (total, curso) => total + curso.cupos,
        0
      );
    },
    alumnosInscritos() {
      return this.$store.getters.obtenerCursos.reduce(
        (total, curso) => total + curso.inscritos,
        0
      );
    },
    cuposRestantes() {
      return this.$store.getters.obtenerCursos.reduce((total, curso) => {
        return total + curso.cupos - curso.inscritos;
      }, 0);
    },
    cursosTerminados() {
      return this.$store.getters.obtenerCursos.filter(
        (curso) => curso.completado
      ).length;
    },
    cursosActivos() {
      return this.$store.getters.obtenerCursos.filter(
        (curso) => !curso.completado
      ).length;
    },
    totalCursos() {
      return this.$store.getters.obtenerCursos.length;
    },
  },
  methods: {
    guardarCurso(curso) {
      const fecha = new Date().toLocaleDateString();
      curso.fecha_registro = fecha;

      console.log("fecha cambiada", fecha);
      
      //si no tiene id, crear curso, forzar completado a false y establecer fecha
      if (!curso.id) {
        curso.completado = false;
        curso.fecha_registro = fecha;
        this.$store.dispatch("agregarCurso", curso);
      } else {
        if (curso.completado === true) {
          curso.inscritos = 0;
        }
        //su tiene id, actualizar
        this.$store.dispatch("actualizarCurso", curso);
      }

      this.dialog = false;
      this.limpiarForm();
    },

    limpiarForm() {
      try {
        this.modalForm = {
          id: null,
          nombre: "",
          fecha_registro: "",
          completado: false,
          inscritos: 0,
          cupos: 0,
        };
      } catch (error) {
        console.error("Error al limpiar formulario:", error);
      }
    },

    editarCurso(id) {
      //console.log(`editar curso con ID: ${id}`);
      const curso = this.$store.getters.obtenerCursos.find(
        (curso) => curso.id === id
      );
      if (curso) {
        this.modalForm = { ...curso }; //copiar datos al form principal
        this.dialog = true;
      } else {
        //console.log("No se encontró el curso con ID:", id);
      }
    },

    actualizarCurso(curso) {
      //console.log("Actualizando curso:", curso);
      //actualizar el curso
      this.$store.dispatch("actualizarCurso", curso);
      this.dialog = false;
    },

    eliminarCurso(cursoId) {
      this.$store.dispatch("eliminarCurso", cursoId);
    },
    confirmarEliminarCurso(curso) {
      //console.log(`eliminar curso con ID: ${curso}`);
      const confirmar = window.confirm(`Estás seguro de eliminar el curso "${curso.nombre}" ?`);
      if (confirmar) {
        this.eliminarCurso(curso.id);
      }
    },
  },
  created() {
    console.log(this.$store.getters.obtenerCursos);
    console.log(this.alumnosPermitidos);
  },
});
</script>

<style scoped>
.bg-green {
  background-color: #03dd3a;
  color: white;
  border-radius: 50px;
  padding: 0.3rem 0.8rem;
  display: inline-block;
  text-align: center;
}
.terminado-true {
  background-color: #049ff8;
  color: white;
  border-radius: 50px;
  padding: 0.3rem 0.7rem;
  display: inline-block;
  text-align: center;
}
.terminado-false {
  background-color: #4c4e4e;
  color: white;
  border-radius: 50px;
  padding: 0.3rem 0.7rem;
  display: inline-block;
  text-align: center;
}
.purple-border {
  border: 1px solid purple;
}
.info-border {
  border: 1px solid #049ff8;
}
.red-border {
  border: 1px solid red;
}
.pink-border {
  border: 1px solid rgb(248, 168, 182);
}
.green-border {
  border: 1px solid #03dd3a;
}
.orange-border {
  border: 1px solid orange;
}
</style>