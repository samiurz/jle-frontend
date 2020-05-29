<template>
  <v-container fill-height fluid grid-list-xl>
    <v-layout justify-center wrap>
      <v-flex md12>
        <material-card color="green" title="gl information" text>
          <v-tooltip bottom>
            <template v-slot:activator="{ on }">
              <v-btn
                class="mx-2"
                absolute
                fab
                top
                right
                color="primary"
                v-on="on"
                @click="addItem()"
              >
                <v-icon dark>mdi-plus</v-icon>
              </v-btn>
            </template>
            <span>
              <font color="white">Add New GL Code</font>
            </span>
          </v-tooltip>
          {{pagination}}
          <v-data-table
            :headers="headers"
            :items="gls"
            :options.sync="pagination"
            :loading="loading"
            :server-items-length="totalItems"
            :footer-props="{
                                showFirstLastPage: true,
                                firstIcon: 'mdi-arrow-collapse-left',
                                lastIcon: 'mdi-arrow-collapse-right',
                                prevIcon: 'mdi-minus',
                                nextIcon: 'mdi-plus'
                            }"
            @update:options="updatePagination"
          >
            <template slot="headerCell" slot-scope="{ header }">
              <span
                class="subheading font-weight-light text-success text--darken-3"
                v-text="header.text"
              />
            </template>
            <template slot="items" slot-scope="{ item }">
              <td>{{ item.name }}</td>
              <td>{{ item.code }}</td>
              <td align="center">
                <v-tooltip top content-class="top">
                  <v-btn
                    slot="activator"
                    class="v-btn--simple"
                    color="success"
                    icon
                    @click="editItem(item)"
                  >
                    <v-icon color="primary">mdi-pencil</v-icon>
                  </v-btn>
                  <span>
                    <font color="white">Edit GL Code Information</font>
                  </span>
                </v-tooltip>
                <v-tooltip top content-class="top">
                  <v-btn
                    slot="activator"
                    class="v-btn--simple"
                    color="danger"
                    icon
                    @click="deleteItem(item)"
                  >
                    <v-icon color="error">mdi-close</v-icon>
                  </v-btn>
                  <span>
                    <font color="white">Remove GL Code</font>
                  </span>
                </v-tooltip>
              </td>
            </template>
          </v-data-table>
        </material-card>
      </v-flex>
    </v-layout>
    <v-row justify="center">
      <v-dialog v-model="dialog" fullscreen hide-overlay transition="dialog-bottom-transition">
        <v-card>
          <v-toolbar dark color="primary">
            <v-btn icon dark @click="dialog = false">
              <v-icon>mdi-close</v-icon>
            </v-btn>
            <v-toolbar-title>Settings</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
              <v-btn dark text @click="dialog = false">Save</v-btn>
            </v-toolbar-items>
          </v-toolbar>
          <v-container fill-height fluid grid-list-xl>
            <v-layout justify-center wrap>
              <v-flex xs12 md12>
                <material-card color="green" title="Edit Profile" text="Complete your profile">
                  <v-form>
                    <v-container py-0>
                      <v-layout wrap>
                        <v-flex xs12 md6>
                          <v-text-field label="Expense Claim Details" />
                        </v-flex>
                        <v-flex xs12 md6>
                          <v-text-field class="purple-input" label="Date" />
                        </v-flex>
                      </v-layout>
                    </v-container>
                  </v-form>
                  <v-flex md12>
                      <v-data-table :headers="headers" :items="items" hide-actions>
                        <template slot="headerCell" slot-scope="{ header }">
                          <span
                            class="subheading font-weight-light text-success text--darken-3"
                            v-text="header.text"
                          />
                        </template>
                        <template slot="items" slot-scope="{ item }">
                          <td>
                            <input v-model="item.name" />
                          </td>
                          <td>
                            <input v-model="item.description" />
                          </td>
                          <td>
                            <input v-model="item.date" />
                          </td>
                          <td>
                            <input v-model="item.unit_cost" />
                          </td>
                          <td class="hide-print">
                            <button class="btn-floating" v-on:click="remove_item(index)">X</button>
                          </td>
                        </template>
                      </v-data-table>
                      <v-btn class="ma-2" v-on:click="add_item" color="dark" dark>
                        <v-icon></v-icon>Add More
                      </v-btn>
                  </v-flex>
                </material-card>
              </v-flex>
              <v-flex xs12 md4></v-flex>
            </v-layout>
          </v-container>
        </v-card>
      </v-dialog>
    </v-row>
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
  mounted() {
    this.fetch();
  },

  data: () => ({
    headers: [
      {
        sortable: true,
        text: "GL Code Name",
        value: "name"
      },
      {
        sortable: true,
        text: "GL Code",
        value: "code"
      },
      {
        sortable: false,
        text: "Action",
        align: "center"
      }
    ],
    pagination: {},
    loading: false,
    totalItems: 0,
    sort: "",
    dialog: false,
    gls: [],
    gl: {
      name: "",
      code: ""
    },
    valid: false,
    editMode: false,
    validationRules: {
      name: [
        v => !!v || "GL Code Name is required",
        v =>
          (v && v.length <= 20) ||
          "GL Code Name must be less than 20 characters"
      ],
      code: [
        v => !!v || "GL Code Name is required",
        v =>
          (v && v.length <= 20) ||
          "GL Code Name must be less than 20 characters"
      ]
    },
    gls: [],
    amount_due: 0,
    items: [
      {
        name: "",
        description: "",
        date: "",
        unit_cost: ""
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
        value: ""
      },
      {
        sortable: false,
        text: "Cost",
        value: ""
      },
      {
        sortable: false,
        text: "Remove",
        value: ""
      }
    ]
  }),
  methods: {
    updatePagination() {},
    addItem() {
      this.gl = {};
      this.dialog = true;
      this.loadDependencies();
    },
    async loadDependencies() {
      try {
        this.loading = true;
        const response = await this.$axios.get("/api/v1/gls-dependencies");
        this.gls = response.data.response.gls.data;
        this.loading = false;
      } catch (e) {
        console.log(e);
        this.loading = false;
      }
    },
    async create() {
      try {
        this.loading = true;
        const response = await this.$axios.post("/api/v1/gls", this.gl);
        this.fetch();
        this.dialog = false;
        this.loading = false;
      } catch (e) {
        console.log(e);
        this.loading = false;
      }
    },
    async update() {
      try {
        this.loading = true;
        const response = await this.$axios.put(
          `/api/v1/gls/${this.gl.id}`,
          this.gl
        );
        this.fetch();
        this.dialog = false;
        this.loading = false;
      } catch (e) {
        console.log(e);
        this.loading = false;
      }
    },
    validate() {
      this.$refs.form.validate();
      this.$nextTick(() => {
        if (this.valid) {
          console.log(this.valid);
          if (this.editMode) {
            this.update();
            return;
          }
          this.create();
        }
      });
    },
    async fetch() {
      try {
        this.loading = true;
        const baseURI = `/api/v1/gls`;
        const response = await this.$axios.get(baseURI);
        this.gls = response.data.response.data.rows;
        this.totalItems = response.data.response.data.total_rows;
        console.log(response.data.response.data.total_rows);
        this.loading = false;
      } catch (e) {
        console.log(e);
        this.loading = false;
      }
    },
    editItem(item) {
      this.editMode = true;
      this.gl = Object.assign({}, item);
      this.dialog = true;
      this.loadDependencies();
    },
    async deleteItem(item) {
      try {
        this.loading = true;
        const response = await this.$axios.delete(`/api/v1/gls/${item.id}`);
        this.fetch();
        this.loading = false;
      } catch (e) {
        console.log(e);
        this.loading = false;
      }
    },
    updatePagination(pagination) {
      if (pagination.sortBy.length) {
        this.sort = `${pagination.sortBy[0]} ${
          pagination.sortDesc[0] == true ? "desc" : "asc"
        }`;
      }
      this.fetchItems();
    },
    format_price: number => {
      return "$" + parseFloat(number).toFixed(2);
    },
    add_item: function() {
      this.items.push({
        name: "",
        description: "",
        date: "",
        unit_cost: ""
      });
    },
    update_value: function(index, event, property) {
      this.items[index][property] = event.target.innerText;
    },
    remove_item: function(index) {
      this.items.splice(index, 1);
    }
  },
}
</script>
