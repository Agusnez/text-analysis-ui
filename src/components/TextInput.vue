<template>
  <div class="sm:border mx-5 mt-5 py-10 text-center">
    <h1 class="text-xl text-gray-900 font-normal">Introduzca su texto:</h1>
    <textarea
      class="border sm:w-3/4 w-full mt-5 p-3"
      :class="getBackground"
      rows="4"
      col="10"
      v-model="text"
      v-on:keyup.enter="analize"
      @input="onChange"
    />
    <br />
    <button class="bg-green-900 text-white rounded-full mt-3 p-3 px-6" @click="analize">
      <img v-if="this.loading" class="h-6 w-16" src="@/assets/puff.svg" />
      {{ this.loading ? '' : 'Analizar' }}
    </button>
    <div class="bg-gray-100 mt-5 mx-5 p-3" v-if="info">
      <p v-if="this.prediction">Este texto presenta una connotación positiva</p>
      <p v-else>Este texto presenta una connotación negativa</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "TextInput",
  props: {
    msg: String
  },
  data() {
    return {
      info: null,
      loading: false,
      errored: false,
      text: "",
      prediction: null
    };
  },
  methods: {
    analize() {
      if (this.text != "") {
        this.loading = true;
        axios
          .post("https://text-analysis-mle.herokuapp.com/predict", {
            text: this.text
          })
          .then(response => {
            this.info = response.data;
            if (this.info.prediction == "Positivo") {
              this.prediction = true;
            } else {
              this.prediction = false;
            }
          })
          .catch(error => {
            console.log(error);
            this.errored = true;
          })
          .finally(() => (this.loading = false));
      }
    },
    onChange() {
      this.prediction = null;
      this.info = null;
    }
  },
  computed: {
    getBackground() {
      if (this.prediction == true) {
        return "bg-green-200";
      } else if (this.prediction == false) {
        return "bg-red-200";
      } else {
        return "";
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
