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
                placeholder="ID do País"
                label="ID do País"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="name"
                outlined
                color="green"
                placeholder="Nome do País"
                label="Nome do País"
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
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllCountries();
  },

  methods: {

    clear() {
      this.name = null;
      this.id = null;
    },

    update(item) {
      this.name = item.name;
      this.id = item.id;
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          name: this.name,
        }
        if (this.id) {
          await this.$api.patch(`/api/countries/${this.id}`, request);
          this.$toast.success('País Editado')
        }else {
          await this.$api.post(`/api/countries`, request);
          this.$toast.success('País Cadastrado')
        }
        this.name = null;
        this.id = null;
        this.dialog = false;
        await this.getAllCountries();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllCountries() {
      try {
        const response = await this.$api.get('/api/countries');
        this.items = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async destroy(item) {
      try {
      await this.$api.delete(`/api/countries/${item.id}`);
      await this.getAllCountries();
      this.$toast.success('País Removido')
    }catch (error){
      this.$toast.error('Erro ao remover País')
    }
  },
 }
}
</script>

<style>

</style>