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
      <!-- <v-btn color="primary" class="ma-2" dark @click="dialog = true">Open Dialog 1</v-btn>
      <v-btn color="primary" class="ma-2" dark @click="dialog2 = true">Open Dialog 2</v-btn>
      <v-btn color="primary" class="ma-2" dark @click="dialog3 = true">Open Dialog 3</v-btn>-->
      <v-dialog
        v-model="dialog"
        fullscreen
        hide-overlay
        transition="dialog-bottom-transition"
        scrollable
      >
        <v-card tile>
          <v-toolbar flat dark color="primary">
            <v-btn icon dark @click="dialog = false">
              <v-icon>mdi-close</v-icon>
            </v-btn>
            <v-toolbar-title>Settings</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
              <v-btn dark text @click="dialog = false">Save</v-btn>
            </v-toolbar-items>
            <v-menu bottom right offset-y>
              <template v-slot:activator="{ on }">
                <v-btn dark icon v-on="on">
                  <v-icon>mdi-dots-vertical</v-icon>
                </v-btn>
              </template>
              <v-list>
                <v-list-item v-for="(item, i) in items" :key="i" @click="() => {}">
                  <v-list-item-title>{{ item.title }}</v-list-item-title>
                </v-list-item>
              </v-list>
            </v-menu>
          </v-toolbar>
          <v-card-text>
            <v-container fill-height fluid grid-list-xl>
              <v-layout justify-center wrap>
                <v-flex xs12 md8>
                  <material-card color="green" title="Edit Profile" text="Complete your profile">
                    <v-form>
                      <v-container py-0>
                        <v-layout wrap>
                          <v-flex xs12 md6>
                            <v-text-field label="Expense Claim Description" />
                          </v-flex>
                          <v-flex xs12 md6>
                            <v-text-field class="purple-input" label="Date" />
                          </v-flex>
                        </v-layout>
                      </v-container>
                    </v-form>
                    <v-btn color="primary" dark class="ma-2" @click="dialog2 = !dialog2">Add Item</v-btn>
                  </material-card>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card-text>
          <div style="flex: 1 1 auto;"></div>
        </v-card>
      </v-dialog>

      <v-dialog v-model="dialog2" max-width="800px">
        <v-card>
          <v-card-title>Expense Item</v-card-title>
          <v-form>
            <v-container py-0>
              <v-layout wrap>
                <v-flex xs12 md3>
                  <v-text-field label="Expense Claim Description" />
                </v-flex>
                <v-flex xs12 md3>
                  <v-text-field class="purple-input" label="Date" />
                </v-flex>
                <v-flex xs12 md3>
                  <v-text-field class="purple-input" label="Job ID" />
                </v-flex>
                <v-flex xs12 md3>
                  <v-text-field class="purple-input" label="Cost" />
                </v-flex>
              </v-layout>
            </v-container>
          </v-form>
          <v-card-actions>
            <v-btn color="danger" text @click="dialog2 = false">Close</v-btn>
            <v-btn color="primary" >Save</v-btn>
          </v-card-actions>
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
    dialog: false,
    dialog2: false,
    dialog3: false,
    notifications: false,
    sound: true,
    widgets: false,
    items: [
      {
        title: "Click Me"
      },
      {
        title: "Click Me"
      },
      {
        title: "Click Me"
      },
      {
        title: "Click Me 2"
      }
    ],
    select: [
      { text: "State 1" },
      { text: "State 2" },
      { text: "State 3" },
      { text: "State 4" },
      { text: "State 5" },
      { text: "State 6" },
      { text: "State 7" }
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
    }
  }
};
</script>