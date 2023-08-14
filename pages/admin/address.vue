<!-- eslint-disable vue/valid-v-slot -->
<!-- eslint-disable vue/v-slot-style -->
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="d-flex align-center flex-column">
          Cadastro de Endereços
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
                placeholder="ID do Endereço"
                label="ID do Endereço"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="zipCode"
                outlined
                color="green"
                placeholder="CEP"
                label="CEP"
              >
              </v-text-field>
              <v-text-field
                v-model="district"
                outlined
                color="green"
                placeholder="Bairro"
                label="Bairro"
              >
              </v-text-field>
              <v-text-field
                v-model="street"
                outlined
                color="green"
                placeholder="Rua"
                label="Rua"
              >
            </v-text-field>
            <v-text-field
                v-model="number"
                outlined
                color="green"
                placeholder="Número"
                label="Número"
              >
              </v-text-field>
              <v-text-field
                v-model="complement"
                outlined
                color="green"
                placeholder="Complemento"
                label="Complemento"
              >
              </v-text-field>
              <v-text-field
                v-model="idCity"
                outlined
                color="green"
                placeholder="ID da Cidade"
                label="ID da Cidade"
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
      zipCode: null,
      idCity: null,
      complement: null,
      number: null,
      street: null,
      district: null,
      headers: [
        {
          text: 'ID',
          value: 'id',
          align: 'center'
        },
        {
          text: 'CEP',
          value: 'zipCode',
          align: 'center'
        },
        {
          text: 'ID da Cidade',
          value: 'idCity',
          align: 'center'
        },
        {
          text: 'Complemento',
          value: 'complement',
          align: 'center'
        },
        {
          text: 'Número',
          value: 'number',
          align: 'center'
        },
        {
          text: 'Rua',
          value: 'street',
          align: 'center'
        },
        {
          text: 'Bairro',
          value: 'district',
          align: 'center'
        },
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllAddresses();
  },

  methods: {

    clear() {
      this.zipCode = null;
      this.idCity = null;
      this.complement = null;
      this.number = null;
      this.street = null;
      this.district = null;
      this.id = null;
    },

    update(item) {
      this.zipCode = item.zipCode;
      this.idCity = item.idCity;
      this.complement = item.complement;
      this.number = item.number;
      this.street = item.street;
      this.district = item.district;
      this.id = item.id;
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          zipCode: this.zipCode,
          idCity: this.idCity,
          complement: this.complement,
          number: this.number,
          street: this.street,
          district: this.district
        }
        if (this.id) {
          await this.$api.patch(`/api/addresses/${this.id}`, request);
          this.$toast.success('Endereço Editado')
        }else {
          await this.$api.post(`/api/addresses`, request);
          this.$toast.success('Endereço Cadastrado')
        }
        this.zipCode = null;
        this.idCity = null;
        this.complement = null;
        this.number = null;
        this.street = null;
        this.district = null;
        this.id = null;
        this.id = null;
        this.dialog = false;
        await this.getAllAddresses();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllAddresses() {
      try {
        const response = await this.$api.get('/api/addresses');
        this.items = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async destroy(item) {
      try {
      await this.$api.delete(`/api/addresses/${item.id}`);
      await this.getAllAddresses();
      this.$toast.success('Endereço Removido')
    }catch (error){
      this.$toast.error('Erro ao remover Endereço')
    }
  },
 }
}
</script>

<style>

</style>  