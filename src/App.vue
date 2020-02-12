<template>
  <div id="app" class="container">
    <h1>Bem vindo a {{ title }}</h1>
    <h3 v-if="horas >= 9 && horas < 17" id="aberta">ABERTA</h3>
    <h3 v-else-if="horas >= 17 && horas < 18" id="proxima-fechar">PROXIMA DE FECHAR</h3>
    <h3 v-else id="fechada">FECHADA</h3>
    <ul id="filmes">
        <li>
          <b-row id="cards">
            <b-card v-for="filme in filmes" v-bind:key="filme.id" style="max-width: 20rem;">

              <img src="./assets/logo.png" alt="Image" top>
              
              <b-card-text>{{ filme.titulo }}</b-card-text>

              <b-card-text>{{ filme.descricao }}</b-card-text>

              <b-card-text>{{ filme.valor }}</b-card-text>

              <b-button href="#" variant="primary">ALUGAR</b-button>
            </b-card>
          </b-row>
        </li>
    </ul>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";

export default {
  name: "App",
  data: function() {
    return {
      title: "Locadora de Filmes",
      horas: new Date().getHours(),
      filmes: [
        {
          id: 1,
          titulo: "Vingadores",
          descricao: "Um filme de heróis",
          valor: 25,
          imagem: "./assets/logo.png"
        },
        {
          id: 2,
          titulo: "Pantera Negra",
          descricao: "Um filme de panteras",
          valor: 35,
          imagem: "./assets/logo.png"
        },
        {
          id: 3,
          titulo: "Homem-Formiga",
          descricao: "Um filme de formigas",
          valor: 20,
          imagem: "./assets/logo.png"
        },
        {
          id: 4,
          titulo: "Capitã Marvel",
          descricao: "Um filme de capitãs",
          valor: 40,
          imagem: "./assets/logo.png"
        },
        {
          id: 5,
          titulo: "Hulk",
          descricao: "Um filme de força",
          valor: 10,
          imagem: "./assets/logo.png"
        }
      ]
    };
  },
  components: {}
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#aberta {
  color: blue;
}

#proxima-fechar {
  color: orange;
}

#fechada {
  color: red;
}

#filmes{
  list-style-type:none;
}

#cards {
  justify-content: space-between;
}

</style>
