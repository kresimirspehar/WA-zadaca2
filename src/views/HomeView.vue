<template>
  <v-container>
    <p><b>Prikaz zahtjeva u tablici</b></p>
    <v-form>
      <div>
        <v-text-field v-model="naziv" label="Unesite naziv"></v-text-field>
      </div>
      <v-btn color="error" @click="dohvatiPod()">Dohvati podatke</v-btn>
    </v-form>

    <v-data-table dense :headers="headers" :items="podIme"></v-data-table>
  </v-container>
</template>

<script>
import axios from "axios";

export default {
  name: "Home",
  data() {
    return {
      podIme: [],
      podGod: "",
      podSpol: "",
      podNac: "",
      naziv: "",

      headers: [
        {
          text: "Naziv",
          value: "osobaIme",
        },
        {
          text: "Nacija",
          value: "osobaNac",
        },
        {
          text: "Spol",
          value: "osobaSpol",
        },

        {
          text: "Godina",
          value: "osobaGod",
        },
      ],
    };
  },

  methods: {
    async dohvatiPod() {
      if (this.naziv == "") {
        return alert("Polje ne moze biti prazno!");
      }

      let godData = await axios.get("https://api.agify.io?name=" + this.naziv);
      this.podGod = godData.data;

      let podSpolData = await axios.get(
        "https://api.genderize.io?name=" + this.naziv
      );
      this.podSpol = podSpolData.data;

      let nacData = await axios.get(
        "https://api.nationalize.io?name=" + this.naziv
      );
      this.podNac = nacData.data;

      this.podIme.push({
        osobaIme: this.naziv,
        osobaGod: this.podGod.age,
        osobaNac:
          this.podNac.country[1].country_id +
          " " +
          (this.podNac.country[1].probability * 100).toFixed(0) +
          "%",
        osobaSpol:
          this.podSpol.gender +
          " " +
          (this.podSpol.probability * 100).toFixed(0) +
          "%",
      });
    },
  },
};
</script>
