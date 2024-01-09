<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row justify="space-between" no-gutters>
          <v-btn @click="openModal" color="success">Cadastrar</v-btn>
          <v-btn @click="confirmClear" color="blue">Limpar</v-btn>
        </v-row>
        <v-card class="my-3">
          <v-list-item-group v-if="items.length > 0">
            <v-list-item v-for="(item, index) in items" :key="index">
              <div>
                <v-row align="center">
                  <v-col cols="8" class="text-left">
                    {{ item.quantidade }}x - {{ item.nome }}
                    <span style="font-size: 14px;">
                      {{ floatToMoney(item.valorUnitario || 0) }}
                    </span>
                  </v-col>
                  <v-col cols="4" class="text-right">
                    {{ floatToMoney(item.valorTotal || 0) }}
                    <v-btn class="mb-1" color="warning" size="x-small" icon="mdi-delete" @click="deleteItem(index)"></v-btn>
                  </v-col>
                </v-row>
              </div>
              <v-divider v-if="index+1 != items.length" class="my-1"></v-divider>
            </v-list-item>
          </v-list-item-group>
          <v-list-item v-else>
            Nenhum item na lista
          </v-list-item>
        </v-card>
        <v-row>
          <v-col>
            <v-row>
              <v-col class="text-right text-h6">
                <strong>Total: {{ floatToMoney(getTotal()) }}</strong>
              </v-col>
            </v-row>
          </v-col>
        </v-row>

        <v-dialog v-model="modal" max-width="600">
          <v-card>
            <v-card-title>Cadastrar Item</v-card-title>
            <v-card-text>
              <v-form @submit.prevent="saveItem">
                <v-text-field autofocus v-model="nome" label="Nome"></v-text-field>
                <v-text-field v-model="quantidade" label="Quantidade" type="number"></v-text-field>
                <v-text-field v-model="valor" label="Valor" type="number"></v-text-field>
                <v-row no-gutters justify="space-between">
                  <v-btn @click="resetForm()">Cancelar</v-btn>
                  <v-btn color="success" type="submit">Salvar</v-btn>
                </v-row>
              </v-form>
            </v-card-text>
          </v-card>
        </v-dialog>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      items: JSON.parse(localStorage.getItem('items')) || [],
      modal: false,
      nome: '',
      quantidade: 0,
      valor: 0
    };
  },
  methods: {
    openModal() {
      this.modal = true
    },
    floatToMoney (float) {
      var formatter = new Intl.NumberFormat('pt-BR',
        {
          style: 'currency',
          currency: 'brl',
          maximumFractionDigits: 2
        }
      )
      return formatter.format(float)
    },
    saveItem() {
      if (!this.nome.trim()) {
        alert('Por favor, preencha o campo "Nome" antes de salvar.');
        return
      }
      const newItem = {
        nome: this.nome,
        quantidade: parseInt(this.quantidade),
        valorUnitario: parseFloat(this.valor),
        valorTotal: parseInt(this.quantidade) * parseFloat(this.valor)
      };

      this.items.push(newItem);
      this.saveItemsToLocalStorage()
      this.resetForm()
    },
    deleteItem(index) {
      this.items.splice(index, 1)
      this.saveItemsToLocalStorage()
    },
    saveItemsToLocalStorage() {
      localStorage.setItem('items', JSON.stringify(this.items))
    },
    resetForm() {
      this.modal = false
      this.nome = ''
      this.quantidade = 0
      this.valor = 0
    },
    getTotal() {
      return this.items.reduce((total, item) => total + item.valorTotal, 0)
    },
    confirmClear() {
      if (confirm('Tem certeza que deseja limpar todos os itens?')) {
        this.items = []
        this.saveItemsToLocalStorage()
      }
    }
  }
};
</script>
