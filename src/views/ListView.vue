<template>
  <v-container class="d-flex justify-center">
    <v-card
    class="mx-auto"
    >
    <v-list >
      <v-list-item-group v-model="selectedItem" color="primary">
        <v-list-item v-for="item in valutes" :key="item.ID">
          <v-list-item-content class="flex-nowrap">
            <v-list-item-title>
              {{item.Name}}
            </v-list-item-title>
              {{item.Previous}}
          </v-list-item-content>
        </v-list-item>
      </v-list-item-group>
    </v-list>
    </v-card>
  </v-container>
</template>

<script>

const axios = require('axios').default;
  export default {
    data: () => ({
      selectedItem: 0,
      countries: [],
      valutes: {},
    }),

    mounted() {
    axios
      .get('https://www.cbr-xml-daily.ru/daily_json.js')
      .then(response => {
        this.valutes = response.data.Valute;
        for (this.code in response.data.Valute) {
          this.countries.push(this.code)
          console.log(this.countries)
        }
      })
      .catch(error => {
        console.log(error)
      })
  },
  }
</script>
