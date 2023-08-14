<!-- eslint-disable vue/valid-v-slot -->
<!-- eslint-disable vue/v-slot-style -->
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="d-flex align-center flex-column">
          Cadastro de Métodos de Pagamento
        </h1>
        <v-row class="d-flex align-center flex column">
        <v-btn
          fab
          small
          color="green"
          @click="dialog = true; clear()"
        >
          <v-icon>
            mdi-plus
          </v-icon>
        </v-btn>
      </v-row>
      </v-col>
    </v-row>
  
    <v-row class="d-flex align-center flex-column">
      <v-card width="900">
        <v-card-title>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
        </v-card-title>
        <v-data-table
          :headers="headers"
          :items="items"
          :search="search"
        >
          <template v-slot:item.actions="{ item }">
            <v-icon
              small
              color="green"
              @click="update(item)"
            >
              mdi-pencil
            </v-icon>
            <v-icon
              small
              color="red"
              @click="destroy(item)"
            >
              mdi-delete
            </v-icon>
            
          </template>
        </v-data-table>
      </v-card>
    </v-row>
    <v-dialog v-model="dialog">
      <v-card>
        <v-card-title>
          <v-row>
            <v-col cols="2">
              <v-text-field
                v-model="id"
                outlined
                disabled
                color="green"
                placeholder="ID do Método de Pagamento"
                label="ID do Método de Pagamento"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="type"
                outlined
                color="green"
                placeholder="Tipo"
                label="Tipo"
              >
              </v-text-field>
              <v-text-field
                v-model="description"
                outlined
                color="green"
                placeholder="Descrição"
                label="Descrição"
              >
              </v-text-field>
              <v-autocomplete
                v-model="selectedBranch"
                :items="branches"
                item-text="tradingName"
                item-value="id"
                outlined
                color="green"
                placeholder="ID da Filial"
                label="ID da Filial"
              >
              </v-autocomplete>
              <v-autocomplete
                v-model="selectedIntegration"
                :items="integ"
                item-text="type"
                item-value="id"
                outlined
                color="green"
                placeholder="ID da Integração"
                label="ID da Integração"
              >
              </v-autocomplete>
            </v-col>
          </v-row>
        </v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="green"
            @click="persist"
          >
            Salvar
          </v-btn>
        </v-card-actions>
        
      </v-card>
      
    </v-dialog>
  </v-container>
  
</template>

<script>
export default {
  name: 'Index',
  data () {
    return {
      search: null,
      items: [],
      dialog: false,
      id: null,
      type: null,
      description: null,
      selectedBranch: null,
      selectedIntegration: null,
      branches: [],
      integ: [],
      headers: [
        {
          text: 'ID',
          value: 'id',
          align: 'center'
        },
        {
          text: 'Tipo',
          value: 'type',
          align: 'center'
        },
        {
          text: 'Descrição',
          value: 'description',
          align: 'center'
        },
        {
          text: 'ID da Filial',
          value: 'idBranch',
          align: 'center'
        },
        {
          text: 'ID da Integração',
          value: 'idIntegration',
          align: 'center'
        },
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllPaymentMethods();
    await this.getAllIntegrarion();
    await this.getAllBranches();
  },

  methods: {

    clear() {
      this.type = null;
      this.description = null;
      this.id = null;
      this.selectedBranch = null;
      this.selectedIntegration = null;
    },

    update(item) {
      this.type = item.name;
      this.id = item.id;
      this.selectedBranch = item.idBranch;
      this.selectedIntegration = item.idIntegration;
      this.description = item.description;
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          type: this.type,
          description: this.description,
          idBranch: this.selectedBranch,
          idIntegration: this.selectedIntegration
        }
        if (this.id) {
          await this.$api.patch(`/api/payment-methods/${this.id}`, request);
          this.$toast.success('Método de Pagamento Editado')
        }else {
          await this.$api.post(`/api/payment-methods`, request);
          this.$toast.success('Método de Pagamento Cadastrado')
        }
        this.type = null;
        this.selectedBranch = null;
        this.description = null;
        this.selectedIntegration = null;
        this.id = null;
        this.dialog = false;
        await this.getAllPaymentMethods();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllPaymentMethods() {
      try {
        const response = await this.$api.get('/api/payment-methods');
        this.items = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async getAllBranches() {
      try {
        const response = await this.$api.get('/api/branches');
        this.branches = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async getAllIntegrarion() {
      try {
        const response = await this.$api.get('/api/integrations');
        this.integ = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async destroy(item) {
      try {
      await this.$api.delete(`/api/payment-methods/${item.id}`);
      await this.getAllPaymentMethods();
      this.$toast.success('Método de Pagamento Removido')
    }catch (error){
      this.$toast.error('Erro ao remover Método de Pagamento')
    }
  },
 }
}
</script>

<style>

</style>