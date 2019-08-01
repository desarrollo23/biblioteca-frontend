<template>
    <div class="container">
        <div>
            <b-form>
            <b-form-group
                id="input-group-1"
                label="Nombre:"
                label-for="input-1">
                <b-form-input
                id="input-1"
                v-model="nombre"
                type="text"
                required
                ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-2" label="Descripción:" label-for="input-2">
                <b-form-input
                id="input-2"
                v-model="descripcion"
                required
                ></b-form-input>
            </b-form-group>
           
            <b-button 
            pill
            variant="outline-dark"
            v-on:click="guardarCategoria()">Guardar</b-button>
            </b-form>
  </div>
    </div>
</template>

<script>
import axios from 'axios'
import configService from '../config/config.js'
export default {
    name: 'editarCategoria',
    props: [ 
      'categoria'
     ],
  data () {
    return{
        token: 'Bearer ',
        descripcion: this.categoria.descripcion || null,
        nombre: this.categoria.nombre || null
    }
  },
  mounted () {
    axios
      .get(`${configService.baseUrl}seguridad/token?nombreUnico=1014212205&usuario=ADMIN`)
      .then(response => {
        this.token += response.data.tokenCadena
      }) 
  },
  methods: {
      guardarCategoria(){
      let _this = this
       axios.defaults.headers.common['Authorization'] = this.token;
        axios.put(`${configService.baseUrl}categorias/${this.categoria.id}`, {
            id: this.categoria.id,
            nombre: this.nombre,
            descripcion: this.descripcion
        })
        .then(function (response) {
          _this.$emit('actualizar', null)
          _this.$bvModal.hide('modal-editarCategoria')
        })
        .catch(function (error) {
            console.log(error);
        });

      }
  }
}
</script>
