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
  methods: {
    searchUser () {},
    open () {},
    search () {
      this.$axios
        .get('/search?amount=' + this.amount)
        .then((response) => {
          console.log(response)
          this.products = response.data
        })
        .catch((error) => {
          console.log(error)
        })
    }
  }
}
</script>
