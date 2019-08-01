<template>
    <div class="container">
      
      <div class="row">
          <div class="col-6 search">
              <label for="buscar">Buscar:</label>
              <b-form-input 
              v-model="search" 
              placeholder="Libro, autor o categoria"
              @keyup.enter.native="buscarLibro"></b-form-input>
               
          </div>
          <div class="col-6 btn-div">
              <b-button 
                pill 
                variant="outline-secondary" 
                style="float:left" 
                class="btn-crearLibro"
                @click="crearLibro()">Crear libro</b-button>
          </div>
      </div>
     
      <div class="row tabla">
        <div>
          <table class="table tabla-libros">
            <thead>
              <tr>
                <td>Nombre</td>
                <td>Autor</td>
                <td>Categoria</td>
                <td colspan="2">Opciones</td>
              </tr>
              </thead>
              <tbody>
                <tr v-for="libro in libros">
                    <td>{{ libro.nombre }}</td>
                    <td>{{ libro.autor.nombres }} {{ libro.autor.apellidos }}</td>
                    <td>{{ libro.categoria.nombre }}</td>
                    <b-button 
                    pill 
                    variant="outline-dark"
                    v-on:click="editarLibro(libro)">Editar</b-button>
                    <b-button 
                    pill
                    variant="outline-danger"
                    v-on:click="mostrarMensaje(libro.id)">Eliminar</b-button>
                </tr>
              </tbody>
          </table>
        </div>
      </div>
        
          <b-modal id="modal-crearLibro" hide-footer>
            <template slot="modal-title">
              Crear libro
            </template>
            <div class="d-block text-center">
              <crearLibro
              v-on:actualizar="consultarLibros()"></crearLibro>
            </div>
          </b-modal>

          <b-modal id="modal-editarLibro" hide-footer>
            <template slot="modal-title">
              Editar libro
            </template>
            <div class="d-block text-center">
              <editarLibro 
              :libro="libro"
              v-on:actualizar="consultarLibros()"></editarLibro>
            </div>
          </b-modal>
    </div>
</template>

<script>
import axios from 'axios'
import crearLibro from './crearLibro'
import editarLibro from './editarLibro'
import configService from '../config/config.js'

export default {
  components: { crearLibro, editarLibro },
  name: 'libro',
  data () {
    return {
      eliminar: '',
      info: null,
      libros: [],
      libro: null,
      token: 'Bearer ',
      search: ''
    }
  },
  mounted () {
    axios
      .get(`${configService.baseUrl}seguridad/token?nombreUnico=1014212205&usuario=ADMIN`)
      .then(response => {
        this.token += response.data.tokenCadena
        this.consultarLibros()
      }) 
  },
  methods:
  {
    consultarLibros(){
      this.libros = []
      axios.defaults.headers.common['Authorization'] = this.token;
      axios
      .get(`${configService.baseUrl}libros`)
      .then(response => {
        this.libros = response.data
        console.log('response', response.data )
      })
    },
    crearLibro(){
      this.$bvModal.show('modal-crearLibro')
    },
    mostrarMensaje(id){
      this.eliminar = ''
        this.$bvModal.msgBoxConfirm('¿Esta seguro?')
          .then(value => {
            this.eliminar = value
            console.log(this.eliminar)

            if(this.eliminar)
              this.eliminarLibro(id);
          })
          .catch(err => {
            // An error occurred
          })
    },
    eliminarLibro(id){
      axios.defaults.headers.common['Authorization'] = this.token;
      axios
      .delete(`${configService.baseUrl}libros/${id}`)
      .then(response => {
        this.consultarLibros()
      })
    },
    editarLibro(libro){
      this.libro = libro
      this.$bvModal.show('modal-editarLibro')
    },
    buscarLibro(object){
      console.log('CHECKING ENTER')
      console.log(this.search)

       axios
      .get(`${configService.baseUrl}libros/buscarLibro?parametroBusqueda=${this.search}`)
      .then(response => {
        this.libros = response.data
      }) 
    }
  }
}
</script>

<style lang="scss">
.tabla{
  margin-top: 5px;
}
.btn-crearLibro{
  margin-top: 30px;
}

</style>

