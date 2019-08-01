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

            <b-form-group id="input-group-2" label="Categoria:" label-for="input-2">
                <b-form-select 
                v-model="selectedCategoria" 
                :options="opcionesCategorias">
                </b-form-select>
            </b-form-group>

             <b-form-group
                id="input-group-1"
                label="Autor:"
                label-for="input-1">
                <b-form-input
                id="input-1"
                v-model="nombreAutor"
                type="text"
                :placeholder="placeholderAutor"
                autocomplete="off"
                ></b-form-input>
            </b-form-group>

            <div 
            class="item-author"
            v-for="(autor, index) in autorFiltrado"
            v-on:click="selccionarAutor(autor)">
             <b-card title="" sub-title="" class="card">
                <p>
                  {{ autor.nombres }} {{ autor.apellidos }}
                </p>
             </b-card>
            </div>
            <b-button 
            pill
            variant="outline-dark"
            v-on:click="guardarLibro()">Guardar</b-button>
            </b-form>
  </div>
    </div>
</template>

<script>
import axios from 'axios'
import configService from '../config/config.js'
export default {
    props: [ "libro" ],
    name: 'editarLibro',
  data () {
    return{
        token: 'Bearer ',
        nombreAutor: `${this.libro.autor.nombres} ${this.libro.autor.apellidos}` || null,
        nombre: this.libro.nombre || null,
        categoria: this.libro.categoria.id || null,
        autor: this.libro.autor.id || null,
        opcionesCategorias: [],
        opcionesAutores: [],
        categorias: [],
        autores: [],
        selectedCategoria: {},
        selectedAutor: {},
        autorFiltrado: [],
        placeholderAutor: 'Autor'
      }
  },
  mounted () {
    axios
      .get(`${configService.baseUrl}seguridad/token?nombreUnico=1014212205&usuario=ADMIN`)
      .then(response => {
        this.token += response.data.tokenCadena
        this.ConsultarCategorias()
        this.ConsultarAutores()
      }) 
  },
   watch: {
    nombreAutor (val) {
      console.log(val)
      this.getFilteredItems(val)
    }
  },
  methods: {
    selccionarAutor (autor) {
      this.autor = autor.id
      this.nombreAutor = ''
      this.placeholderAutor = `${autor.nombres} ${autor.apellidos}`
    },
      guardarLibro(){
        let _this = this
        axios.defaults.headers.common['Authorization'] = this.token;
        axios.put(`${configService.baseUrl}libros/${this.libro.id}`, {
            id: this.libro.id,
            nombre: this.nombre,
            categoriaId: this.selectedCategoria,
            autorId: this.autor
        })
        .then(function (response) {
            _this.$emit('actualizar', null)
            _this.$bvModal.hide('modal-editarLibro')
        })
        .catch(function (error) {
            console.log(error);
        });

        
      },
      ConsultarCategorias(){
      axios.defaults.headers.common['Authorization'] = this.token;
      axios
      .get(`${configService.baseUrl}categorias`)
      .then(response => {
        this.categorias = response.data
        console.log('response', response.data )
        this.categorias.forEach((element, index) => {
          var option = { value: element.id, text: element.nombre }
          this.opcionesCategorias.push(option)
        });
        this.selectedCategoria = this.libro.categoria.id
        this.selectedAutor = this.libro.autor.id
        let nameCategory = this.libro.categoria.nombre
      })
    },
    ConsultarAutores(){
      axios.defaults.headers.common['Authorization'] = this.token;
      axios
      .get(`${configService.baseUrl}autores`)
      .then(response => {
        this.autores = response.data
        console.log('response autores', response.data )

        this.autores.forEach(element => {
          var option = { value: element.id, text: `${element.nombres} ${element.apellidos}` }
          this.opcionesAutores.push(option)
        });
      })
    },
    getFilteredItems (match) {
      if (match.length < 3) {
        this.autorFiltrado = []
      } else {
        this.autorFiltrado = this.autores.filter((option) => {
          return option.nombres
            .toString()
            .toLowerCase()
            .indexOf(match.toLowerCase()) >= 0 || option.apellidos
            .toString()
            .toLowerCase()
            .indexOf(match.toLowerCase()) >= 0
        })
      }
    }
  }
}
</script>

<style lang="scss">

  .card{
    cursor: pointer;
    &:hover {
      background: #D1CDCC ;
    }
  }
</style>
