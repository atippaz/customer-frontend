<template>
      <v-container class="mt-12">
        <div class="d-flex justify-end ga-2">
            <v-btn color="success">import Data</v-btn>
            <v-btn color="primary" @click="dialog=true">create Data</v-btn>

        </div>
        <v-card class="mt-4">
          <v-data-table
          class="elevation-4"
            :items="items"
            hide-actions
            select-all
            pagination.sync="pagination"
            :loading="loading"
          >
            
          </v-data-table>
        </v-card>
        <v-dialog
          v-model="dialog"
          scrollable
          persistent
          location="center center"
          :overlay="false"
          max-width="500px"
        >
        <v-card >
          <v-card-title primary-title>
            title
          </v-card-title>
          <v-card-actions>
            <v-btn color="success">cancel</v-btn>
            <v-btn color="success">submit</v-btn>
          </v-card-actions>
        </v-card>
        </v-dialog>
      </v-container>
      <!-- <v-data-table
      headers="headers"
      items="items"
      hide-actions
      class="elevation-1"
      select-all
      pagination.sync="pagination"
      item-key="id"
      loading="true"
      search="search"
    >
    
    </v-data-table> -->
</template>

<script setup>
import HelloWorld from "@/components/HelloWorld.vue";
    import { ref } from "vue";
    const dialog = ref(false);
    const loading = ref(false)
    const items = [
    {
      name: 'African Elephant',
      species: 'Loxodonta africana',
      diet: 'Herbivore',
      habitat: 'Savanna, Forests',
    }];
    (() => {
  fetch("http://localhost:5255/")
    .then((e) => e.json())
    .then((e) => console.log(e))
    .catch((e) => console.log(e));
})();
</script>
