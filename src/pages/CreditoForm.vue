<template>
  <div>
    <h1>Análise de Crédito</h1>
    <form @submit.prevent="analisarCredito">
      <div>
        <label for="nome">Nome:</label>
        <input type="text" v-model="form.nome" id="nome" required />
      </div>
      <div>
        <label for="email">Email:</label>
        <input type="email" v-model="form.email" id="email" required />
      </div>
      <div>
        <label for="telefone">Telefone:</label>
        <input type="tel" v-model="form.telefone" id="telefone" required />
      </div>

      <div>
        <label for="numeroCartao">Número do Cartão:</label>
        <input type="text" v-model="form.numeroCartao" id="numeroCartao" required />
      </div>
      <div>
        <label for="validadeCartao">Validade:</label>
        <input type="month" v-model="form.validadeCartao" id="validadeCartao" required />
      </div>
      <div>
        <label for="codigoSeguranca">Código de Segurança:</label>
        <input type="text" v-model="form.codigoSeguranca" id="codigoSeguranca" required />
      </div>

      <div>
        <label for="score">Score: {{ form.score }}</label>
        <input type="range" v-model="form.score" id="score" min="1" max="1000" />
      </div>

      <div>
        <label for="preco">Preço do Produto:</label>
        <input type="text" v-model="precoFormatado" @input="atualizarPreco" id="preco" required />
      </div>

      <div>
        <label for="juros">Juros ao mes(%):</label>
        <input type="number" v-model="form.juros" id="juros" step="0.01" />
      </div>

      <button type="submit">Analisar Crédito</button>
      <button type="button" @click="preencherComDadosDeTeste">Preencher com Dados de Teste</button>
    </form>

    <div v-if="mostrarModal" class="modal" @click.self="fecharModal">
      <div class="modal-content">
        <span class="close" @click="fecharModal">&times;</span>
        <div v-if="form.score < 500">
          <p>Cliente Inapto</p>
        </div>
        <div v-else>
          <p>Dados enviados com sucesso!</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        nome: '',
        email: '',
        telefone: '',
        numeroCartao: '',
        validadeCartao: '',
        codigoSeguranca: '',
        score: 500,
        preco: 0,
        juros: 0,
      },
      precoFormatado: '0,00',
      mostrarModal: false,
    };
  },
  methods: {
    analisarCredito() {
      const opcoesParcelamento = [];
      const limiteParcelas = this.form.score > 800 ? 10 : 4;
      for (let i = 1; i <= limiteParcelas; i++) {
        const valorParcela = this.calcularParcela(i);
        const totalPraPagar = this.calcularTotal(i);
        opcoesParcelamento.push({
          quantidadeParcelas: i,
          valorParcela: valorParcela,
          totalPraPagar: totalPraPagar,
        });
      }

      if (this.form.score < 500) {
        this.mostrarModal = true; 
      } else {
        const dados = {
          cliente: {
            nome: this.form.nome,
            email: this.form.email,
            telefone: this.form.telefone,
          },
          cartao: {
            numeroCartao: this.form.numeroCartao,
            validadeCartao: this.form.validadeCartao,
            codigoSeguranca: this.form.codigoSeguranca,
          },
          opcoesParcelamento: opcoesParcelamento,
          jurosAoMes: this.form.juros / 100,
        };

        console.log('Enviando dados para o webhook:', dados);
        this.resetarFormulario();
        this.mostrarModal = true;
      }
    },
    preencherComDadosDeTeste() {
      this.form = {
        nome: 'João da Silva',
        email: 'joao.silva@example.com',
        telefone: '11987654321',
        numeroCartao: '4111111111111111',
        validadeCartao: '2025-12',
        codigoSeguranca: '123',
        score: 750,
        preco: 300,
        juros: 5,
      };
      this.precoFormatado = this.formatarPreco(this.form.preco);
    },
    fecharModal() {
      this.mostrarModal = false;
    },
    atualizarPreco(event) {
      let valor = event.target.value.replace(/\D/g, '');
      valor = parseInt(valor).toString();
      while (valor.length < 3) valor = '0' + valor;
      const parteInteira = valor.slice(0, -2);
      const parteDecimal = valor.slice(-2);
      this.precoFormatado = `${parteInteira},${parteDecimal}`;
      this.form.preco = parseFloat(`${parteInteira}.${parteDecimal}`);
    },
    formatarPreco(valor) {
      const [inteiros, decimais] = valor.toFixed(2).split('.');
      return `${parseInt(inteiros)},${decimais}`;
    },
    resetarFormulario() {
      this.form = {
        nome: '',
        email: '',
        telefone: '',
        numeroCartao: '',
        validadeCartao: '',
        codigoSeguranca: '',
        score: 500,
        preco: 0,
        juros: 0,
      };
      this.precoFormatado = '0,00';
    },
    calcularParcela(n) {
      const preco = this.form.preco;
      const juros = this.form.juros / 100;
      if (n === 1) {
        return preco.toFixed(2);
      } else {
        const total = preco + (preco * juros * (n - 1));
        return (total / n).toFixed(2);
      }
    },
    calcularTotal(n) {
      const preco = this.form.preco;
      const juros = this.form.juros / 100;
      if (n === 1) {
        return preco.toFixed(2);
      } else {
        const total = preco + (preco * juros * (n - 1));
        return total.toFixed(2);
      }
    }
  }
};
</script>

<style scoped>
form {
  display: flex;
  flex-direction: column;
  max-width: 400px;
  margin: auto;
}
div {
  margin-bottom: 15px;
}
label {
  font-weight: bold;
}
button {
  padding: 10px;
  margin-top: 10px;
  background-color: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
}
button:hover {
  background-color: #45a049;
}
button[type="button"] {
  background-color: #008CBA;
}
button[type="button"]:hover {
  background-color: #007BB5;
}
.modal {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}
.modal-content {
  background-color: #fefefe;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
  max-width: 500px;
  text-align: center;
  position: relative;
}
.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
  cursor: pointer;
}
.close:hover{
  color: black;
  text-decoration: none;
}
</style>