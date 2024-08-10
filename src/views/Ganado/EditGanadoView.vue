<template>
    <div>
      <h1>Editar Ganado</h1>
      <form @submit.prevent="submitForm" v-if="form">

        <div class="form-group">
          <label for="name">Nombre:</label>
          <input type="text" id="name" v-model="form.nombre" :class="{ 'is-invalid': errors.nombre }"
            placeholder="Ingrese el nombre" />
          <div v-if="errors.nombre" class="invalid-feedback">{{ errors.nombre }}</div>
        </div>

        <div class="form-group">
            <label for="type">Tipo:</label>
            <select id="type" v-model="form.tipoId" :class="{ 'is-invalid': errors.tipoId }">
              <option :value="type.id" v-for="(type, index) in tipoList" :key="`type-${index}`">{{ type.nombre }}
              </option>
            </select>
            <div v-if="errors.tipoId" class="invalid-feedback">{{ errors.tipoId }}</div>
        </div>

        <div class="form-group">
          <label for="fecha">Fecha:</label>
          <input type="date" id="fecha" v-model="form.fecha" :class="{ 'is-invalid': errors.fecha }"
            placeholder="Ingrese la fecha" />
          <div v-if="errors.fecha" class="invalid-feedback">{{ errors.fecha }}</div>
        </div>

        <button type="submit" class="btn btn-primary">Registrar</button>
      </form>
    </div>
  </template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex'
export default {
  name: 'EditGanado',
  data() {
    return {
      tipoList: [],
      errors: {}
    };
  },
  props:['item'],
  methods: {
    ...mapActions(['increment']),
    validateForm() {
      this.errors = {};

      if (!this.form.tipoId) {
        this.errors.tipoId = 'El tipo es obligatorio.';
      }

      if (!this.form.fecha) {
        this.errors.fecha = 'La fecha es obligatorio.';
      }

      if (!this.form.nombre) {
        this.errors.hora = 'La hora es nombre.';
      }


      return Object.keys(this.errors).length === 0;
    },

    submitForm() {
      if (this.validateForm()) {
        // Enviar los datos al servidor
        this.save();
        // Reiniciar el formulario
        this.form = {
            tipoId: null
        };
      }
    },
    save() {
      const vm = this;
      this.axios.patch(this.baseUrl + "/ganados/"+this.item.id, this.form)
        .then(function (response) {
          if (response.status == '200') {
            vm.$emit('on-update', response.data);
          }
          vm.itemList = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    },
    gettipoList() {
      const vm = this;
      this.axios.get(this.baseUrl + "/tipos")
        .then(function (response) {
          vm.tipoList = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    },
  },
  computed: {
    // propiedades computadas que dependen de otras propiedades reactivas
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl
    },
    form(){
      return Object.assign({},this.item);
    }
  },
  mounted() {
    this.gettipoList();
  },
}
</script>
  
<style scoped></style>


