<template>
    <div class="container">
      
      <div class="row">
        <div>
          <div class="col-12">
             <b-button 
              pill 
              variant="outline-secondary" 
              style="float:right" 
            @click="createAuthor()">Crear autor</b-button>
          </div>
         

          <table class="table tabla-autores">
            <thead>
              <tr>
                <td>Nombres</td>
                <td>Apellidos</td>
                <td>Fecha Nacimiento</td>
                <td colspan="2">Opciones</td>
              </tr>
              </thead>
              <tbody>
                <tr v-for="(autor, index) in autores">
                    <td>{{ autor.nombres }}</td>
                    <td>{{ autor.apellidos }}</td>
                    <td>{{ autor.fechaNacimiento }}</td>
                    <b-button 
                    pill
                    variant="outline-dark"
                    v-on:click="editarAutor(autor)">Editar</b-button>
                    <b-button 
                    pill  
                    variant="outline-danger"
                    v-on:click="mostrarMensaje(autor.id)">Eliminar</b-button>
                </tr>
              </tbody>
          </table>
        </div>
      </div>

      <b-modal id="modal-crearAutor" hide-footer>
        <template slot="modal-title">
          Nuevo autor
        </template>
        <div class="d-block text-center">
          <crearAutor 
          v-on:actualizar="ConsultarAutores()">
          </crearAutor>
        </div>
        <!-- <b-button class="mt-3" block @click="$bvModal.hide('bv-modal-example')">Close Me</b-button> -->
      </b-modal>

      <b-modal id="modal-editarAutor" hide-footer>
        <template slot="modal-title">
          Editar autor
        </template>
        <div class="d-block text-center">
          <editarAutor
           :autor="autor"
           v-on:actualizar="ConsultarAutores()">
            
          </editarAutor>
        </div>
        <!-- <b-button class="mt-3" block @click="$bvModal.hide('bv-modal-example')">Close Me</b-button> -->
      </b-modal>
        
    </div>
</template>

<script>
import axios from 'axios'
import crearAutor from './crearAutor'
import editarAutor from './editarAutor'
import configService from '../config/config.js'

export default {
  name: 'autor',
  components: { crearAutor, editarAutor },
  data () {
    return {
      eliminar: '',
      token: 'Bearer ',
      info: null,
      autores: [],
      autor: null
    }
  },
  mounted () {
    axios
      .get(`${configService.baseUrl}seguridad/token?nombreUnico=1014212205&usuario=ADMIN`)
      .then(response => {
        this.token += response.data.tokenCadena
        this.ConsultarAutores()
      }) 
  },
  created () {
    
  },
  methods:
  {
    createAuthor (id) {
      this.$bvModal.show('modal-crearAutor')
    },
    ConsultarAutores(){
      this.autores = []
      
      axios.defaults.headers.common['Authorization'] = this.token;
      axios
      .get(`${configService.baseUrl}autores`)
      .then(response => {
        this.autores = response.data
        console.log('response', response.data )
      })
    },
    editarAutor(autor){
      this.autor = autor
      this.$bvModal.show('modal-editarAutor')
    },
    mostrarMensaje(id){
      console.log('idautor',id)
      this.eliminar = ''
        this.$bvModal.msgBoxConfirm('¿Esta seguro?')
          .then(value => {
            this.eliminar = value

            if(this.eliminar)
              this.eliminarAutor(id);
          })
          .catch(err => {
            // An error occurred
          })
    },
    eliminarAutor(id){

      axios.defaults.headers.common['Authorization'] = this.token;
      axios
      .delete(`${configService.baseUrl}autores/${id}`)
      .then(response => {
        this.ConsultarAutores();
      });
    }
  }
}
</script>

