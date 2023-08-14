<template>
  <v-container class="d-flex justify-center text-center mt-15">
    <v-form 
    @submit.prevent="logna" 
    >
    <h1 
      style="height: 10%;"
      mt-5
    >
    Faça seu login 
    </h1>
    <v-text-field
    v-model="login.email"
    class="mt-10"
      label="E-mail"
      outlined
      required
      placeholder="E-mail"
      color="red"
      prepend-inner-icon="mdi-email"
    >
    </v-text-field>
    <div>
      <v-form
      @submit.prevent="logna"
      >
        <v-row>
          <v-col>
            <v-text-field 
              v-model="login.password"
              label="Senha"
              style="margin-top: -8%"
              solo
              outlined
              placeholder="Senha"
              color="red"
              :append-icon="show ? 'mdi-eye-off' : 'mdi-eye'"
              :type="show ? 'text' : 'password'"
              prepend-inner-icon="mdi-lock"
              @click:append="toggleShow"
            >
            </v-text-field>
            <v-btn 
              class="mr-3"
              @click="logna"
            >
              Entrar
            </v-btn>
            <v-btn
              href="/cadastro"
              >Cadastrar 
            </v-btn>
          </v-col>
        </v-row>
      </v-form>
      <v-btn 
        class="mt-5"
        href="/"
      >
        Voltar a página inicial
      </v-btn>
  </div>
    </v-form>
  </v-container>
</template>

<script>
export default {
  name: 'Login',
  layout: 'login',
  data(){
    return{
      valid: false,
      show:false,
      login:{
        email: null,
        password: null
      },
      rule: [
        v => !!v || 'Esse campo é obrigatorio'
      ]
    }
  },
  methods:{
    async logna() {
      const forget = {
        email: this.login.email,
        password: this.login.password
      }
      try {
        const response = await this.$api.post('/api/users/login', forget);
        // eslint-disable-next-line eqeqeq
        if(response.type == "success"){
          localStorage.setItem("forget-key", response.data.token);
          this.$toast.success(response.message);
          this.$router.push("/");
        }else{
          this.$toast.error(response.data.message)
        }
      } catch (error) {
        this.$toast.error('Usuario ou senha incorretos');
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