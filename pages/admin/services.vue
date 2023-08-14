<!-- eslint-disable vue/valid-v-slot -->
<!-- eslint-disable vue/v-slot-style -->
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="d-flex align-center flex-column">
          Cadastro de Serviços
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
                placeholder="ID do Serviço"
                label="ID do Serviço"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="name"
                outlined
                color="green"
                placeholder="Nome do Serviço"
                label="Nome do Serviço"
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
      name: null,
      selectedBranch: null,
      branches: [],
      headers: [
        {
          text: 'ID',
          value: 'id',
          align: 'center'
        },
        {
          text: 'Nome',
          value: 'name',
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
    await this.getAllServices();
    await this.getAllBranches();
  },

  methods: {

    clear() {
      this.name = null;
      this.id = null;
      this.selectedBranch = null
    },

    update(item) {
      this.name = item.name;
      this.id = item.id;
      this.selectedBranch = item.idBranch
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          name: this.name,
          idBranch: this.selectedBranch
        }
        if (this.id) {
          await this.$api.patch(`/api/services/${this.id}`, request);
          this.$toast.success('Serviço Editado')
        }else {
          await this.$api.post(`/api/services`, request);
          this.$toast.success('Serviço Cadastrado')
        }
        this.name = null;
        this.selectedBranch = null;
        this.id = null;
        this.dialog = false;
        await this.getAllServices();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllServices() {
      try {
        const response = await this.$api.get('/api/services');
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
      await this.$api.delete(`/api/services/${item.id}`);
      await this.getAllServices();
      this.$toast.success('Serviço Removido')
    }catch (error){
      this.$toast.error('Erro ao remover Serviço')
    }
  },
 }
}
</script>

<style>

</style>