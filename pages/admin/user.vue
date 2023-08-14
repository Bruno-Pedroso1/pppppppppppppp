<!-- eslint-disable vue/valid-v-slot -->
<!-- eslint-disable vue/v-slot-style -->
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="d-flex align-center flex-column">
          Cadastro de Usuario
        </h1>
        <v-row class="d-flex align-center flex column">
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
                placeholder="ID do Usuario"
                label="ID do Usuario"
              >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="taxIdentificationNumber"
                v-mask="['###.###.###-##', '##.###.###/####-##']"
                outlined
                color="green"
                placeholder="###.###.###-##"
                label="CPF"
              >
              </v-text-field>
              <v-text-field
              v-model="phone"
              v-mask="['(##) ####-####', '(##) #####-####']"
                outlined
                color="green"
                placeholder="(##)#####-####"
                label="Telefone"
              >
              </v-text-field> 
              <v-text-field
                v-model="email"
                outlined
                color="green"
                placeholder="E-mail"
                label="E-mail"
              >
              </v-text-field>
              <v-text-field
                v-model="name"
                outlined
                color="green"
                placeholder="Nome do Usuario"
                label="Nome do Usuario"
              >
              </v-text-field>
              <v-autocomplete
                v-model="role"
                :items="rolea"
                outlined
                color="green"
                placeholder="Função"
                label="Função"
              >
              </v-autocomplete>
              <v-text-field
                v-model="passwordHash"
                outlined
                color="green"
                placeholder="Password Hash"
                label="Password Hash"
              >
            </v-text-field>
            <v-text-field
                v-model="token"
                outlined
                color="green"
                placeholder="Token"
                label="Token"
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
      rolea: ['admin', 'customer'],
      search: null,
      items: [],
      dialog: false,
      email: null,
      id: null,
      taxIdentificationNumber: null,
      name: null,
      phone: null,
      token: null,
      passwordHash: null,
      role: null,
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
          text: 'Telefone',
          value: 'phone',
          align: 'center'
        },
        {
          text: 'E-mail',
          value: 'email',
          align: 'center'
        },
        {
          text: 'CPF',
          value: 'taxIdentificationNumber',
          align: 'center'
        },
        {
          text: 'Função',
          value: 'role',
          align: 'center'
        },
        {
          text: 'Password Hash',
          value: 'passwordHash',
          align: 'center'
        },
        {
          text: 'Token',
          value: 'token',
          align: 'center'
        },
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllUsers();
  },

  methods: {

    clear() {
      this.name = null;
      this.id = null;
      this.taxIdentificationNumber = null;
      this.phone = null;
      this.email = null;
      this.role = null;
      this.passwordHash = null;
      this.token = null;
    },
    
    update(item) {
      this.name = item.name;
      this.id = item.id;
      this.taxIdentificationNumber = item.taxIdentificationNumber;
      this.phone = item.phone;
      this.email = item.email;
      this.role = item.role;
      this.passwordHash = item.passwordHash;
      this.token = item.token;
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          name: this.name,
          taxIdentificationNumber: this.taxIdentificationNumber,
          email: this.email,
          phone: this.phone,
          role: this.role,
          passwordHash: this.passwordHash,
          token: this.token,
        }
        if (this.id) {
          await this.$api.patch(`/api/users/${this.id}`, request);
          this.$toast.success('Usuario Editado')
        }else {
          await this.$api.post(`/api/users/`, request);
          this.$toast.success('Usuario Cadastrado')
        }
        this.dialog = false;
        await this.getAllUsers();
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async getAllUsers() {
      try {
        const response = await this.$api.get('/api/users');
        this.items = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async destroy(item) {
      try {
      await this.$api.delete(`/api/users/${item.id}`);
      await this.getAllUsers();
      this.$toast.success('Usuario Removido')
    }catch (error){
      this.$toast.error('Erro ao remover usuario')
    }
  },
}
}
</script>

<style>

</style>