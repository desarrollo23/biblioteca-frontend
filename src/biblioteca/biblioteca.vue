<template>
    <div>
      <div class="container">
        <h3>BIBLIOTECA</h3>
        <div class="row">
          <div class="col-3 bootnes">
            <b-button 
            variant="light"
            v-on:click="changeTab(1)">
              Autores
            </b-button>
            <b-button 
            variant="light"
            v-on:click="changeTab(2)">
              Categorias
            </b-button>
            <b-button 
            variant="light"
            v-on:click="changeTab(3)">
              Libros
            </b-button>
          </div>
          <div class="col-9">

            <div class="wrrp-tabs">
              <div class="tab" v-show="activeTab == 1">
                <autor></autor>
              </div>
              <div class="tab" v-show="activeTab == 2">
                <categoria></categoria>
              </div>
              <div class="tab" v-show="activeTab == 3">
                <libro></libro>
              </div>
            </div>
          </div>
        </div>
      </div>

    </div>
    
</template>

<script>
import autor from './autor/autor'
import categoria from './categoria/categoria'
import libro from './libro/libro'

export default {
  name: 'biblioteca',
  components: { autor, categoria, libro },
  data () {
    return {
      activeTab: 1
    }
  },
  methods: {
    changeTab (activeTab) {
      this.activeTab = activeTab
    }
  }
}
</script>

<style lang="scss">

  .bootnes {
    
  }

  .bootnes button {
    width: 100%;
    margin-top: 5px;
  }

  .wrrp-tabs {
    .tab {
      height: 300px;
      width: 100%;
      &:nth-child(1n) {
        //background: red;
      }
      &:nth-child(2n) {
        //background: blue;
      }
      &:nth-child(3n) {
        //background: green;
      }
    }
  }
</style>

