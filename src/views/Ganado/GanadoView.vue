<template>
    <div>
        <Modal v-model:modelValue="showModalNuevo">
            <RegisterGanadoView @on-register="onRegister()" />
        </Modal>
        <Modal v-model:modelValue="showModalEdit">
            <EditGanadoView @on-update="onUpdate()" :item="itemToEdit" />
        </Modal>
        <h1>Lista de Ganados</h1>
        <!-- <div>{{path}}</div> -->
        <button @click="showModalNuevo = true" class="btn btn-primary">Nuevo</button>
        <button @click="buscar()" class="btn btn-lith" style="float:right">Buscar</button>
        <input type="search" style="float:right" v-model="textToSearch" @search="buscar()">
        <div style="margin: 20px 0;">
            <h3>Filtros:</h3>
            <form @submit.prevent="filtrar()">

                <label for="fecha"> Fecha: </label>
                <input type="date" id="fecha" v-model="filter.fecha" placeholder="Ingrese la fecha" />

                <label for="tipo"> Tipo: </label>
                <select id="tipo" v-model="filter.tipoId">
                    <option value="">Todos</option>
                    <option :value="tipo.id" v-for="(tipo, index) in tipoList" :key="`tipo-${index}`">{{ tipo.nombre }}
                    </option>
                  </select>
                <button type="submit" class="btn btn-lith">Fitrar</button>
            </form>
        </div>
        <table>
            <thead>
                <tr>
                    <th>No.</th>
                    <th>Nombre</th>
                    <th>Fecha</th>
                    <th>Tipo</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in itemList" :key="index">
                    <td>{{ 1 + index }}</td>
                    <td>{{ item.nombre }}</td>
                    <td>{{ item.fecha }}</td>
                    <td>{{ item.tipo.nombre }}</td>
                    <td>
                        <button @click="edit(item)" class="btn btn-dark" style="margin-right: 15px;">Editar</button>
                        <button @click="Eliminar(item.id)" class="btn btn-danger">Eliminar</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
  
<script>
import { mapState, mapGetters, mapActions } from 'vuex'
import Modal from '../../components/Modal.vue'
import RegisterGanadoView from './RegisterGanadoView.vue'
import EditGanadoView from './EditGanadoView.vue'

export default {
    name: 'Cita',
    data() {
        return {
            currentPage: 1,
            totalPages: 100, // Este valor debe ser calculado según tus datos
            showModalNuevo: false,
            showModalEdit: false,
            itemToEdit: null,
            textToSearch: '',
            textToFilter: '',
            itemList: [],
            tipoList: [],
            path: '',
            filter: {
                fecha: null,
                tipoList:''
            }
        }
    },
    components: {
        // Registro de componentes que se utilizaran.
        Modal,
        RegisterGanadoView,
        EditGanadoView
    },
    methods: {
        // métodos que se pueden llamar desde la plantilla o desde otras partes del componente.
        ...mapActions(['increment']),
        getList() {
            const vm = this;
            this.path = this.baseUrl + "/ganados?_sort=fecha&_order=desc,asc&_expand=tipo" + this.textToFilter + "&q=" + this.textToSearch;
            this.axios.get(this.baseUrl + "/ganados?_sort=fecha&_order=desc,asc&_expand=tipo" + this.textToFilter + "&q=" + this.textToSearch)
                .then(function (response) {
                    vm.itemList = response.data;
                })
                .catch(function (error) {
                    console.error(error);
                });
        },
        getTiposList() {
            const vm = this;
            this.axios.get(this.baseUrl + "/tipos")
                .then(function (response) {
                    vm.tipoList = response.data;
                })
                .catch(function (error) {
                    console.error(error);
                });
        },
        edit(item) {
            console.log(item);
            this.itemToEdit = Object.assign({}, item);
            this.showModalEdit = true;
        },
        Eliminar(id) {
            if (confirm("¿Esta Seguro de eliminar el registro?")) {
                const vm = this;
                this.axios.delete(this.baseUrl + "/ganados/" + id)
                    .then(function (response) {
                        vm.getList();
                        vm.$toast.show("Registro eliminado.", "danger");
                    })
                    .catch(function (error) {
                        console.error(error);
                    });
            }

        },
        buscar() {
            this.getList();
        },
        filtrar() {
            this.textToFilter = '';
            if (this.filter.fecha != null && this.filter.fecha != '') {
                this.textToFilter += "&fecha=" + this.filter.fecha;
            }
            if (this.filter.tipoId != null && this.filter.tipoId != '') {
                this.textToFilter += "&tipoId=" + this.filter.tipoId;
            }
            this.getList();
        },
        onRegister(event) {
            this.getList();
            this.showModalNuevo = false;
            this.$toast.show('Registro exitoso', 'success');
        },
        onUpdate(event) {
            this.getList();
            this.showModalEdit = false;
            this.itemToEdit = null;
            this.$toast.show('Edicion exitosa', 'info');
        },
        showToast(message, type) {
            this.$toast.show(message, type);
        }
    },
    computed: {
        // propiedades computadas que dependen de otras propiedades reactivas
        ...mapState(['count']),
        ...mapGetters(['doubleCount', 'getBaseUrl']),
        baseUrl() {
            return this.getBaseUrl
        }
    },
    props: {
        // propiedades que el componente puede recibir.
    },
    mounted() {
        this.getList();
        this.getTiposList();
    },
    emits: [] // los eventos personalizados que el componente puede emitir.
}
</script>
  
<style></style>