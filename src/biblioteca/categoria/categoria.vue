<template>
    <div class="container">
     
      <div class="row">
        <div>
          <div class="col-12">
            <b-button 
            pill 
            variant="outline-secondary"
            style="float:right"
            @click="crearCategoria()">Crear categoria</b-button>
          </div>
          
         
          <table class="table tabla-categorias">
            <thead>
              <tr>
                <td>Nombre</td>
                <td>Descripcion</td>
                <td colspan="2">Opciones</td>
              </tr>
              </thead>
              <tbody>
                <tr v-for="categoria in categorias">
                    <td>{{ categoria.nombre }}</td>
                    <td>{{ categoria.descripcion }}</td>
                    <b-button 
                    pill
                    variant="outline-dark"
                    @click="editarCategoria(categoria)">Editar</b-button>
                    <b-button 
                    pill
                    variant="outline-danger"
                    v-on:click="mostrarMensaje(categoria.id)">Eliminar</b-button>
                </tr>
              </tbody>
          </table>
        </div>
      </div>
        
      <b-modal id="modal-crearCategoria" hide-footer>
        <template slot="modal-title">
          Nueva categoria
        </template>
        <div class="d-block text-center">
          <crearCategoria
          v-on:actualizar="ConsultarCategorias()">
          </crearCategoria>
        </div>
      </b-modal>

      <b-modal id="modal-editarCategoria" hide-footer>
        <template slot="modal-title">
          Editar categoria
        </template>
        <div class="d-block text-center">
          <editarCategoria 
          :categoria="categoria"
          v-on:actualizar="ConsultarCategorias()">
          </editarCategoria>
        </div>
      </b-modal>

    </div>
</template>

<script>
import axios from 'axios'
import crearCategoria from './crearCategoria'
import editarCategoria from './editarCategoria'
import configService from '../config/config.js'

export default {
  name: 'categoria',
  components: { crearCategoria, editarCategoria },
  data () {
    return {
      eliminar: '',
      token: 'Bearer ',
      info: null,
      categorias: [],
      categoria: null
    }
  },
  mounted () {
    axios
      .get(`${configService.baseUrl}seguridad/token?nombreUnico=1014212205&usuario=ADMIN`)
      .then(response => {
        this.token += response.data.tokenCadena
        this.ConsultarCategorias()
      }) 
  },
  methods:
  {
    crearCategoria(){
      this.$bvModal.show('modal-crearCategoria')
    },
    eliminarCategoria(id){
      axios.defaults.headers.common['Authorization'] = this.token;
      axios
      .delete(`${configService.baseUrl}categorias/${id}`)
      .then(response => {
        this.ConsultarCategorias()
      })
    },
    editarCategoria(categoria){
      this.categoria = categoria
      this.$bvModal.show('modal-editarCategoria')
    },
    mostrarMensaje(id){
      this.eliminar = ''
        this.$bvModal.msgBoxConfirm('¿Esta seguro?')
          .then(value => {
            this.eliminar = value
            console.log(this.eliminar)

            if(this.eliminar)
              this.eliminarCategoria(id);
          })
          .catch(err => {
            // An error occurred
          })
    },
    ConsultarCategorias(){
      this.categorias = []
      axios.defaults.headers.common['Authorization'] = this.token;
      axios
      .get(`${configService.baseUrl}categorias`)
      .then(response => {
        this.categorias = response.data
        console.log('response', response.data )
      })
    }
  }
}
</script>
<style lang="scss">
.tabla-categorias{
  margin-top: 10px;
}
</style>


