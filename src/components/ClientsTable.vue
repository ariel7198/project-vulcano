<template>
    <v-data-table
              :headers="headers"
              :items="desserts"
              :items-per-page="5"
              class="elevation-1"
              item-key="name"
              :search="search"
              >
              <template v-slot:top>
        <v-text-field
          v-model="search"
          label="Search (UPPER CASE ONLY)"
          class="mx-4"
        ></v-text-field>
      </template>
              </v-data-table>
</template>

<script>
  export default {
    name: 'ClientsTable',

    data (){
        return {
            search: '',
            calories: '',
            headers: [
            {
                
            }
            ],
            
        }
    }
  }
</script>