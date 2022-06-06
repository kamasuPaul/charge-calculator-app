<template>
  <v-container fluid>
    <v-row justify="center" align="center">
      <v-col cols="12" sm="8" md="6">
        <v-card>
          <v-text-field
            v-model="amount"
            label="Enter amount"
            filled
            @change="search"
            @keyup="search"
          />
          <v-data-table
            v-model="selectedUsers"
            show-select
            :headers="headers"
            :items="products"
            :search="searchQuery"
            class="flex-grow-1"
          >
            <template #[`item.name`]="{ item }">
              <div class="d-flex align-center py-1">
                <div class="ml-1 caption font-weight-bold">
                  {{ item.name }}
                </div>
              </div>
            </template>

            <template #[`item.from`]="{ item }">
              {{ Intl.NumberFormat().format(item.from) }}
            </template>
            <template #[`item.to`]="{ item }">
              {{ Intl.NumberFormat().format(item.to) }}
            </template>
            <template #[`item.withdraw_charge`]="{ item }">
              {{ Intl.NumberFormat().format(item.withdraw_charge) }}
            </template>
            <template #[`item.sending_charge`]="{ item }">
              {{ Intl.NumberFormat().format(item.sending_charge) }}
            </template>
          </v-data-table>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'IndexPage',
  data () {
    return {
      amount: '',
      searchQuery: '',
      selectedUsers: [],
      headers: [
        { text: 'Product', value: 'name' },
        { text: 'From (UGX)', value: 'from' },
        { text: 'To (UGX)', align: 'left', value: 'to' },
        { text: 'Withdraw Charge (UGX)', value: 'withdraw_charge', sortable: true },
        { text: 'Sending Charge (UGX)', value: 'sending_charge', sortable: true }
      ],
      products: [
      ]
    }
  },
  watch: {
    amount (val) {
      this.amount = val.replace(/[^0-9.]/g, '')
    }
  },
  mounted () {
    this.fetchAllProducts()
  },
  methods: {
    fetchAllProducts () {
      this.$axios.get('/products')
        .then((response) => {
          const products = response.data
          // foreach product, create an object with the following properties
          // name, from, to, withdraw_charge, sending_charge
          products.forEach((product) => {
            // get product transaction_bands
            const transactionBands = product.transaction_bands
            transactionBands.forEach((transactionBand) => {
              this.products.push({
                name: product.name,
                from: transactionBand.from,
                to: transactionBand.to,
                withdraw_charge: transactionBand.withdraw_charge,
                sending_charge: transactionBand.sending_charge
              })
            })
          })
        })
        .catch((error) => {
          console.log(error)
        })
        // sort the products array by from
        .finally(() => {
          this.products.sort((a, b) => {
            return a.from - b.from
          })
        })
    },
    search () {
      // if amount is empty, return all products
      if (this.amount === '') {
        this.products = []
        this.fetchAllProducts()
        return
      }
      this.$axios
        .get('/search?amount=' + this.amount)
        .then((response) => {
          this.products = response.data
        })
        .catch((error) => {
          console.log(error)
        })
        // sort the products array by withdraw_charge
        .finally(() => {
          this.products.sort((a, b) => {
            return a.withdraw_charge < b.withdraw_charge
          })
        })
    }
  }
}
</script>
