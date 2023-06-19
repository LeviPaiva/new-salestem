<template>
    <div>
      <table class="table">
        <thead>
          <tr>
            <th>Nome</th>
            <th>Idade</th>
            <th>E-mail</th>
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in items" :key="item.id" :class="{ 'row-editing': item.editavel }">
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
              </template>
              <template v-else>
                <button @click="salvarItem(index)" class="save-button">Salvar</button>
                <button @click="cancelarEdicao(index)" class="cancel-button">Cancelar</button>
              </template>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent } from 'vue';
  import jsonData from './VendaData.json'; // Importar o arquivo JSON com os dados
  
  export default defineComponent({
    data() {
      return {
        items: jsonData, // Atribuir os dados do arquivo JSON à propriedade "items"
      };
    },
    methods: {
      editarItem(index: number) {
        this.items[index].editavel = true;
        this.items[index].copia = { ...this.items[index] }; // Faz uma cópia do item antes da edição
      },
      salvarItem(index: number) {
        this.items[index].editavel = false;
        this.items[index].copia = {}; // Limpa a cópia após salvar as alterações
      },
      cancelarEdicao(index: number) {
        Object.assign(this.items[index], this.items[index].copia); // Restaura o item para o estado original
        this.items[index].editavel = false;
        this.items[index].copia = {}; // Limpa a cópia
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
  
  th {
    background-color: #f2f2f2;
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
  .cancel-button {
    padding: 8px 12px;
    border: none;
    border-radius: 4px;
    color: #fff;
    cursor: pointer;
  }
  
  .edit-button {
    background-color: #007bff;
  }
  
  .save-button {
    background-color: #28a745;
  }
  
  .cancel-button {
    background-color: #dc3545;
  }
  </style>