<template>
  <v-container fill-height fluid grid-list-xl>
    <v-layout justify-center wrap>
      <v-flex md12>
        <material-card
          color="green"
          title="Expense Claim Form"
          text="Please add your expense description separately"
        >

         <v-data-table :headers="headers" :items="items" hide-actions>
            <template slot="headerCell" slot-scope="{ header }">
              <span
                class="subheading font-weight-light text-success text--darken-3"
                v-text="header.text"
              />
            </template>
            <template slot="items" slot-scope="{ item }">
                  <td><input v-model="item.name" /></td>
                  <td><input v-model="item.description" /></td>
                  <td><input v-model="item.date" /></td>
                  <td><input v-model="item.unit_cost" /></td>
                  <td class="hide-print"><button class="btn-floating" v-on:click="remove_item(index)">X</button></td>
            </template>
          </v-data-table>
          <v-btn class="ma-2" v-on:click="add_item" color="dark" dark>
              <v-icon></v-icon> Add More
          </v-btn>
        </material-card>
      </v-flex>
        <v-flex xs12 md12>
        <material-card
          color="green"
          title="Documents"
          text="Please upload your supporting documents"
        >
          <v-form>
            <v-container py-0>
              <v-layout wrap>
                <v-flex xs12 md6>
                  <v-file-input chips multiple label="File input w/ chips"></v-file-input>
                </v-flex>
                <v-flex xs12 text-xs-right>
                  <v-btn class="mx-0 font-weight-light" color="success">Submit Expense</v-btn>
                  <v-btn class="mx-0 font-weight-light" color="info">Approve Expense Claim</v-btn>
                </v-flex>
              </v-layout>
            </v-container>
          </v-form>
        </material-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import { mapGetters } from "vuex";
import materialCard from "~/components/material/AppCard";

export default {
  layout: "dashboard",
  components: {
    materialCard
  },

  data: () => ({
    picker: new Date().toISOString().substr(0, 10),
    amount_due: 0,
    items: [
      {
        name: '',
        description: '',
        date:'',
        unit_cost: '',
      }
    ],
    headers: [
      {
        sortable: false,
        text: "Item",
        value: "name"
      },
      {
        sortable: false,
        text: "Description",
        value: "description"
      },
      {
        sortable: false,
        text: "Date",
        value: "",
      },
      {
        sortable: false,
        text: "Cost",
        value: "",
      },
      {
        sortable: false,
        text: "Remove",
        value: "",
      }
    ],
  }),
  methods: {
    format_price: (number) => {
      return '$' + parseFloat(number).toFixed(2)
    },
    add_item: function () {
      this.items.push({
        name: '',
        description: '',
        date:'',
        unit_cost: '',
      })
    },
    update_value: function (index, event, property) {
      this.items[index][property] = event.target.innerText
    },
    remove_item: function (index) {
      this.items.splice(index, 1)
    }
  },
  computed: {
    ...mapGetters({
      user: "user/getUser",
      fullname: "user/getFullname"
    })
  }
}
</script>
