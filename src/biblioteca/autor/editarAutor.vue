<template>
    <div class="container">
        <div>
            <b-form>
            <b-form-group
                id="input-group-1"
                label="Nombres:"
                label-for="input-1">
                <b-form-input
                id="input-1"
                v-model="nombres"
                type="text"
                required
                ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-2" label="Apellidos:" label-for="input-2">
                <b-form-input
                id="input-2"
                v-model="apellidos"
                required
                ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-3" label="Fecha de nacimiento:" label-for="input-3">
                <b-form-input
                id="input-2"
                v-model="fechaNacimiento"
                required
                type="text"
                ></b-form-input>
            </b-form-group>
            <b-button 
            pill
            variant="outline-dark"
            v-on:click="guardarAutor">Guardar</b-button>
            </b-form>
  </div>
    </div>
</template>

<script>
import axios from 'axios'
import configService from '../config/config.js'
export default {
    name: 'editarAutor',
    props: [ "autor" ],
  data () {
      return{
          token: 'Bearer ',
          nombres: this.autor.nombres || null,
          apellidos: this.autor.apellidos || null,
          fechaNacimiento: this.autor.fechaNacimiento || null
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
      guardarAutor(){
        let _this = this
        axios.defaults.headers.common['Authorization'] = this.token;
        axios.put(`${configService.baseUrl}autores/${this.autor.id}`, {
            id: this.autor.id,
            nombres: this.nombres,
            apellidos: this.apellidos,
            fechaNacimiento: this.fechaNacimiento
        })
        .then(function (response) {
            _this.$emit('actualizar', null)
            _this.$bvModal.hide('modal-editarAutor')
        })
        .catch(function (error) {
            console.log(error);
        });
      }
  }
}
</script>
