<template>
  <div id="app" class="container">
    <h1>Bem vindo a {{ title }}</h1>
    <h3 v-if="horas >= 9 && horas < 17" id="aberta">ABERTA</h3>
    <h3 v-else-if="horas >= 17 && horas < 18" id="proxima-fechar">PROXIMA DE FECHAR</h3>
    <h3 v-else id="fechada">FECHADA</h3>
    <b-button
      href="#" @click="mostrarCarrinho()" variant="primary">
      Carrinho: {{ quantidadeNoCarrinho }} filmes
    </b-button>

    <b-button
      href="#" @click="mostrar5estrelas()" variant="primary">
      Filtrar 5 estrelas
    </b-button>

    <ul id="filmes" v-if="mostrarFilmes">
      <li>
        <b-row id="cards">
          <b-card
            v-for="filme in filmesOrdenados"
            v-bind:key="filme.id"
            :title="filme.titulo"
            :id="filme.id"
            :img-src="filme.imagem"
            img-alt="Image"
            img-top
            tag="article"
            style="width: 20rem"
            class="mb-2"
          >
            <b-card-text>{{ filme.descricao }}</b-card-text>

            <b-card-text
              v-if="filme.estoqueDisponivel - quantidadeNoCarrinhoPorFilme(filme) === 0">
                Indisponível
            </b-card-text>
            <b-card-text
              v-else-if="filme.estoqueDisponivel - quantidadeNoCarrinhoPorFilme(filme) < 5">
                Apenas {{ filme.estoqueDisponivel - quantidadeNoCarrinhoPorFilme(filme) }} itens no estoque.
            </b-card-text>
            <b-card-text
              v-else>
                Alugue agora!
            </b-card-text>

            <b-card-text>{{ filme.valor | formatarPreco("R$") }}</b-card-text>

            <b-card-text class="avaliacao"> 
            <span v-for="n in 5" :key="n">
              <b-icon icon="star-fill" variant="warning" font-scale="2" v-if="filme.avaliacao >= n"></b-icon> 
              <b-icon icon="star" font-scale="2" variant="warning" v-else></b-icon>
            </span>
          </b-card-text>
            

            <b-button
              href="#"
              @click="adicionarAoCarrinho(filme)"
              v-if="validarPermissaoParaAdicionarNoCarrinho(filme)"
              variant="primary"
            >ALUGAR</b-button>
            <b-button href="#" v-else variant="primary" disabled>ALUGAR</b-button>
          </b-card>
        </b-row>
      </li>
    </ul>
    <div class="row" v-else>
      <h2>Carrinho</h2>

      <div class="col-12">
        <form>
          <div class="form-group">
            <label for="pedido.primeiroNome">Primeiro nome</label>
            <input
              type="text"
              class="form-control"
              id="primeiroNome"
              placeholder="Digita o primeiro nome"
              v-model="pedido.primeiroNome"
            />
          </div>
          <div class="form-group">
            <label for="ultimoNome">Último nome</label>
            <input
              type="text"
              class="form-control"
              id="ultimoNome"
              placeholder="Digite o último nome"
              v-model="pedido.ultimoNome"
            />
          </div>
          <div class="form-group">
            <label for="endereco">Endereço</label>
            <input
              type="text"
              class="form-control"
              id="endereco"
              placeholder="Digita o endereço"
              v-model.trim.lazy="pedido.endereco"
            />
          </div>
          <div class="form-group">
            <label for="cidade">Cidade</label>
            <input
              type="text"
              class="form-control"
              id="cidade"
              placeholder="Digita a cidade"
              v-model.trim.lazy="pedido.cidade"
            />
          </div>
          <div class="form-group">
            <label for="estado">Estado</label>
            <select class="form-control" id="estado" v-model="pedido.estado">
              <option disabled value>Escolha um estado</option>
              <option
                v-for="(estado, key) in estados"
                v-bind:value="estado"
                v-bind:key="key"
              >{{ key }}</option>
            </select>
          </div>
          <div class="form-group">
            <label for="cep">CEP</label>
            <input
              type="number"
              class="form-control"
              id="cep"
              placeholder="Digita o CEP"
              v-model.number="pedido.cep"
            />
          </div>
          <div class="form-group form-check">
            <input
              type="checkbox"
              class="form-check-input"
              id="pagoNaEntrega"
              v-bind:true-value="pedido.simNaEntrega"
              v-bind:false-value="pedido.naoNaEntrega"
              v-model="pedido.pagoNaEntrega"
            />
            <label class="form-check-label" for="pagoNaEntrega">Pago na entrega?</label>
          </div>
          <div class="form-group form-check-inline">
            <input
              type="radio"
              class="form-check-input"
              id="manha"
              value="Manhã"
              v-model="pedido.entrega"
            />
            <label class="form-check-label" for="manha">Manhã</label>
          </div>
          <div class="form-group form-check-inline">
            <input
              type="radio"
              class="form-check-input"
              id="tarde"
              value="Tarde"
              v-model="pedido.entrega"
            />
            <label class="form-check-label" for="tarde">Tarde</label>
          </div>
          <div class="form-group form-check-inline">
            <input
              type="radio"
              class="form-check-input"
              id="noite"
              value="Noite"
              v-model="pedido.entrega"
            />
            <label class="form-check-label" for="noite">Noite</label>
          </div>

          <div class="form-group">
            <button
              type="submit"
              class="btn btn-primary"
              v-on:click="submitFormulario"
            >Finalizar pedido</button>
          </div>
        </form>
      </div>
      <div class="col-12">
        <pre>
          Primeiro nome: {{ pedido.primeiroNome }}
          Último nome: {{ pedido.ultimoNome }}
          Endereço: {{ pedido.endereco }}
          Cidade: {{ pedido.cidade }}
          Estado: {{ pedido.estado }}
          CEP: {{ pedido.cep }}
          Pago na entrega?: {{ pedido.pagoNaEntrega }}
          Entrega: {{ pedido.entrega }}
        </pre>
      </div>
    </div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";

export default {
  name: "App",
  data: function() {
    return {
      filtrar5estrelas: false,
      mostrarFilmes: true,
      title: "Locadora de Filmes",
      horas: new Date().getHours(),
      pedido: {
        primeiroNome: "",
        ultimoNome: "",
        endereco: "",
        cidade: "",
        estado: "",
        cep: "",
        pagoNaEntrega: "Não",
        simNaEntrega: "Sim",
        naoNaEntrega: "Não",
        entrega: "Manhã"
      },
      estados: {
        RJ: 'Rio de Janeiro',
        MG: 'Minas Gerais',
        SP: 'São Paulo',
        ES: 'Espírito Santo'
      },
      filmes: [
        {
          id: 1,
          titulo: "Vingadores",
          descricao: "Um filme de heróis",
          valor: 25,
          estoqueDisponivel: 3,
          avaliacao: 5,
          imagem:
            "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
        },
        {
          id: 2,
          titulo: "Pantera Negra",
          descricao: "Um filme de panteras",
          valor: 35,
          estoqueDisponivel: 8,
          avaliacao: 2,
          imagem:
            "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
        },
        {
          id: 3,
          titulo: "Homem-Formiga",
          descricao: "Um filme de formigas",
          valor: 20,
          estoqueDisponivel: 9,
          avaliacao: 4,
          imagem:
            "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
        },
        {
          id: 4,
          titulo: "Capitã Marvel",
          descricao: "Um filme de capitãs",
          valor: 40,
          estoqueDisponivel: 6,
          avaliacao: 5,
          imagem:
            "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
        },
        {
          id: 5,
          titulo: "Hulk",
          descricao: "Um filme de força",
          valor: 10,
          estoqueDisponivel: 8,
          avaliacao: 3,
          imagem:
            "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
        },
        {
          id: 6,
          titulo: "Teste",
          descricao: "Teste",
          valor: 1,
          estoqueDisponivel: 12,
          avaliacao: 1,
          imagem:
            "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
        }
      ],
      carrinho: []
    };
  },
  methods: {
    mostrar5estrelas: function() {
      this.filtrar5estrelas = this.filtrar5estrelas ? false : true;
      this.filmes = this.filmes.filter(elem => elem.avaliacao === 5)
      return this.filmes;
    },
    checarAvaliacao(n, filme) {
      return filme.avaliacao - n >= 0;
    },
    submitFormulario() {
      alert('Pedido finalizado');
    },
    adicionarAoCarrinho: function(filme) {
      this.carrinho.push(filme.id);
    },
    quantidadeNoCarrinhoPorFilme: function(filme) {
      var quantidade = 0;
      for (let i = 0; i < this.carrinho.length; i++) {
        if (filme.id == this.carrinho[i]) {
          quantidade++;
        }
      }
      return quantidade;
      // return carrinho.filter(elem => elem === filme.id).length;
    },
    validarPermissaoParaAdicionarNoCarrinho: function(filme) {
      return filme.estoqueDisponivel > this.quantidadeNoCarrinhoPorFilme(filme);
    },
    mostrarCarrinho: function() {
      this.mostrarFilmes = this.mostrarFilmes ? false : true;
    }
  },
  computed: {
    quantidadeNoCarrinho: function() {
      return this.carrinho.length;
    },
    filmesOrdenados: function(){
      let filmesOrd = this.filmes;
      filmesOrd.sort(function (a, b) {
        if (a.titulo > b.titulo) {
          return 1;
        }
        if (a.titulo < b.titulo) {
          return -1;
        }
        return 0;
      });
      return this.filmes
      }
  }
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
  align-items: center;
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

#filmes {
  list-style-type: none;
}

#cards {
  justify-content: space-between;
  display: flex;
  flex-wrap: wrap;
}
</style>
