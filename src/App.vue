<template>
  <v-app id="inspire">
    <SplashScreen :isLoading="isLoading" />
    <v-app-bar
      app
      color="#8000FF"
      flat
      dark
    >
      <v-container fluid class="py-0 fill-height" v-if="!isLoading">
        <!-- <v-avatar
          class="mr-10"
          color="grey darken-1"
          size="32"
        >
          <img src="../public/img/icons/favicon.png">
        </v-avatar> -->
        <!-- <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon> -->
        <img src="../public/img/brand/logo-h-04.png" height="20px">
        <v-btn
          v-for="link in departamentos"
          :key="link"
          text
          class="mx-2"
        >
          {{ link }}
        </v-btn>
        <!-- <h3> Vulcano </h3> -->
        <v-spacer></v-spacer>

          <v-btn
            class="m-3 report-btn"
            dark
            small
            flat
            color="#6900E2"
            href= 'https://forms.gle/ftyfKTrKgqJG9MKr8'
            target='_blank'
          > <span class="mr-2 report-text"> Reportar erro </span> 
          <v-icon dark small >
            mdi-message-alert
          </v-icon>
        </v-btn>
        <v-dialog
          v-model="dialog"
          width="600px"
        >
          <template v-slot:activator="{ on, attrs }">
              <v-badge
                bordered
                color="green"
                icon="mdi-update"
                overlap
                v-if="updateDialog"
              >
                <v-btn
                  class="ml-2 changelog-btn"
                  v-bind="attrs"
                  v-on="on"
                  dark
                  small
                  color="#6900E2"
                >
                  <v-icon dark small>
                    mdi-package-variant-closed
                  </v-icon>
                </v-btn>
              </v-badge>
              
          </template>
          <v-card>
            <v-card-title>
              <h2>v0.3.6-beta</h2>
            </v-card-title>
            <v-divider> </v-divider>
            <v-card-text class="mt-3">
                <h2> Últimas atualizações </h2>
                  <h3> 0.3.6-beta - 06/02/2023 </h3>
                  
                <h4> Novo </h4>
                <ul>
                  <li> Removido filtro de auxiliar </li>
                  <li> Configurado listagem de erros reportados para listar todos os erros sem considerar filtro ativo </li>
                  <li> Corrigido posição da barra de status quando o modal do log de alterações era aberto </li>
                </ul>
                <v-divider> </v-divider>
                </v-card-text>
          </v-card>
          <v-card>
            <v-card-title>
              <h2>v0.3.5-beta</h2>
            </v-card-title>
            <v-divider> </v-divider>
            <v-card-text class="mt-3">
                <h2> Últimas atualizações </h2>
                  <h3> 0.3.5-beta - 06/02/2023 </h3>
                  
                <h4> Hotfix </h4>
                <ul>
                  <li> Adicionado suporte para imagens de analistas novos (Roberto e Priscila) </li>
                </ul>
                <v-divider> </v-divider>
                </v-card-text>
          </v-card>
          <v-card>
            <v-card-title>
              <h2>v0.3.4-beta</h2>
            </v-card-title>
            <v-divider> </v-divider>
            <v-card-text class="mt-3">
                <h2> Últimas atualizações </h2>
                  <h3> 0.3.4-beta - 01/01/2023 </h3>
                  <small> Feliz ano novo 🎊 </small>
                <h4> Novo </h4>
                <ul>
                  <li> Adicionado splash screen com logo animada </li>
                </ul>
                <v-divider> </v-divider>
                </v-card-text>
          </v-card>
          <v-card>
            <v-card-title>
              <h2>v0.3.3-beta</h2>
            </v-card-title>
            <v-divider> </v-divider>
            <v-card-text class="mt-3">
                <h2> Últimas atualizações </h2>
                  <h3> 0.3.3-beta - 01/09/2022 </h3>
                <h4> Novo </h4>
                <ul>
                  <li> Atualização de identidade visual </li>
                </ul>
                <v-divider> </v-divider>
                </v-card-text>
          </v-card>
          <v-card>
            <v-card-title>
              <h2>v0.3.2-beta</h2>
            </v-card-title>
            <v-divider> </v-divider>
            <v-card-text class="mt-3">
                <h2> Últimas atualizações </h2>
                  <h3> 0.3.2-beta - 28/07/2022 </h3>
                <h4> Corrigido </h4>
                <ul>
                  <li> Ajustes de responsividade para exibição mobile </li>
                </ul>
                <v-divider> </v-divider>
                </v-card-text>
          </v-card>
          <v-card>
            <v-card-title>
              <h2>v0.3.1-beta</h2>
            </v-card-title>
            <v-divider> </v-divider>
            <v-card-text class="mt-3">
                <h2> Últimas atualizações </h2>
                  <h3> 0.3.1-beta - 11/07/2022 </h3>
                  <h4> Novo </h4>
                <ul>
                  <li> Adicionado nome do analista ao lado do avatar na tabela e no card de cliente </li>
                  <li> Adicionado botão de reportar problemas </li>
                  <li> Adicionado botão de rolar para o topo </li>
                  <li> Adicionado link do cliente no botão da tabela </li>
                  <li> Adicionado botão de link do cliente no card e no título do cliente </li>
                  <li> Adicionado informações de dias de tolerância por cliente na tabela e no card </li>
                  <li> Reformulado visual dos cards </li>
                  <li> Adicionado horário da última atualização </li>
                  <li> Adicionado dialog com últimas atualizações da versão </li>
                </ul>

                <h4> Corrigido </h4>
                <ul>
                  <li> Data de última conversão da tag estava mostrando um horário vazio </li>
                </ul>
                <h4> Removido </h4>
                <ul>
                  <li> Botão de notificar Web Analytics </li>
                </ul>
                <v-divider> </v-divider>
                </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="green darken-1"
                text
                @click="dialog = false"
              >
                Fechar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-container>
    </v-app-bar>
    <!-- <v-navigation-drawer
                v-model="drawer"
                absolute
                temporary
                class="left-drawer"
              >
              <v-sheet rounded="lg" class="left-nav">
              <div class="project-avatar">
                <v-avatar
                class="mr-10"
                color="grey darken-1"
                size="64"
              > <img src="../public/img/icons/favicon.png"> </v-avatar>
                
              </div>
              <v-list color="transparent" class="d-flex flex-column justify-space-between">
                <v-list-item
                  v-for="view in views"
                  :key="view.name"
                  link
                  class="menu-link"
                >
                  <v-list-item-content>
                    <v-list-item-title>
                      <v-icon
                        color="#4CAF50">
                          {{ view.icon }}
                      </v-icon> 
                      {{ view.name }}
                    </v-list-item-title>
                  </v-list-item-content>
                </v-list-item>

                <v-list-item
                  link
                  color="grey lighten-4"
                >
                  <v-list-item-content>
                    <v-list-item-title>
                      <v-icon
                        color="green">
                          mdi-reload
                      </v-icon> 
                      Atualizar
                    </v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
              </v-list>
            </v-sheet>
             </v-navigation-drawer> -->
    <v-main>
      <v-container fluid>
        <v-row>
          <v-col>
            <v-sheet
              min-height="90vh"
              rounded="lg"
            > 
             <router-view>  </router-view>
              <!--  -->
            </v-sheet>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
    <div class="version p1">
      <p class="text-right"> 
        <small> project-jaguatirica | v0.3.6-beta </small> 
        </p>
    </div>
    
    <div class="scroll-top">
      <v-btn
        class="m-3 scroll-top-btn"
        fab
        small
        @click="scrollTop"
      >
      <v-icon dark>
        mdi-chevron-up
      </v-icon>
    </v-btn>
    </div>
  </v-app>
</template>

<script>

  import SplashScreen from "./components/SplashScreen";
  
  export default {
    data () {
      return { 
        departamentos: [
        'Resumo'
        ],
        isLoading: true,
        views: [
          {
            name: 'Tags',
            icon: 'mdi-tag'
          }
        ],
        drawer: false,
        dialog: false,

        updateDialog: true
      }
    },
    methods: {
      scrollTop(){
        document.body.scrollTop = 0;
        document.documentElement.scrollTop = 0;
      },

    },
    components:{
      SplashScreen
    },
    mounted() {
      setTimeout(() => {
        this.isLoading = false;
      }, 5000);
    }
  }
</script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@100;200;300;400;500;600;700;800;900&display=swap');
* {
  scrollbar-width: thin;
  scrollbar-color: rgb(226, 226, 226) white;
  font-family: 'Inter', sans-serif;
  scroll-behavior: smooth;
}
a{
  text-decoration: none;
}
h1, h2, h3, h4, h5{
  /* font-family: 'Inconsolata', monospace; */
}

*::-webkit-scrollbar {
  width: 10px;  
  height: 4px;            /* width of the entire scrollbar */
}

*::-webkit-scrollbar-track {
  background: white;       /* color of the tracking area */
}

*::-webkit-scrollbar-thumb {
  background: rgb(218, 218, 218);    /* color of the scroll thumb */
  border-radius: 20px;       /* roundness /* creates padding around scroll thumb */
}
.v-application--wrap{
  max-width: inherit !important;
}
.left-drawer{
  background: white !important;
}
.left-nav{
  /* position: sticky !important; */
  /* top: 70px !important; */
  color: white;
  background: transparent !important;
}
.menu-link{
  color: rgb(58, 58, 58) !important;
  font-size: 8px !important;
}
.scroll-top{
  padding: 5px;
  position: fixed !important;
  right: 10px;
  bottom: 5px;
}
.report-btn, .changelog-btn{
  box-shadow: none !important;
}
@media(max-width: 470px){
  .report-btn .report-text{
    display: none;
  }
}
.v-card-title h2{
  font-size: 1em;
}
</style>