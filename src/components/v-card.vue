<template>
  <v-app>
    <v-container>
      <v-row>
        <v-col cols="10" md="12" lg="9" xl="6" class="bg--light mx-auto">
          <h1 class="text-center mb-4">Конвектор валют</h1>
          <v-row justify="space-between" no-gutters>
            <v-col cols="12" md="5">
              <v-row no-gutters>
                <v-col class="px-1" cols="3">
                  <v-select @change="convert" v-model="selected[0]" :items="countries"></v-select>
                </v-col>

                <v-col class="px-1" cols="9">
                  <v-text-field @input="convert" v-model="inputed" :rules="[rules.onlyNumbers]"></v-text-field>
                </v-col>
              </v-row>
            </v-col>
            <v-col cols="12" md="5">
              <v-row no-gutters>
                <v-col class="px-1" cols="9">
                  <v-text-field v-model="result" type="number" readonly></v-text-field>
                </v-col>

                <v-col class="px-1" cols="3">
                  <v-select @change="convert" v-model="selected[1]" :items="countries"></v-select>
                </v-col>
              </v-row>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>

const axios = require('axios').default;

export default {

  name: 'App',

  data () {
    return {
      valutes: {},
      selected: ['RUB', 'USD'],
      inputed: "",
      result: null,
      countries: ['RUB'],
      rules: {
        onlyNumbers: (value) => {
          const pattern = /^\d+$/.test(value)
          if (!pattern) this.inputed = this.inputed.replace(/\D/g, '')
          return true;
        }
      }
    }
  },

  methods: {
    convert () {
      let defaultValute = {
        Value: 1,
        Nominal: 1
      };

      let firstValute = this.valutes[this.selected[0]] ?? defaultValute,
          firstValuteValue = firstValute.Value * Number(this.inputed),
          firstValuteNominal = firstValute.Nominal;

      let secondValute = this.valutes[this.selected[1]] ?? defaultValute,
          secondValuteValue = secondValute.Value,
          secondValuteNominal = secondValute.Nominal;

      let result = (firstValuteValue / firstValuteNominal) / (secondValuteValue / secondValuteNominal)
      this.result = result ? Math.floor(result * 10000) / 10000 : null
    }
  },

  mounted () {
    axios
      .get('https://www.cbr-xml-daily.ru/daily_json.js')
      .then(response => {
        this.valutes = response.data.Valute;
        for (let code in response.data.Valute) {
          this.countries.push(code)
        }
      })
      .catch(error => {
        console.log(error)
      })
  }
}
</script>
