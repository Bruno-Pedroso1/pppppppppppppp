<!-- eslint-disable vue/valid-v-slot -->
<!-- eslint-disable vue/v-slot-style -->
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="d-flex align-center flex-column">
          Cadastro de Filiais
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
                placeholder="ID da Filial"
                label="ID da Filial"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="businessName"
                outlined
                color="green"
                placeholder="Razão Social da Filial"
                label="Razão Social da Filial"
              >
              </v-text-field>
              <v-text-field
                v-model="companyDocument"
                outlined
                color="green"
                placeholder="CNPJ da Filial"
                label="CNPJ da Filial"
              >
              </v-text-field>
              <v-text-field
                v-model="email"
                outlined
                color="green"
                placeholder="E-mail da Filial"
                label="E-mail da Filial"
              >
              </v-text-field>
              <v-text-field
                v-model="tradingName"
                outlined
                color="green"
                placeholder="Nome fantasia da Filial"
                label="Nome fantasia da Filial"
              >
              </v-text-field>
              <v-text-field
                v-model="idCompany"
                outlined
                color="green"
                placeholder="ID da Empresa"
                label="ID da Empresa"
              >
              </v-text-field>
              <v-text-field
                v-model="idAddress"
                outlined
                color="green"
                placeholder="ID do Endereço"
                label="ID do Endereço"
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
      email: null,
      tradingName: null,
      businessName: null,
      idCompany: null,
      idAddress: null,
      headers: [
        {
          text: 'ID',
          value: 'id',
          align: 'center'
        },
        {
          text: 'CNPJ',
          value: 'companyDocument',
          align: 'center'
        },
        {
          text: 'E-mail',
          value: 'email',
          align: 'center'
        },
        {
          text: 'Nome Fantasia',
          value: 'tradingName',
          align: 'center'
        },
        {
          text: 'Razão Social',
          value: 'businessName',
          align: 'center'
        },
        {
          text: 'ID da Empresa',
          value: 'idCompany',
          align: 'center'
        },
        {
          text: 'ID do Endereço',
          value: 'idAddress',
          align: 'center'
        },
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllBranch();
  },

  methods: {

    clear() {
      this.companyDocument = null;
      this.id = null;
      this.email = null;
      this.tradingName = null;
      this.businessName = null;
      this.idCompany = null;
      this.idAddress = null;
    },

    update(item) {
      this.businessName = item.businessName;
      this.companyDocument = item.companyDocument;
      this.email = item.email;
      this.tradingName = item.tradingName;
      this.idCompany = item.idCompany;
      this.idAddress = item.idAddress;
      this.id = item.id;
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          companyDocument: this.companyDocument,
          email: this.email,
          tradingName: this.tradingName,
          businessName: this.businessName,
          idCompany: this.idCompany,
          idAddress: this.idAddress,
        }
        if (this.id) {
          await this.$api.patch(`/api/branches/${this.id}`, request);
          this.$toast.success('Filial Editado')
        }else {
          await this.$api.post(`/api/branches`, request);
          this.$toast.success('Filial Cadastrado')
        }
        this.companyDocument = null;
        this.email = null;
        this.tradingName = null;
        this.businessName = null;
        this.idCompany = null;
        this.idAddress = null;
        this.id = null;
        this.dialog = false;
        await this.getAllBranch();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllBranch() {
      try {
        const response = await this.$api.get('/api/branches');
        this.items = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async destroy(item) {
      try {
      await this.$api.delete(`/api/branches/${item.id}`);
      await this.getAllBranch();
      this.$toast.success('FIlial Removida')
    }catch (error){
      this.$toast.error('Erro ao remover Filial')
    }
  },
 }
}
</script>

<style>

</style>