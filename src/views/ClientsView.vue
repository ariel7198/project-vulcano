<template>
  <clients-table />
</template>

<script>
  import ClientsTable from '../components/ClientsTable'

  export default {
    name: 'HomeView',

    components: {
      ClientsTable,
    },
  }
</script>
