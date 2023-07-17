<template>
  <v-container>
    <v-row class="status-bar" :class="{ 'status-bar--hidden': !showStatusBar}"> 
      <!-- <v-col> <h4> Resumo</h4> </v-col> -->
      <v-col>
        <v-icon
            color="green"
            class="status-bar-icon"
          >
          mdi-check
        </v-icon>
         <b> {{noErrorCount}} </b> <small @click="filterByStatus('true')"> em funcionamento  </small>   
      </v-col>
      <v-col>
        <v-icon
            color="orange"
            class="status-bar-icon"
          >
          mdi-alert
        </v-icon>
        <b> {{alertCount}} </b> <small @click="filterByStatus('alert')"> em alerta  </small>   
      </v-col>
      <v-col>
        <v-icon
            color="red"
            class="status-bar-icon"
          >
           mdi-close-circle-outline
        </v-icon>
         <b> {{errorCount}} </b>  <small @click="filterByStatus('false')">com erro  </small>   
      </v-col>
    </v-row>
    <v-row class="d-flex flex-row content-row">
      <v-col class="col-sm-12 col-md-9">
        <h2> Resumo de clientes </h2> 
        
        <div class="tags-overview">
          <overview-card :class="'tag-success'" :type="'success'" :title="'Em funcionamento'" :value="noErrorCount" :link="'teste'" @click.native="filterByStatus('true')"/>

          <overview-card :class="'tag-warning'" :type="'warning'" :title="'Em alerta'" :value="alertCount" :link="'teste'" @click.native="filterByStatus('alert')"/>

          <overview-card :class="'tag-error'" :type="'error'" :title="'Com erro'" :value="errorCount" :link="'teste'" @click.native="filterByStatus('false')" />
        </div>
        <small> Atualizado em: {{ updatedAt }} </small>
        <template>
          <div class="action-buttons d-flex justify-end align-center">
            <v-btn-toggle
              v-model="activeLayout"
              mandatory
            >
              <v-btn
                outlined
                small
                rounded
              >
                <v-icon
                  small
                >
                  mdi-view-grid
                </v-icon>
              </v-btn>
              <v-btn
                outlined
                small
                rounded
              >
                <v-icon small >mdi-format-list-bulleted</v-icon>
              </v-btn>
            </v-btn-toggle>
            <v-text-field
                class="search-field"
                v-model="search"
                append-icon="mdi-magnify"
                label="Filtrar por cliente"
                flat
                hide-details
                rounded
                :loading="filterLoading"
                @click:clear="() => clientsFiltered = clients"
                background-color="#F6F6F6"  
              ></v-text-field>
              <template> 
              <div class="filters-buttons d-flex align-center">
                <div class="analyst-list" v-for="analyst in analystsList" :key="analyst.responsavel">
                  <v-btn
                      class="ma-1 analyst-filter-button"
                      outlined
                      small
                      fab
                      @click="filterByAnalist(analyst.responsavel)"
                      >
                        <img :src=analyst.responsavelURL class="analyst-filter-avatar" :alt=analyst.responsavel :title=analyst.responsavel> 
                  </v-btn>
                  <!-- <v-tooltip bottom>
                  <template v-slot:activator="{ on, attrs }">
                    <v-btn
                      :loading="waNotifyLoading"
                      class="ma-2"
                      outlined
                      small
                      v-bind="attrs"
                      v-on="on"
                      color="#a8a8a8"
                      @click="notifyWebAnalytics"
                    >
                      <a :href="item.link"> <v-icon color="#a8a8a8" size="16px">mdi-bell-ring</v-icon> </a>
                    </v-btn>
                  </template>
                  <span>Notificar Web Analytics</span>
                </v-tooltip> -->
                </div>
              </div>
            </template>
          </div>
        </template>
        <div class="active-filters-container" v-if="filteringBy.length > 0">
            <small> <b> Filtrando por: </b> 
            <template v-for="filter in filteringBy">
              <v-chip 
                :key="filter.name"
                close
                @click:close="removeFilter(filter.name)"
                > 
                {{filter.name}}: {{filter.value}} 
              </v-chip>  
            </template>
          </small>
        </div>
        
        <div class="card-container" ref="clientsCards" v-if="activeLayout=='0'">
          <div class="card-item" v-for="client in clientsFiltered" :key="client.name">
            <report-card :class="{ 'has-error': client.tagStatus==='false' }" :title="client.name" :link="client.link" :tagId="client.tagId" :tagStatus="client.tagStatus" :tags="client.tags" :analyst="client.responsavel" :analystURL='client.responsavelURL' :tolerance="client.tolerancia" />
          </div>
        </div>
        
        <div class="table-container" ref="clientsTable" v-if="activeLayout=='1'">
          <v-card>
            <v-card-title>
            </v-card-title>

            <v-data-table
              :headers="headers"
              :items="clientsFiltered"
              :search="search"
              :expanded.sync="expanded"
              item-key="name"
              fixed-header
              dense
              show-expand
              :single-expand="singleExpand"
              :loading="tableLoading"
              loadingText='Carregando...'
              no-data-text="Nenhum resultado encontrado"
            >
              <template v-slot:[`item.tagStatus`]="{ item }">
                <v-chip
                  v-if="item.tagStatus==='alert'"
                  color="orange"
                  class="ma-2"
                  label
                  text-color="white"
                >
                  Alerta
                </v-chip>    
                <v-chip
                  v-if="item.tagStatus==='false'"
                  color="red"
                  class="ma-2"
                  label
                  text-color="white"
                >
                  Erro
                </v-chip>  
                <v-chip
                  v-if="item.tagStatus==='true'"
                  color="green"
                  class="ma-2"
                  label
                  text-color="white"
                >
                  Ok
                </v-chip>  

              </template>
              <template v-slot:expanded-item="{ headers, item }">
                <td class="table-slot" :colspan="headers.length">
                  <v-container>
                    <v-row>
                      <v-col> 
                        <p class="mb-0"> <b> Cliente: </b> </p> <span> {{item.name }} </span> <br> 
                        <p class="mb-0 mt-2"> <b> Dias de tolerância: </b> </p> <span> {{item.tolerancia}} dias </span> <br>
                        <p class="mb-0 mt-2"> <b> ID: </b> </p> <span> {{item.tagId}} </span> <br>
                      </v-col>
                      <v-col>
                      <div class="table-error-tags">
                      <p> Tags com erro </p>
                        <div v-if="item.tagStatus === 'false'">
                          <template v-for="tag in item.tags" >
                            <template v-if="tag.status==='false'"> 
                              <div class="tag-check-item tag-table-error" :key="tag.name" > 
                                <v-icon
                                  color="white"
                                  small>
                                    mdi-close-circle-outline
                                </v-icon> 
                                <small>  {{ tag.name }}  </small>
                                <br>
                                <small v-if="tag.lastConversion"> Última conversão em: {{tag.lastConversion}} </small>
                                <small v-else> Sem dados de ultima conversão </small>
                              </div> 
                            </template>
                          </template>
                        </div>
                        <div v-else> Nenhum erro reportado </div>
                      </div>
                      </v-col>
                      <!-- <v-col cols="4">
                        <div class="table-alert-tags">
                          <p> Tags em alerta </p>
                          <div v-for="tag in item.tags" :key="tag.name">
                            <div class="tag-check-item tag-table-alert" v-if="tag.status==='alert'"> 
                              <v-icon
                                color="white"
                                small>
                                  mdi-check
                              </v-icon> 
                              <small>  {{ tag.name }} - {{tag.status}} </small>
                            </div> 
                          </div>
                        </div>
                      </v-col> -->
                      <v-col>
                        <div class="table-success-tags">
                          <p> Tags em funcionamento </p>
                          <div v-for="tag in item.tags" :key="tag.name">
                            <div class="tag-check-item tag-table-success" v-if="tag.status==='true'"> 
                              <v-icon
                                color="green"
                                small>
                                  mdi-check
                              </v-icon> 
                              <small>  {{ tag.name }} </small>
                            </div> 
                          </div>
                        </div>
                      </v-col>
                    </v-row>
                  </v-container>
                </td>
              </template>
              <template v-slot:[`item.responsavel`]="{ item }">
                 <v-tooltip right>
                  <template v-slot:activator="{ on, attrs }">
                    <img :src=item.responsavelURL :title=item.responsavel class="analyst-avatar" v-bind="attrs"
                      v-on="on"> 
                  </template>
                  <span> {{item.responsavel}} </span>
                </v-tooltip>
              </template>
              <template v-slot:[`item.link`]="{ item }">
                <v-btn
                    class="ma-0"
                    outlined
                    small
                    color="#a8a8a8"
                    >
                    <a :href="item.link" target="_blank" alt="abrir cliente no analytics"> <v-icon color="#a8a8a8" size="16px">mdi-open-in-new</v-icon> </a>
                    
                </v-btn>
                <!-- <v-tooltip bottom>
                  <template v-slot:activator="{ on, attrs }">
                    <v-btn
                      :loading="waNotifyLoading"
                      class="ma-2"
                      outlined
                      small
                      v-bind="attrs"
                      v-on="on"
                      color="#a8a8a8"
                      @click="notifyWebAnalytics"
                    >
                    <a :href="item.link"> <v-icon color="#a8a8a8" size="16px">mdi-bell-ring</v-icon> </a>
                    
                </v-btn>
                  </template>
                  <span>Notificar Web Analytics</span>
                </v-tooltip> -->
              </template>
              
            </v-data-table>
          </v-card>
        </div>

      </v-col>
      
      <v-col class="sidebar-tags col-sm-12 col-md-3"> 
      <div class="tags-title">
          <v-badge
              color="red"
              :content="errorCount"
            >
            <h3> Erros reportados </h3>
          </v-badge>
          <p> Clientes com tags em erro </p>
      </div>
      <!-- <div class="assistant-select">
          <v-select
            v-model="selectedAssistant"
            :items="assistants"
            menu-props="auto"
            label="Filtrar por auxiliar"
            background-color="#F2F2F2"
            outlined
            @input="filterByAssistant()"
          ></v-select>
        </div> -->
        <div class="tags-container">
          <div class="tag-item" v-for="client in clients" :key="client.name">
            <tag-monitor-card :title="client.name" :link="client.link" :tag="client.tagStatus" :tags="client.tags" v-if="client.tagStatus ==='false' || !client.tagStatus" />
          </div>
          <v-alert
                type="success"
                text
                max-width="210px"
                v-if="errorCount == 0"
                >
                  <small> Nenhum erro reportado </small>
          </v-alert>
        </div>
          
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  import ReportCard from '../components/ReportCard.vue'
  import TagMonitorCard from '../components/TagMonitorCard.vue'
  import OverviewCard from '../components/OverviewCard.vue'

  import axios from 'axios'
  import VueToastify from 'vue-toastify'
  import Vue from 'vue'

  Vue.use(VueToastify, {
    
  })


  export default {
    name: 'HomeView',

    components: {
      ReportCard,
      TagMonitorCard,
      OverviewCard
    },

    data() {
      return {
        apiURL: '//',
        search: '',
        noError: false,
        activeLayout: 1,
        expanded: [],
        singleExpand: true,
        selectedAssistant: '',

        tableLoading: false,
        filterLoading: false,
        filteringBy: [],
        waNotifyLoading: false,
        updatedAt: 'Carregando...',

        showStatusBar: false,

        assistants: ['Roberto', 'Priscila'],
        headers: [ 
          {
            text: 'Status',
            align: 'center',
            filterable: true,
            value: 'tagStatus'
          },
          {
            text: 'Cliente',
            align: 'start',
            filterable: true,
            value: 'name'
          },
    
          {
            text: 'Analista',
            value: 'responsavel'
          },
          {
            text: 'Ações',
            value: 'link'
          },
        ],

        clientsFiltered: [],

        clientsFilteredByAssistant: [],

        clients: [],

        clients2: [
          {
            name: 'Probel',
            tagId: 2919043305,
            tagStatus: 'alert',
            tags:[  
              {
                name: 'ND - Transactions',
                status: 'true'
              },
              {
                name: 'ND - Compra Concluída',
                status: 'alert'
              }
            ],
            value: '5.000,00',
            link: "#"
          },
          {
            name: 'Aquario',
            tagId: 2919043322,
            tagStatus: 'false',
            tags:[
              {
                name: 'Ligações',
                status: 'alert'
              },
              {
                name: 'ND - Compra Concluída',
                status: 'true'
              }
            ],
            value: '5.000,00',
            link: "#"
          },
          {
            name: 'Annexe',
            tagId: 2919043305,
            tagStatus: 'false',
            tags:[
              {
                name: 'CheckOut',
                status: 'true'
              },
              {
                name: 'Compra',
                status: 'true'
              },
              {
                name: "Whatsapp",
                status: 'true'
              },
              {
                name: "Adicionar ao carrinho",
                status: 'true'
              },
              {
                name: "ND - Compra concluída",
                status: 'true'
              },
              {
                name: "ND - Purchase GA4",
                status: 'true'
              }
            ],
            value: '15.000,00',
            link: "#"
          },
          {
            name: 'Atacado Vila Nova',
            tagId: 2919043305,
            tagStatus: 'false',
            tags:[
              {
                name: 'Transactions (1 - Vila Nova [MAIN])',
                status: 'true'
              },
              {
                name: 'Calls from ads',
                status: 'false'
              },
              {
                name: "Compra - Finalização [2021]",
                status: 'alert'
              }
            ],
            value: '8.000,00',
            link: "#"
          },
          {
            name: 'Brascol',
            tagId: 2919043305,
            tagStatus: 'false',
            tags:[
              {
                name: 'Transactions (GA)',
                status: 'false'
              },
              {
                name: 'Compra',
                status: 'false'
              },
              {
                name: "Adicionar ao carrinho",
                status: 'alert'
              },
              {
                name: "Cadastro",
                status: 'alert'
              }
            ],
            value: '8.000,00',
            link: "#"
          }
        ]
      }
    },
    created() {
      this.tableLoading = true
      
      axios.get(this.apiURL)
          .then(response => {
            this.clients = response.data.clientes
            this.clientsFiltered = this.clients
            this.tableLoading = false
            this.updatedAt = this.clients[0].data
          })
          .catch(()=>{
              this.$vToastify.error("Sua requisição está demorando mais do que o normal", "Algo não está certo...");
          })
    },
    mounted() {
      window.addEventListener('scroll', this.onScroll)
    },
    beforeDestroy() {
      window.removeEventListener('scroll', this.onScroll)
    },
    methods: {
      toggleLayout(){
        // this.$refs.clientsTable.style.display = "none"
        // this.activeLayout = this.activeLayout == 1 ? 2 : 1;
      },
      onScroll() {
        const currentScrollPosition = document.documentElement.scrollTop

        if (currentScrollPosition < 0){
          return
        }

        this.showStatusBar = currentScrollPosition > 200 ? true : false
      },
      notifyWebAnalytics(){
        // this.waNotifyLoading = true;
      },
      filterClients(filterTerm) { 
        this.filterLoading = true
        // this.filteringBy = [{'name':'Cliente', 'value':filterTerm}]
        let re = new RegExp(filterTerm, "i")
        this.clientsFiltered = this.clients.filter(function(e) { return e.name.match(re)}) 
        this.filterLoading = false
        this.clientsFiltered.length < 0 ? this.clients : this.clientsFiltered
      },
      filterByAnalist(analyst) {
        this.filterLoading = true
        this.filteringBy = [{'name': 'Analista', 'value':analyst}]
        let re = new RegExp(analyst, "i")
        this.clientsFiltered = this.clients.filter(function(e) { return e.responsavel.match(re)})
        this.filterLoading = false
      },
      filterByAssistant() {
        try {
          let assistant = this.selectedAssistant
          console.log(`Filtrando por ${assistant} ...`)
          this.filterLoading = true
          this.filteringBy = [ { 'name': 'Auxiliar', 'value' : assistant }]
          let re = new RegExp(assistant, "i")
          this.clientsFiltered = this.clients.filter(function(e) { return e.auxiliar.match(re)})
          this.filterLoading = false
          console.log(`filtro concluido por ${assistant} ...`)
        } catch (e){
          console.log(e)
        }
      },
      filterByStatus(status){
        let filtro 
        switch (status) {
          case 'true':
            filtro = 'Em funcionamento'
          break;
          case 'false':
            filtro = 'Com erro'
          break;
          case 'alert':
            filtro = 'Em alerta'
          break;
        }

        this.filterLoading = true
        
        this.filteringBy = [ { 'name': 'Status', 'value' : filtro}]
        
        this.clientsFiltered = this.clients.filter(function(e) { return e.tagStatus===status})
        
        this.filterLoading = false
      },
      removeFilter(filter){
        this.filteringBy = this.filteringBy.filter((e) => {
          return e.name != filter
        })
        this.clientsFiltered = this.clients
      },
      sortClients(a, b){
        console.log("ordenando clientes")
        if (a.tagStatus ==='false' && b.tagStatus ==='true'){
          return 1
        } else
        if (a.tagStatus ==='false' && b.tagStatus ==='alert'){
          return 1
        } else 
        if (a.tagStatus ==='alert' && b.tagStats ==='false'){
          return -1
        } else 
        if (a.tagStatus === 'alert' && b.tagStatus ==='true'){
          return 1
        } else 
        if (a.tagStatus ==='true' && b.tagStatus ==='false'){
          return -1
        } else 
        if (a.tagStatus === 'true' && b.tagStatus ==='alert'){
          return -1
        } else 
        if (a.tagStatus ==='true' && b.tagStatus ==='true'){
          return 0
        }
      }
    },
    computed: {
      // updatedAt() {
      //   // console.log("tipo: " + this.clients[0].data)
      //   const rawDate = this.clients[0].data;
      //   console.log("tipo: " + typeof(rawDate))
      //   // const formatedDate = rawDate.toISOString('pt-BR')
      //   // return formatedDate
      //   return rawDate
      // },
      errorCount() {
        const count = this.clients.filter((tag) => tag.tagStatus == "false").length
        document.title = count > 0 ? `(${count}) - Vulcano - Dashboard` : `Vulcano - Dashboard`
        return count
      },
      alertCount() {
        return this.clients.filter((tag) => tag.tagStatus == "alert").length
      },
      noErrorCount(){
        return this.clients.filter((tag) => tag.tagStatus == "true").length
      },
      analystsList(){
        /* Função que extrai os analistas recebidos no json, monta uma lista, 
        remove as duplicatas e retorna os analistas + url do avatar */
        
        let analysts = this.clients.map(( { responsavel, responsavelURL }) => ( { responsavel, responsavelURL })) 

        const setAnalyst = new Set()

        const filterAnalysts = analysts.filter((analyst) => {
          const duplicatedAnalyst = setAnalyst.has(analyst.responsavel)
          setAnalyst.add(analyst.responsavel)
          return !duplicatedAnalyst
        })

        return filterAnalysts
      },
      // assistantsList(){
      //   let assistants = this.clients.map( ( { auxiliar }) => ( { auxiliar }))
      //   let uniqueAssistants = [...new Set(assistants)]
      //   return uniqueAssistants
      // }
    },
    watch: {
      search(filterTerm){
        this.filterLoading = true
        console.log("filtrando por "+filterTerm)
        this.filterClients(filterTerm)
      }
    }
    
  }
</script>

<style scoped>
.main{
  padding-top: 25px !important;
}
a{
  text-decoration: none;
}
.main{
  padding: 50px;
}
@media (max-width: 960px) {
  .content-row{
    flex-direction: column-reverse !important;
  }
}
.tags-overview{
  display: flex;
  flex-wrap: wrap;
}
.tags-overview-item{
  margin: 0 10px 0 0;
}
.filters-buttons{
  padding: 5px;
  /* border: 1px solid rgb(245, 245, 245); */
  /* background: #F5F5F5; */
  border-radius: 4px;
}
.analyst-filter-title{
  margin: 0 5px 0 0;
}
.analyst-filter-button{
  margin: 1px !important;
  height: 30px;
  width: 30px;
}
.analyst-filter-avatar{
  height: 30px;
  width: 30px;
  border-radius: 50%; 
}
.action-buttons {
  padding: 35px 0 25px 0;
}
.action-buttons .search-field{
  padding: 0 5px 0 5px;
  margin-top: 0!important;
  border-radius: 5px !important;
}
.active-filters-container{
  padding: 5px 0 5px 10px;
}
.tags-container{
  /* position: relative; */
  padding: 15px 0 15px 0;
  display: flex;
  flex-wrap: wrap;
  flex-direction: column;
  /* overflow: scroll;
  overflow-y: hidden; */
}
.card-container{
  display: flex;
  flex-wrap: wrap;
}
.table-container .v-sheet{
  /* background: #F7F7F7 !important; */
}
.table-slot{
  padding: 25px !important;
}
.table-tags{
  width: 100%;
  align-items: center;
  justify-content: center;
  display: flex;
}
.analyst-filter-title{
  color: rgb(55, 55, 55) !important;
}
.analyst-avatar{
  height: 30px;
  width: 30px;
  border-radius: 50%;
}
.tag-check-item{
  padding: 5px;
  margin: 5px;
  border-radius: 4px;
  width: auto;
}
.tag-table-error{
  color: white;
  background: #FF5252;
}
.tag-table-success{
  background: #d9ffdf;
  color: #4caf50;
}
/* clients table */ 
.table-container .v-card, .v-card__title, .v-data-table {
  /* background: #F7F7F7!important; */
  border-radius: 15px;
  box-shadow: none !important;
  /* border-color: transparent !important; */
}
.table-container td, button {
  /* border: none !important; */
}
.table-container button{
  margin: 5px !important;
  padding: 5px !important;
}
.theme--light.v-data-table.v-data-table--fixed-header thead th {
  background: #f7f7f7 !important;
}
.v-data-table > .v-data-table__wrapper tbody tr.v-data-table__expanded__content{
  box-shadow: none !important;
}

.sidebar-tags{
  border-radius: 5px;
  padding: 25px 20px 25px 20px;
  background: #F2F2F2;
}
.tags-title h3{
  /* color: #ff4b4b; */
}
.tags-title p{
  color: #5f5f5f;
  font-size: 0.9rem;
}

.assistant-select{
  margin: 5px 0 5px 0;
  /* border-radius: 20px;
  padding: 5px;
  border: 1px solid rgb(194, 194, 194); */
}
.status-bar{
  /* width: 800px; */
  background: #eeeeee;
  position: sticky;
  border-radius: 15px;
  top: 68px;
  /* top: 0px; */
  z-index: 10;
  /* box-shadow: 5px 5px 15px 5px rgba(0,0,0,0.5); */
  /* display: none; */
  transition: all 0.2s ease;
}
.status-bar--hidden{
  z-index: 0;
  top: 50;
  opacity: 0;
  transition: all 0.2s ease;
}
.status-bar-icon{
  /* background: white; */
  border-radius: 50%;
  padding: 3px;
  margin-right: 5px;
}
.status-bar small{
  cursor: pointer;
}
@media(min-width: 1035px){
  .status-bar{
    width: 800px;
  }
}
@media(max-width: 450px){
  .status-bar small{
    display: none;
  }
}

/* clients table */ 


</style>
