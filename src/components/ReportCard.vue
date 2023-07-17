<template>
  <v-card
    class="client-card"
    max-width="344"
    outlined
  >
    <v-list-item three-line>
      <v-list-item-content>
        <div class="text-overline mb-1 d-flex justify-space-between align-center">
          <h2>
            <a :href=link target="_blank"> {{ title }}  </a>
            <v-icon
                v-if="tagStatus==='alert'"
                color="orange">
                  mdi-alert
            </v-icon>
            <v-icon
                v-if="tagStatus==='true'"
                color="green">
                  mdi-check-circle
            </v-icon>
            
            <v-icon
                v-if="tagStatus==='false'"
                color="red">
                  mdi-alert
            </v-icon>
          </h2> 
          <v-tooltip right>
                  <template v-slot:activator="{ on, attrs }">
                    <img :src=analystURL :title=analyst class="analyst-avatar" v-bind="attrs"
                      v-on="on"> 
                  </template>
                  <span> {{analyst}} </span>
                </v-tooltip>
          

        </div>
        <div class="client-info">
          <v-tooltip bottom>
                  <template v-slot:activator="{ on, attrs }">
                    <small class="tolerance" v-bind="attrs" v-on="on"> 🕗 Tolerância:  {{tolerance}} {{ tolerance > 1 ? "dias" : "dia" }}   </small> 
                  </template>
                  <span> Tolerância: Quantidade de dias sem conversão necessários para a tag ser considerada com erro</span>
                </v-tooltip>
          
          <!-- <small class="client-id"> #{{tagId}} </small>  -->
        </div>
        
        <v-divider> </v-divider>
        
      </v-list-item-content>

    </v-list-item>
    
    <v-list-item>
      <v-list-item-content>
        <div v-if="tagStatus==='false'"> 
          <h5> Problemas detectados </h5>
          <div class="tag-item" v-for="tag in tags" :key="tag.title">
            <v-alert 
              v-if="tag.status=='false'"
              type="error"
              icon="mdi-close-circle-outline"
              dense>
              <small> <b> {{ tag.name }} </b> </small>
              <br>
              <small v-if="tag.lastConversion"> Última conversão em: {{tag.lastConversion}} </small>
              <small v-else> Sem dados de ultima conversão </small>
            </v-alert>
          </div>
        </div>
          <v-alert
            dense
            outlined
            type="success"
            class="mb-4"
            v-if="tagStatus!='false'"
            >
              <small> Nenhum problema detectado </small>
          </v-alert>
        <v-divider> </v-divider>
      </v-list-item-content>
    </v-list-item>

    <!-- <v-list-item>
      <v-list-item-content>
        <h5> Alertas </h5>
        <div class="tag-item" v-for="tag in tags" :key="tag.title">
          <v-alert 
            v-if="tag.status=='alert'"
            type="warning"
            dense
            max-width="250px">
            <span> {{ tag.name }}  </span> <br> 
            <v-tooltip bottom>
                <template v-slot:activator="{ on, attrs }">
                  <small
                    v-bind="attrs"
                    v-on="on"
                  >
                    Sem conversões recentes
                    </small>
                </template>
                <span>Última conversão em: 22/05/22</span>
              </v-tooltip>
          </v-alert>
        </div>
        <v-divider> </v-divider>
      </v-list-item-content>
    </v-list-item> -->

    <v-list-item>
      <v-list-item-content>
        <h5> Tags em funcionamento </h5>
        <div class="tags-check-container">
          <div v-for="tag in tags" :key="tag.title">
            <div class="tag-check-item" v-if="tag.status==='true'"> 
              <v-icon
                color="green"
                small>
                  mdi-check
              </v-icon> 
              <small>  {{ tag.name }} </small>
            </div> 
          </div>
        </div>
        
        <v-divider> </v-divider>
      </v-list-item-content>
    </v-list-item>
    <div class="card-footer d-flex justify-end p-2">
      <v-btn
        class="ma-0"
        outlined
        small
        fab
        color="#a8a8a8"
        >
        <a :href="link" target="_blank" alt="abrir cliente no analytics"> <v-icon color="#a8a8a8" size="16px">mdi-open-in-new</v-icon> </a>            
      </v-btn>
      <!-- <v-tooltip bottom >
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  v-if="tagStatus==='false'"
                  class="ma-2 btn-notify"
                  small
                  fab
                  v-bind="attrs"
                  v-on="on"
                >
                <a href="#"> <v-icon color="red" size="16px">mdi-bell-ring</v-icon> </a>
                
                </v-btn>
              </template>
              <span>Notificar Web Analytics</span>
        </v-tooltip> -->
    </div>
  </v-card>
</template>

<script>
  export default {

    props: ['title', 'tolerance', 'link', 'tagId', 'tagStatus', 'tags','analyst','analystURL'],

    name: 'ReportCard',
  }
</script>

<style scoped>
a{
  text-decoration: none !important;
  color: black !important;
}
.btn-notify{
  background: transparent !important;
  box-shadow: none !important;
  border: 1px transparent !important; 
}
.client-card{
  min-width: 354px;
  /* border-radius: 20px !important; */
  margin: 15px 15px 15px 0;
  /* background: white !important; */
  background: #fafafa !important;
  /* border: 1px solid #f1f1f1 !important; */
  border-color: transparent !important;
  transition: all 0.2s ease;
  padding: 15px 10px 15px 10px;
  /* box-shadow: 12px 24px 28px 21px rgb(29 56 70 / 6%); */
}
.client-card:hover{
  transition: all 0.2s ease;
  box-shadow: 4px 7px 20px 2px rgb(0 0 0 / 10%);
}
.has-error{
 border: 2px solid rgb(255, 166, 166) !important;
}
.client-id{
  color:rgb(180, 180, 180);
}
.tolerance{
  color:rgb(85, 85, 85); 
}
.tags-check-container{
  display: flex;
  flex-wrap: wrap;
}
.tag-item{
  margin-top: 10px;
}
.tag-check-item{
  padding: 5px;
  margin: 5px;
  border-radius: 4px;
  background: #d9ffdf;
  color: #4caf50;
  width: auto;
}
.success-tag{
  color: white !important;
  /* background:#0F9D58 !important; */
}
.success-tag i{
  color: white;
}
.card-footer{
  padding: 5px 10px 5px 0;
}
.analyst-avatar{
  height: 30px;
  width: 30px;
  border-radius: 50%; 
}
</style>
