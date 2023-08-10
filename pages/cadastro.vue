<template>
  <v-container>
    <v-form
      v-model="valid"
      @submit.prevent="register"
    >
    <v-row>
      <v-col>
        <v-row
          class="text-center"
        >
          <v-col>
            <h1
              style="font-size: 60px;"
            >
              Cadastre-se
            </h1>
          </v-col>
        </v-row>
        <v-row
          class="justify-center"
        >
        </v-row>
      </v-col>
      <v-col>
        <v-card>
          <v-row>
            <v-col>
              <v-text-field
                v-model="user.name"
                outlined
                label="Nome Completo"
                style="margin-top: -8%"
                placeholder="Nome Completo"
                color="red"
                prepend-inner-icon="mdi-email"
                :rules="[rule.password]"
                @keyup.enter="register"
              >
              </v-text-field>
                <v-text-field
                  v-model="user.taxIdentificationNumber"
                  v-mask="['###.###.###-##']"
                  outlined
                  label="CPF"
                  placeholder="CPF"
                  color="red"
                  prepend-inner-icon="mdi-lock"
                  :rules="[rule.password]"
                  @keyup.enter="register"
                >
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="user.phone"
                v-mask="['(##)#####-####', '(##) ####-####']"
                outlined
                label="Celular"
                style="margin-top: -8%"
                placeholder="Celular"
                color="red"
                prepend-inner-icon="mdi-cellphone"
                :rules="[rule.password]"
                @keyup.enter="register"
              >
              </v-text-field>
            <v-text-field
              v-model="user.email"
              outlined
              type="email"
              label="Email"
              placeholder="Email"
              color="red"
              prepend-inner-icon="mdi-mail"
              :rules="[rule.password]"
              @keyup.enter="register"
            >
          </v-text-field>
        </v-col>
      </v-row>
      <v-col>
        <v-autocomplete
              v-model="user.role"
              block
              :items="rolea"
              outlined
              type="role"
              label="role"
              placeholder="role"
              color="red"
              prepend-inner-icon="mdi-mail"
              :rules="[rule.password]"
              @keyup.enter="register"
            >
          </v-autocomplete>
        <v-text-field
          v-model="user.password"
          outlined
          label="Senha"
          placeholder="Senha"
          color="red"
          prepend-inner-icon="mdi-lock"
          :append-icon="show ? 'mdi-eye-off' : 'mdi-eye'"
          :type="show ? 'text' : 'password'"
          :rules="[rule.password]"
          @keyup.enter="register"
          @click:append="toggleShow"
        >
        </v-text-field>
    </v-col>  
    <v-col>
      <v-btn
        block
        color="red"
        href="/login"
      >
    Já sou cadastrado
  </v-btn>
    </v-col>
      <v-col>
        <v-btn
          block
          color="green"
          @click="register"
        >
          Cadastrar
        </v-btn>
        </v-col>
      </v-card>
      </v-col>
    </v-row>
  </v-form>
  </v-container>
</template>

<script>

export default {
  name: 'LoginsPage',
  data(){
    return{
      valid: false,
      rolea: ['admin','customer'],
      show: false,
      show2:false,
      user:{
        email: null,
        name: null,
        phone: null,
        password: null,
        role: 'customer',
        taxIdentificationNumber:null,
      },
      rule:{
        password: v => !!v || 'Esse campo é obrigatorio',
      },
    }
  },
  methods: {
    async register (){
      try {
        if(!this.valid){
          return this.$toast.warning("Infome todas as opções!")
        }
        const user ={
          email: this.user.email,
          name: this.user.name,
          phone: this.user.phone,
          password: this.user.password,
          role: this.user.role,
          taxIdentificationNumber: this.user.taxIdentificationNumber,
        }
        const response = await this.$axios.$post('http://localhost:3333/api/users', user)
        // eslint-disable-next-line eqeqeq
        if(response.type == "success"){
          this.$toast.success(response.message)
          this.$router.push({ name: 'login' });
        }
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    toggleShow(){
      this.show = !this.show
    },
  }
}

</script>

<style>

</style>