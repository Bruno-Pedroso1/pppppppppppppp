<!-- eslint-disable vue/valid-v-slot -->
<!-- eslint-disable vue/v-slot-style -->
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="d-flex align-center flex-column">
          Cadastro de Países
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
                placeholder="ID da Cidade"
                label="ID da Cidade"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="name"
                outlined
                color="green"
                placeholder="Nome da Cidade"
                label="Nome da Cidade"
              >
              </v-text-field>
              <v-text-field
                v-model="idState"
                outlined
                color="green"
                placeholder="ID do Estado"
                label="ID do Estado"
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
      name: null,
      idState: null,
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
          text: 'ID do Estado',
          value: 'idState',
          align: 'center'
        },
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllCities();
  },

  methods: {

    clear() {
      this.name = null;
      this.id = null;
      this.idState = null
    },

    update(item) {
      this.name = item.name;
      this.id = item.id;
      this.idState = item.idState
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          name: this.name,
          idState: this.idState
        }
        if (this.id) {
          await this.$api.patch(`/api/cities/${this.id}`, request);
          this.$toast.success('Cidade Editada')
        }else {
          await this.$api.post(`/api/cities`, request);
          this.$toast.success('Cidade Cadastrada')
        }
        this.name = null;
        this.idState = null;
        this.id = null;
        this.dialog = false;
        await this.getAllCities();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllCities() {
      try {
        const response = await this.$api.get('/api/cities');
        this.items = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async destroy(item) {
      try {
      await this.$api.delete(`/api/cities/${item.id}`);
      await this.getAllCities();
      this.$toast.success('Cidade Removida')
    }catch (error){
      this.$toast.error('Erro ao remover Cidade')
    }
  },
 }
}
</script>

<style>

</style>