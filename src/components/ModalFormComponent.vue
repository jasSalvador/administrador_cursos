<template>
  <v-row justify="center">
    <v-dialog v-model="dialogInterno" persistent max-width="600px">
      <template v-slot:activator="{ on, attrs }">
        <v-btn color="primary" dark v-bind="attrs" v-on="on">
          Agregar curso
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="text-h5">{{
            modalForm.id ? "Actualizar curso" : "Agregar curso"
          }}</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="localForm.nombre"
                  label="Nombre*"
                  required
                ></v-text-field>
              </v-col>

              <!--url img-->
              <v-col cols="12">
                <v-text-field
                  v-model="localForm.img"
                  label="URL de la imagen*"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model.number="localForm.cupos"
                  label="Cupos del curso*"
                  type="number"
                  min="1"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model.number="localForm.inscritos"
                  label="Inscritos en el curso*"
                  type="number"
                  required
                  min="0"
                  :max="localForm.cupos"
                ></v-text-field>
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="localForm.duracion"
                  label="Duración del curso*"
                  placeholder="Ej: 2 meses"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12">
                <label for="completado">Completado:</label>
                <input
                  type="checkbox"
                  v-model="localForm.completado"
                  :disabled="!localForm.id"
                />
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="localForm.fecha_registro"
                  label="Fecha de registro*"
                  required
                  :disabled="!localForm.id"
                ></v-text-field>
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="localForm.costo"
                  label="Costo del curso*"
                  type="number"
                  min="0"
                  prefix="$"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12">
                <v-textarea
                  v-model="localForm.descripcion"
                  label="Descripción del curso*"
                  required
                  rows="4"
                ></v-textarea>
              </v-col>
            </v-row>
          </v-container>
          <small>*Campos obligatorios</small>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <!--btn modal-->
          <v-btn color="green" dark @click="agregar">
            {{ modalForm.id ? "Actualizar" : "Agregar" }}
          </v-btn>
          <v-btn color="orange" dark @click="limpiar">Limpiar formulario</v-btn>
          <v-btn color="red" dark @click="cancelar"> Cancelar </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
import Vue from "vue";
export default Vue.extend({
  name: "ModalFormComponent",
  props: {
    dialog: {
      type: Boolean,
      required: true,
    },
    modalForm: {
      type: Object,
      required: true,
      default: () => ({
        nombre: "",
        img: "",
        cupos: "",
        inscritos: "",
        duracion: "",
        fecha_registro: "",
        costo: "",
        descripcion: "",
        completado: "false",
        id: null,
      }),
    },
  },
  data() {
    return {
      dialogInterno: this.dialog,
      localForm: { ...this.modalForm }, //copia la prop
    };
  },

  methods: {
    agregar() {
    if (this.localForm.inscritos > this.localForm.cupos) {
      alert(
        "La cantidad de inscritos no puede ser mayor a la cantidad de cupos disponibles"
      );
      return;
    }

    //fecha en nuevo curso
    if (!this.localForm.id) {
      const fecha = new Date().toLocaleDateString();
      this.localForm.fecha_registro = fecha;
      this.localForm.completado = false;
    }

    //vuex para gestionar los cursos (agregar o actualizar)
    if (this.localForm.id) {
      //si tiene id, actualiza
      this.$store.dispatch("actualizarCurso", this.localForm);
    } else {
      //si no tiene id, agregaa nuevo curso
      this.$store.dispatch("agregarCurso", this.localForm);
    }

    if (this.localForm.id) {
      this.$emit("actualizar", this.localForm); //para actualizar en el padre
    } else {
      this.$emit("guardar", this.localForm); //para guardar en el componente padre
    }

    this.limpiar();
    this.dialogInterno = false;
    },

    limpiar() {
      this.localForm = {
        nombre: "",
        img: "",
        cupos: "",
        inscritos: "",
        duracion: "",
        fecha_registro: "",
        costo: "",
        descripcion: "",
        completado: false,
        id: null,
      };
      this.$emit("limpiar");
    },

    cancelar() {
      this.dialogInterno = false;
      this.limpiar();
    },
  },

  created() {
    //recuperar cursos de localS al cargar la pag
    const cursos = JSON.parse(localStorage.getItem("cursos")) || [];
    if (cursos.length > 0) {
      this.cursos = cursos; //agregar cursos desde localS
    }

    //establecer fecha de inicio si no hay
    const fechaHoy = new Date();
    const formatoFecha = fechaHoy.toLocaleDateString("es-ES");

    if (!this.localForm.fecha_registro) {
      this.localForm.fecha_registro = formatoFecha;
    }
  },

  watch: {
    modalForm: {
      handler(newValue) {
        this.localForm = { ...newValue }; //actualizar localForm con nuevos datos
      },
      deep: true,
    },
    //para abrir modal desde tabla
    dialog(newVal) {
      this.dialogInterno = newVal;
    },
    dialogInterno(newVal) {
      this.$emit("update:dialog", newVal); //enviar cambio al padre
    },
    "localForm.completado": {
      handler(newValue) {
        //si el curso pasa a completado, actualizar inscritos a 0
        if (newValue === true) {
          this.localForm.inscritos = 0;
        }
      },
      immediate: true,
    },
  },
});
</script>