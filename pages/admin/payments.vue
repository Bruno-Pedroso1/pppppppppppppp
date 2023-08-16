<!-- eslint-disable vue/valid-v-slot -->
<!-- eslint-disable vue/v-slot-style -->
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="d-flex align-center flex-column">
          Cadastro de Pagamentos
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
                v-model="totalValue"
                outlined
                color="green"
                placeholder="Valor Total"
                label="Valor Total"
              >
              </v-text-field>
              <v-text-field
                v-model="status"
                outlined
                color="green"
                placeholder="Status"
                label="Status"
              >
              </v-text-field>
              <v-autocomplete
                v-model="selectedPaymentMethod"
                outlined
                :items="paymethod"
                item-text="type"
                item-value="id"
                color="green"
                placeholder="ID do Método de Pagamento"
                label="ID do Método de Pagamento"
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
      status: null,
      paymethod: [],
      selectedPaymentMethod: null,
      totalValue: null,
      headers: [
        {
          text: 'ID',
          value: 'id',
          align: 'center'
        },
        {
          text: 'Status',
          value: 'status',
          align: 'center'
        },
        {
          text: 'Valor Total',
          value: 'totalValue',
          align: 'center'
        },
        {
          text: 'ID Método de Pagamento',
          value: 'idPaymentMethod',
          field:  'id_paymentMethod',
          align: 'center'
        },
        { text: "", value: "actions", filterable: false},
      ]
    }
  },
  async created() {
    await this.getAllPayments();
    await this.getAllPaymentM();
  },

  methods: {

    clear() {
      this.status = null;
      this.totalValue = null;
      this.selectedPaymentMethod = null;
      this.id = null;
    },

    update(item) {
      this.status = item.status;
      this.totalValue = item.totalValue;
      this.selectedPaymentMethod = item.idPaymentMethod
      this.id = item.id;
      this.dialog = true;
    },

    async persist() {
      try {
        const request = {
          status: this.status,
          totalValue: this.totalValue,
          idPaymentMethod: this.selectedPaymentMethod
        }
        if (this.id) {
          await this.$api.patch(`/api/payments/${this.id}`, request);
          this.$toast.success('Pagamento Editado')
        }else {
          await this.$api.post(`/api/payments`, request);
          this.$toast.success('Pagamento Cadastrado')
        }
        this.status = null;
        this.totalValue = null;
        this.selectedPaymentMethod = null;
        this.id = null;
        this.dialog = false;
        await this.getAllPayments();
      } catch (error) {
        this.$toast.error('Erro')
      }
    },

    async getAllPayments() {
      try {
        const response = await this.$api.get('/api/payments');
        this.items = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },

    async getAllPaymentM() {
      try {
        const response = await this.$api.get('/api/payment-methods');
        this.paymethod = response;
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    async destroy(item) {
      try {
      await this.$api.delete(`/api/payments/${item.id}`);
      await this.getAllCountries();
      this.$toast.success('Pagamento Removido')
    }catch (error){
      this.$toast.error('Erro ao remover Pagamento')
    }
  },
 }
}
</script>

<style>

</style>