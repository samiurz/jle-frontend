<template>
    <v-container fill-height fluid grid-list-xl>
        <v-layout justify-center wrap>
            <v-flex md12>
                <material-card
                    color="green"
                    title="Employee information"
                    text=""
                >
                    <v-tooltip bottom>
                        <template v-slot:activator="{ on }">
                            <v-btn class="mx-2"
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
                        <span><font color="white">Add New Employee</font></span>
                    </v-tooltip>
                    {{pagination}}
                    <v-data-table
                        :headers="headers"
                        :items="users"
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
                            <td>{{ item.full_name }}</td>
                            <td>{{ item.email }}</td>
                            <td>{{ item.branch.name }}</td>
                            <td>{{ item.role.name }}</td>
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
                                    <span><font color="white">Edit Employee Information</font></span>
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
                                    <span><font color="white">Remove Employee Information</font></span>
                                </v-tooltip>
                            </td>
                        </template>
                    </v-data-table>
                </material-card>
            </v-flex>
        </v-layout>
    <v-row justify="center">
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Employee Profile</span>
        </v-card-title>
        <v-card-text>
        
         <v-form
            ref="form"
            v-model="valid"
            lazy-validation
            @submit.prevent="validate()"
            @keyup.enter.native="validate()"
            @keyup.esc.native="dialog = false"
        >
          <v-container>
            <v-row>
                <v-col cols="12" sm="6" md="3">
                <v-date-picker v-model="dates" range></v-date-picker>
                </v-col>
            </v-row>
            <v-row>
                <v-col cols="12" sm="6" md="3">
                 <v-text-field label="Regular"></v-text-field>
                </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                <v-select
                    item-text="name"
                    item-value="id"
                    :items="branches"
                    label="Branch"
                    v-model="user.branch_id"
                    :rules="validationRules.branch"
                ></v-select>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                    label="Full Name"
                    v-model="user.full_name"
                    class="purple-input"
                    :rules="validationRules.full_name"
                />
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                    class="purple-input"
                    v-model="user.username"
                    label="User Name"
                    :rules="validationRules.username"
                />
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-select
                    item-text="name"
                    item-value="id"
                    :items="roles"
                    label="Role"
                    v-model="user.role_id"
                    :rules="validationRules.role"
                ></v-select>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                 <v-text-field
                    label="Email Address"
                    class="purple-input"
                    v-model="user.email"
                    :rules="validationRules.email"
                />
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                    label="Password"
                    type="password"
                    class="purple-input"
                    v-model="user.password"
                    :rules="validationRules.password"
                />
              </v-col>
            </v-row>
            <v-card-actions>
            <v-spacer></v-spacer>
                <v-btn class="ma-2" @click="dialog = false" color="orange darken-2" dark>
                    <v-icon dark left>mdi-cancel</v-icon>Close
                </v-btn>
                <v-btn class="ma-2" type="submit" color="primary" dark>Save
                    <v-icon dark right>mdi-checkbox-marked-circle</v-icon>
                </v-btn>
            </v-card-actions>
          </v-container>
         </v-form>
        </v-card-text>
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
        this.fetch()
    },

    data: () => ({
        headers: [
            {
                sortable: false,
                text: "Full Name",
                value: "full_name",
            },
            {
                sortable: true,
                text: "Email",
                value: "email"
            },
            {
                sortable: true,
                text: "Role",
                value: "role.name"
            },
            {
                sortable: true,
                text: "Branch",
                value: "branch.name",
            },
            {
                sortable: false,
                text: "Action",
                align: "center"
            }
        ],
        items: [
            {
                name: "Dakota Rice",
                country: "Niger",
                city: "Oud-Tunrhout",
                salary: "$35,738"
            },
            {
                name: "Minerva Hooper",
                country: "Curaçao",
                city: "Sinaai-Waas",
                salary: "$23,738"
            },
            {
                name: "Sage Rodriguez",
                country: "Netherlands",
                city: "Overland Park",
                salary: "$56,142"
            },
            {
                name: "Philip Chanley",
                country: "Korea, South",
                city: "Gloucester",
                salary: "$38,735"
            },
            {
                name: "Doris Greene",
                country: "Malawi",
                city: "Feldkirchen in Kārnten",
                salary: "$63,542"
            },
            {
                name: "Mason Porter",
                country: "Chile",
                city: "Gloucester",
                salary: "$78,615"
            }
        ],
        pagination: {},
        loading: false,
        totalItems: 0,
        sort: '',
        dialog: false,
        branches: [],
        roles: [],
        user: {
            full_name: '',
            username: '',
            email: '',
            password: '',
            branch_id: '',
            role_id: '',
        },
        valid: false,
        editMode: false,
        validationRules: {
            full_name: [
                v => !!v || "Name is required",
                v =>
                    (v && v.length <= 20) ||
                    "Name must be less than 20 characters"
            ],
            username: [
                v => !!v || "Name is required",
                v =>
                    (v && v.length <= 10) ||
                    "Name must be less than 20 characters"
            ],
            email: [
                v => !!v || "Email is required",
                v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
            ],
            password: [
                v => !!v || "Password is required",
                v =>
                    (v && v.length <= 10) ||
                    "Name must be less than 10 characters"
            ],
            branch: [
                v => !!v || "Branch is required",
                v =>
                    (v && v > 0) ||
                    "Branch must be selected"
            ],
            role: [
                v => !!v || "Role is required",
                v =>
                    (v && v > 0) ||
                    "Role must be selected"
            ],
        },
        users: []
    }),
    methods: {
        updatePagination() {
        },
        addItem() {
            this.user = {}
            this.dialog = true
            this.loadDependencies()
        },
        async loadDependencies() {
            try {
                this.loading = true
                const response = await this.$axios.get('/api/v1/users-dependencies')
                this.branches = response.data.response.branches.data
                this.roles = response.data.response.roles.data
                this.loading = false

            } catch (e) {
                console.log(e)
                this.loading = false
            }
        },
        async create() {
            try {
                this.loading = true
                const response = await this.$axios.post('/api/v1/users', this.user)
                this.fetch()
                this.dialog = false
                this.loading = false

            } catch (e) {
                console.log(e)
                this.loading = false
            }
        },
        async update() {
            try {
                this.loading = true
                const response = await this.$axios.put(`/api/v1/users/${this.user.id}`, this.user)
                this.fetch()
                this.dialog = false
                this.loading = false

            } catch (e) {
                console.log(e)
                this.loading = false
            }
        },
        validate() {
            this.$refs.form.validate()
            this.$nextTick(() => {
                if (this.valid) {
                    console.log(this.valid)
                    if (this.editMode) {
                        this.update()
                        return
                    }
                    this.create()
                }
            })
        },
        async fetch() {
            try {
                this.loading = true
                const baseURI = `/api/v1/users`
                const response = await this.$axios.get(baseURI)
                this.users = response.data.response.data.rows
                this.totalItems = response.data.response.data.total_rows
                console.log(response.data.response.data.total_rows)
                this.loading = false

            } catch (e) {
                console.log(e)
                this.loading = false
            }
        },
        editItem(item) {
            this.editMode = true
            this.user = Object.assign({}, item)
            this.dialog = true
            this.loadDependencies()
        },
        async deleteItem(item) {
            try {
                this.loading = true
                const response = await this.$axios.delete(`/api/v1/users/${item.id}`)
                this.fetch()
                this.loading = false

            } catch (e) {
                console.log(e)
                this.loading = false
            }
        },
        updatePagination(pagination) {
            if (pagination.sortBy.length) {
                this.sort = `${pagination.sortBy[0]} ${pagination.sortDesc[0] == true ? 'desc' : 'asc'}`
            }
            this.fetchItems()
        },
    },
};
</script>
