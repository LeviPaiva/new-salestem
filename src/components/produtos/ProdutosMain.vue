<template>
    <div class="left">
      <h1>Produtos</h1>
    </div>
  
    <div class="table-data">
      <div class="add-row">
        <button @click="toggleAdicionarLinha" class="add-row-button">Adicionar Linha</button>
      </div>
      <table class="table">
        <thead>
          <tr>
            <th v-for="(column, index) in columns" :key="index">{{ column }}</th>
            <th>Ações</th>
          </tr>
          <tr v-if="adicionarLinhaVisivel">
            <td v-for="(column, index) in columns" :key="index">
              <input v-model="novoItem[column]" class="input-field" />
            </td>
            <td>
              <button @click="adicionarLinha" class="add-button">Adicionar</button>
            </td>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in displayedItems" :key="item.id" :class="{ 'row-editing': item.editavel }">
            <td v-for="(column, colIndex) in columns" :key="colIndex">
              <input v-model="item[column]" :disabled="!item.editavel" class="input-field" />
            </td>
            <td>
              <template v-if="!item.editavel">
                <button @click="editarItem(index)" class="edit-button">Editar</button>
                <button @click="removerItem(item)" class="remove-button">Remover</button>
              </template>
              <template v-else>
                <button @click="salvarItem(item)" class="save-button">Salvar</button>
                <button @click="cancelarEdicao(item)" class="cancel-button">Cancelar</button>
              </template>
            </td>
          </tr>
        </tbody>
      </table>
  
      <div class="pagination">
        <button @click="previousPage" :disabled="currentPage === 1">Anterior</button>
        <span>Página {{ currentPage }} de {{ totalPages }}</span>
        <button @click="nextPage" :disabled="currentPage === totalPages">Próxima</button>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent } from 'vue';
  import jsonData from './ProdutoData.json'; // Importar o arquivo JSON com os dados
  
  export default defineComponent({
    data() {
      return {
        items: [],
        itemsPerPage: 5,
        currentPage: 1,
        adicionarLinhaVisivel: false,
        novoItem: {},
        columns: [],
        linhasEmEdicao: [], 
      };
    },
    computed: {
      displayedItems() {
        const startIndex = (this.currentPage - 1) * this.itemsPerPage;
        const endIndex = startIndex + this.itemsPerPage;
        return this.items.slice(startIndex, endIndex);
      },
      totalPages() {
        return Math.ceil(this.items.length / this.itemsPerPage);
      },
    },
    created() {
      const dadosLocalStorage = localStorage.getItem('tabelaDadosProdutos');
      if (dadosLocalStorage) {
        this.items = JSON.parse(dadosLocalStorage);
      } else {
        this.items = jsonData;
      }
  
      if (this.items.length > 0) {
        this.columns = Object.keys(this.items[0]).filter((column) => column !== 'id' && column !== 'editavel' && column !== 'copia');
      }
    },
    methods: {
      salvarLocalStorage() {
        localStorage.setItem('tabelaDadosProdutos', JSON.stringify(this.items));
      },
      editarItem(index: number) {
        const realIndex = (this.currentPage - 1) * this.itemsPerPage + index;
        this.items[realIndex].editavel = true;
        this.linhasEmEdicao.push(this.items[realIndex]); // Armazena o objeto da linha em edição
      },
      removerItem(item) {
        const index = this.items.indexOf(item);
        this.items.splice(index, 1);
        this.salvarLocalStorage();
  
        const linhasEmEdicaoSalvas = [...this.linhasEmEdicao]; // Faz uma cópia do array linhasEmEdicao
      this.linhasEmEdicao = []; // Limpa o array linhasEmEdicao
  
      this.items.forEach((linha) => {
        linha.editavel = false; // Desabilita a edição em todas as linhas
      });
      this.salvarLocalStorage();
  
      const index2 = linhasEmEdicaoSalvas.findIndex((linha) => linha === item);
      if (index2 !== -1) {
        linhasEmEdicaoSalvas.splice(index2, 1); // Remove a linha clicada da array linhasEmEdicaoSalvas
      }
  
      linhasEmEdicaoSalvas.forEach((linha) => {
        linha.editavel = true; // Habilita a edição novamente apenas nas linhas salvas
      });
  
      },
      salvarItem(item: any) {
        const linhasEmEdicaoSalvas = [...this.linhasEmEdicao]; // Faz uma cópia do array linhasEmEdicao
      this.linhasEmEdicao = []; // Limpa o array linhasEmEdicao
  
      this.items.forEach((linha) => {
        linha.editavel = false; // Desabilita a edição em todas as linhas
      });
      this.salvarLocalStorage();
  
      const index = linhasEmEdicaoSalvas.findIndex((linha) => linha === item);
      if (index !== -1) {
        linhasEmEdicaoSalvas.splice(index, 1); // Remove a linha clicada da array linhasEmEdicaoSalvas
      }
  
      linhasEmEdicaoSalvas.forEach((linha) => {
        linha.editavel = true; // Habilita a edição novamente apenas nas linhas salvas
      });
  
      },
      cancelarEdicao(item: any) {
        
        item.editavel = false;
        item.editavel = false;
  
  const index = this.linhasEmEdicao.findIndex((linha) => linha === item);
  if (index !== -1) {
    this.linhasEmEdicao.splice(index, 1); // Remove a linha da array linhasEmEdicaoSalvas
  }
  
      },
      previousPage() {
        if (this.currentPage > 1) {
          this.currentPage--;
        }
      },
      nextPage() {
        if (this.currentPage < this.totalPages) {
          this.currentPage++;
        }
      },
      toggleAdicionarLinha() {
        this.adicionarLinhaVisivel = !this.adicionarLinhaVisivel;
      },
      adicionarLinha() {
        const id = this.items.length + 1;
        const novoItem = { ...this.novoItem, id, editavel: false };
        this.items.push(novoItem);
        this.novoItem = this.columns.reduce((acc, column) => {
          acc[column] = '';
          return acc;
        }, {});
        this.adicionarLinhaVisivel = false;
        this.salvarLocalStorage();
      },
    },
  });
  </script>
  
  <style scoped>
  .table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 16px;
  }
  
  th,
  td {
    padding: 12px;
    text-align: left;
    border-bottom: 1px solid #ddd;
  }
  
  .row-editing {
    background-color: #f9f9f9;
  }
  
  .input-field {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
  }
  
  .edit-button,
  .save-button,
  .cancel-button,
  .add-button,
  .remove-button,
  .add-row-button {
    padding: 8px 12px;
    border: none;
    border-radius: 4px;
    color: #fff;
    cursor: pointer;
  }
  
  .remove-button,
  .edit-button {
    margin-left: 3px;
    background-color: #007bff;
  }
  
  .save-button {
    background-color: #28a745;
  }
  
  .cancel-button {
    background-color: #dc3545;
  }
  
  .add-button,
  .add-row-button {
    background-color: #007bff;
  }
  
  .pagination {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 16px;
  }
  
  .pagination button {
    margin: 0 4px;
    padding: 8px;
    border: none;
    border-radius: 4px;
    background-color: #007bff;
    color: #fff;
    cursor: pointer;
  }
  
  .pagination button:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
  
  .add-row {
    display: flex;
    justify-content: flex-end;
    margin: 16px;
  }
  
  .left {
    margin: 10px;
  }
  </style>
  