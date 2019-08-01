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
                placeholder="Nombre"
                ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-2" label="Descripción:" label-for="input-2">
                <b-form-input
                id="input-2"
                v-model="descripcion"
                required
                placeholder="Descripción"
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
    name: 'crearCategoria',
  data () {
      return{
          nombre: '',
          descripcion: '',
          token: 'Bearer '
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
        axios.post(`${configService.baseUrl}categorias`, {
            nombre: this.nombre,
            descripcion: this.descripcion
        })
        .then(function (response) {
            _this.$emit('actualizar', null)
            _this.$bvModal.hide('modal-crearCategoria')
        })
        .catch(function (error) {
            console.log(error);
        });

        
      }
  }
}
</script>
