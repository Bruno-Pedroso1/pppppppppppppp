<!-- eslint-disable vue/valid-v-slot -->
<!-- eslint-disable vue/v-slot-style -->
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="d-flex align-center flex-column">
          Cadastro de Empresa
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
                placeholder="ID da Compania"
                label="ID da Compania"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="companyDocument"
                v-mask="['##.###.###/####-##']"
                outlined
                color="green"
                placeholder="CNPJ"
                label="CNPJ"
              >
              </v-text-field>
              <v-text-field
                v-model="businessName"
                outlined
                color="green"
                placeholder="Nome da Compania"
                label="Nome da Compania"
              >
              </v-text-field>
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
      companyDocument: null,
      businessName: null,
      headers: [
        {
          text: 'ID',
          value: 'id',
          align: 'center'
        },
        {
          text: 'Nome da Compania',
          value: 'businessName',
          field: 'business_name',
          align: 'center'
        },
        {
          text: 'CNPJ',
          value: 'companyDocument',
          field: 'company_document',
          align: 'center'
        },
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllCompanies();
  },

  methods: {

    clear() {
      this.businessName = null;
      this.id = null;
      this.companyDocument = null
    },

    update(item) {
      this.businessName = item.businessName;
      this.id = item.id;
      this.companyDocument = item.companyDocument
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          businessName: this.businessName,
          companyDocument: this.companyDocument
        }
        if (this.id) {
          await this.$api.patch(`/api/companies/${this.id}`, request);
          this.$toast.success('Empresa Editada')
        }else {
          await this.$api.post(`/api/companies`, request);
          this.$toast.success('Empresa Cadastrada')
        }
        this.businessName = null;
        this.companyDocument = null;
        this.id = null;
        this.dialog = false;
        await this.getAllCompanies();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllCompanies() {
      try {
        const response = await this.$api.get('/api/companies');
        this.items = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async destroy(item) {
      try {
      await this.$api.delete(`/api/companies/${item.id}`);
      await this.getAllCompanies();
      this.$toast.success('Empresa Removida')
    }catch (error){
      this.$toast.error('Erro ao remover Empresa')
    }
  },
 }
}
</script>

<style>

</style>