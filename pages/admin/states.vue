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
                placeholder="ID do Estado"
                label="ID do Estado"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="name"
                outlined
                color="green"
                placeholder="Nome do Estado"
                label="Nome do Estado"
              >
              </v-text-field>
              <v-text-field
                v-model="idCountry"
                outlined
                color="green"
                placeholder="ID do País"
                label="ID do País"
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
      idCountry: null,
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
          text: 'ID do País',
          value: 'idCountry',
          align: 'center'
        },
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllStates();
  },

  methods: {

    clear() {
      this.name = null;
      this.id = null;
      this.idCountry = null
    },

    update(item) {
      this.name = item.name;
      this.id = item.id;
      this.idCountry = item.idCountry
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          name: this.name,
          idCountry: this.idCountry
        }
        if (this.id) {
          await this.$api.patch(`/api/states/${this.id}`, request);
          this.$toast.success('Estado Editado')
        }else {
          await this.$api.post(`/api/states`, request);
          this.$toast.success('Estado Cadastrado')
        }
        this.name = null;
        this.idCountry = null;
        this.id = null;
        this.dialog = false;
        await this.getAllStates();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllStates() {
      try {
        const response = await this.$api.get('/api/states');
        this.items = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async destroy(item) {
      try {
      await this.$api.delete(`/api/states/${item.id}`);
      await this.getAllStates();
      this.$toast.success('Estado Removido')
    }catch (error){
      this.$toast.error('Erro ao remover Estado')
    }
  },
 }
}
</script>

<style>

</style>