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
            <!-- <template #item.id="{ item }">
          <div class="font-weight-bold">
            # {{ item.id }}
          </div>
        </template>

        <template #item="{ item }">
          <div class="d-flex align-center py-1">
            <div class="ml-1 caption font-weight-bold">
              {{ item.email }}
            </div>
          </div>
        </template>

        <template #item.verified="{ item }">
          <v-icon v-if="item.verified" small color="success">
            mdi-check-circle
          </v-icon>
          <v-icon v-else small>
            mdi-circle-outline
          </v-icon>
        </template>

        <template #item.disabled="{ item }">
          <div>{{ item.disabled.toString() | capitalize }}</div>
        </template>

        <template #item.role="{ item }">
          <v-chip
            label
            small
            class="font-weight-bold"
            :color="item.role === 'ADMIN' ? 'primary' : undefined"
          >
            {{ item.role | capitalize }}
          </v-chip>
        </template>

        <template #item.created="{ item }">
          <div>{{ item.created }}</div>
        </template>

        <template #item.lastSignIn="{ item }">
          <div>{{ item.lastSignIn }}</div>
        </template>

        <template #item.action="{}">
          <div class="actions">
            <v-btn icon to="#">
              <v-icon>mdi-open-in-new</v-icon>
            </v-btn>
          </div>
        </template> -->
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
        { text: 'From', value: 'from' },
        { text: 'To', align: 'left', value: 'to' },
        { text: 'Withdraw Charge', value: 'withdraw_charge', sortable: true },
        { text: 'Sending Charge', value: 'sending_charge', sortable: true }
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
