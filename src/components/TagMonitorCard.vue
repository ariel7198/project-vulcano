<template>
    <v-alert
      prominent
      dense
      type="error"
      icon="mdi-close-circle-outline"
      class="tag-error-alert"
    >
      <v-row align="center">
        <v-col class="grow">
        <h4> {{ title }}</h4>
        <v-spacer> </v-spacer>
        <v-container class="container-tags" v-for="tag in tags" :key="tag.name"> 
          
          <v-tooltip bottom>
                  <template v-slot:activator="{ on, attrs }">
                    <small v-if="tag.status==='false'" v-bind="attrs" v-on="on"> {{tag.name}} </small> 
                  </template>
                  <small v-if="tag.status==='false' && tag.lastConversion"> Ultima conversão em {{tag.lastConversion}} </small>
                  <small v-else> Sem dados de ultima conversão </small>
                </v-tooltip>
          
        </v-container>
        </v-col>
      </v-row>
    </v-alert>
</template>

<script>
  export default {

    props: ['title','status','link', 'tags'],

    name: 'TagMonitorCard',
  }
</script>

<style scoped>
.tag-error-alert{
  margin: 10px 10px 10px 0;
  border-color: transparent !important;
  width: auto;
  min-width: 150px;
  border-radius: 5px;   
  /* max-width: auto; */

}
.container-tags{
  margin: 0!important;
  padding: 0!important;
}
</style>
