<!-- eslint-disable vue/valid-v-slot -->
<!-- eslint-disable vue/v-slot-style -->
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="d-flex align-center flex-column">
          Cadastro de Integrações
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
                placeholder="ID da Integração"
                label="ID da Integração"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="type"
                outlined
                color="green"
                placeholder="Tipo da Integração"
                label="Tipo da Integração"
              >
              </v-text-field>
              <v-text-field
                v-model="tokenApi"
                outlined
                color="green"
                placeholder="Token API"
                label="Token API"
              >
              </v-text-field>
              <v-autocomplete
                v-model="selectedBranch"
                :items="branches"
                item-value="id"
                item-text="tradingName"
                outlined
                color="green"
                placeholder="ID da Filial"
                label="ID da Filial"
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
      branches: [],
      selectedBranch: null,
      search: null,
      items: [],
      dialog: false,
      id: null,
      type: null,
      tokenApi: null,
      headers: [
        {
          text: 'ID',
          value: 'id',
          align: 'center'
        },
        {
          text: 'Tipo da API',
          value: 'type',
          align: 'center'
        },
        {
          text: 'Token API',
          value: 'tokenApi',
          field: 'token_api',
          align: 'center'
        },
        {
          text: 'ID da Filial',
          value: 'idBranch',
          align: 'center'
        },
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllIntegration();
    await this.getAllBranches();
  },

  methods: {

    clear() {
      this.type = null;
      this.id = null;
      this.selectedBranch = null;
      this.tokenApi = null;
    },

    update(item) {
      this.type = item.type;
      this.id = item.id;
      this.selectedBranch = item.idBranch;
      this.dialog = true;
      this.tokenApi = item.tokenApi;
    },

    async persist() {
      try {
        const request = {
          type: this.type,
          idBranch: this.selectedBranch,
          tokenApi: this.tokenApi
        }
        if (this.id) {
          await this.$api.patch(`/api/integrations/${this.id}`, request);
          this.$toast.success('Integração Editada')
        }else {
          await this.$api.post(`/api/integrations`, request);
          this.$toast.success('Integração Cadastrada')
        }
        this.type = null;
        this.selectedBranch = null;
        this.tokenApi = null;
        this.id = null;
        this.dialog = false;
        await this.getAllIntegration();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllIntegration() {
      try {
        const response = await this.$api.get('/api/integrations');
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
    async destroy(item) {
      try {
      await this.$api.delete(`/api/integrations/${item.id}`);
      await this.getAllIntegration();
      this.$toast.success('Integração Removida')
    }catch (error){
      this.$toast.error('Erro ao remover Integração')
    }
  },
 }
}
</script>

<style>

</style>