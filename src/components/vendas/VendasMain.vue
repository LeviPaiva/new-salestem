<template>
  <div class="left">
    <h1>Venda</h1>


  </div>

  <div class="table-data">

    <div class="add-row">
      <button @click="toggleAdicionarLinha" class="add-row-button">Adicionar Linha</button>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th>Nome</th>
          <th>Idade</th>
          <th>E-mail</th>
          <th>Ações</th>
        </tr>
        <tr v-if="adicionarLinhaVisivel">
          <td>
            <input v-model="novoItem.nome" class="input-field" />
          </td>
          <td>
            <input v-model="novoItem.idade" class="input-field" />
          </td>
          <td>
            <input v-model="novoItem.email" class="input-field" />
          </td>
          <td>
            <button @click="adicionarLinha" class="add-button">Adicionar</button>
          </td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in displayedItems" :key="item.id" :class="{ 'row-editing': item.editavel }">
          <td>
            <input v-model="item.nome" :disabled="!item.editavel" class="input-field" />
          </td>
          <td>
            <input v-model="item.idade" :disabled="!item.editavel" class="input-field" />
          </td>
          <td>
            <input v-model="item.email" :disabled="!item.editavel" class="input-field" />
          </td>
          <td>
            <template v-if="!item.editavel">
              <button @click="editarItem(index)" class="edit-button">Editar</button>
              <!-- <td> -->
              <button @click="removerItem(item)" class="remove-button">Remover</button>
              <!-- </td> -->
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
import jsonData from './VendaData.json'; // Importar o arquivo JSON com os dados

export default defineComponent({
  data() {
    return {
      items: jsonData, // Atribuir os dados do arquivo JSON à propriedade "items"
      itemsPerPage: 5, // Número de itens exibidos por página
      currentPage: 1, // Página atual
      adicionarLinhaVisivel: false, // Indica se o formulário para adicionar nova linha está visível
      novoItem: { nome: '', idade: '', email: '', editavel: false }, // Novo item a ser adicionado
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
  methods: {
    editarItem(index: number) {
      const realIndex = (this.currentPage - 1) * this.itemsPerPage + index;
      this.items[realIndex].editavel = true;
      this.items[realIndex].copia = { ...this.items[realIndex] }; // Faz uma cópia do item antes da edição
    },
    removerItem(item) {
      const index = this.items.indexOf(item);
      this.items.splice(index, 1);
    },
    salvarItem(item: any) {
      item.editavel = false;
      item.copia = {}; // Limpa a cópia após salvar as alterações
    },
    cancelarEdicao(item: any) {
      Object.assign(item, item.copia); // Restaura o item para o estado original
      item.editavel = false;
      item.copia = {}; // Limpa a cópia
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
      this.novoItem = { nome: '', idade: '', email: '', editavel: false };
      this.adicionarLinhaVisivel = false;
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
  /* Alinha o botão à direita da div */
  margin: 16px;
  /* Adiciona espaço abaixo da div */
}

.left {
  margin: 10px;
}
</style>
