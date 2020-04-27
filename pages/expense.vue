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
              <td>{{ item.job_code }}</td>
              <td>{{ item.description }}</td>
              <td>{{ item.date }}</td>
              <td class="text-xs-right">{{ item.cost }}</td>
            </template>
          </v-data-table>
        </material-card>
        <v-form>
          <v-flex xs12 md12>
            <p class="text-xs-right">
              Incl. GST: $500.00 
            </p>
            <p class="text-xs-right">
              Excl. GST: $425.00 
            </p>
          </v-flex>
        </v-form>
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
    headers: [
      {
        sortable: false,
        text: "Job Code",
        value: "job_code"
      },
      {
        sortable: false,
        text: "Description",
        value: "description"
      },
      {
        sortable: false,
        text: "Date",
        value: "date"
      },
      {
        sortable: false,
        text: "Cost",
        value: "cost",
        align: "right"
      }
    ],
    items: [
      {
        job_code: "001",
        description: "Nestle Development work",
        date: "27-02-2020",
        cost: "$35,738"
      },
      {
        job_code: "002",
        description: "Holcim Cement",
        date: "27-03-2020",
        cost: "$23,738"
      },
      {
        job_code: "003",
        description: "Devenport",
        date: "27-04-2020",
        cost: "$56,142"
      },
      {
        job_code: "004",
        description: "Uber",
        date: "27-05-2020",
        cost: "$38,735"
      }
    ]
  }),

  computed: {
    ...mapGetters({
      user: "user/getUser",
      fullname: "user/getFullname"
    })
  }
};
</script>
