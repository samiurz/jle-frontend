<template>
    <v-container fill-height fluid grid-list-xl>
        <v-layout justify-center wrap>
            <v-flex md12>
                <material-card
                    color="green"
                    title="Job information"
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
                        <span><font color="white">Add New Job</font></span>
                    </v-tooltip>
                    {{pagination}}
                    <v-data-table
                        :headers="headers"
                        :items="jobs"
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
                            <td>{{ item.customer.name }}</td>
                            <td>{{ item.description }}</td>
                            <td>{{ item.code }}</td>
                            <td>{{ item.priority.name }}</td>
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
                                    <span><font color="white">Edit Job Information</font></span>
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
                                    <span><font color="white">Remove Job Information</font></span>
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
          <span class="headline">Job</span>
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
              <v-col cols="12" sm="6" md="4">
                <v-select
                    item-text="name"
                    item-value="id"
                    :items="customers"
                    label="customer"
                    v-model="job.customer_id"
                    :rules="validationRules.customer"
                ></v-select>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                    label="Job Description"
                    v-model="job.description"
                    class="purple-input"
                    :rules="validationRules.description"
                />
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                    class="purple-input"
                    v-model="job.code"
                    label="job Code"
                    :rules="validationRules.code"
                />
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-select
                    item-text="name"
                    item-value="id"
                    :items="priorities"
                    label="priority"
                    v-model="job.priority_id"
                    :rules="validationRules.priority"
                ></v-select>
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
                sortable: true,
                text: "customer",
                value: "customer.name",
            },
            {
                sortable: false,
                text: "Job Description",
                value: "description",
            },
            {
                sortable: true,
                text: "Job Code",
                value: "code"
            },
            {
                sortable: true,
                text: "priority",
                value: "priority.name"
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
        sort: '',
        dialog: false,
        customers: [],
        priorities: [],
        job: {
            description: '',
            code: '',
            customer_id: '',
            priority_id: '',
        },
        valid: false,
        editMode: false,
        validationRules: {
            description: [
                v => !!v || "Job Description is required",
                v =>
                    (v && v.length <= 20) ||
                    "Job Description must be less than 20 characters"
            ],
            code: [
                v => !!v || "Job Code is required",
                v =>
                    (v && v.length <= 10) ||
                    "Job Code must be less than 20 characters"
            ],
            customer: [
                v => !!v || "Customer is required",
                v =>
                    (v && v > 0) ||
                    "Customer must be selected"
            ],
            priority: [
                v => !!v || "priority is required",
                v =>
                    (v && v > 0) ||
                    "priority must be selected"
            ],
        },
        jobs: []
    }),
    methods: {
        updatePagination() {
        },
        addItem() {
            this.job = {}
            this.dialog = true
            this.loadDependencies()
        },
        async loadDependencies() {
            try {
                this.loading = true
                const response = await this.$axios.get('/api/v1/jobs-dependencies')
                this.customers = response.data.response.customers.data
                this.priorities = response.data.response.priorities.data
                this.loading = false

            } catch (e) {
                console.log(e)
                this.loading = false
            }
        },
        async create() {
            try {
                this.loading = true
                const response = await this.$axios.post('/api/v1/jobs', this.job)
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
                const response = await this.$axios.put(`/api/v1/jobs/${this.job.id}`, this.job)
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
                const baseURI = `/api/v1/jobs`
                const response = await this.$axios.get(baseURI)
                this.jobs = response.data.response.data.rows
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
            this.job = Object.assign({}, item)
            this.dialog = true
            this.loadDependencies()
        },
        async deleteItem(item) {
            try {
                this.loading = true
                const response = await this.$axios.delete(`/api/v1/jobs/${item.id}`)
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
