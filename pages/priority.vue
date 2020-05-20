<template>
    <v-container fill-height fluid grid-list-xl>
        <v-layout justify-center wrap>
            <v-flex md12>
                <material-card
                    color="green"
                    title="priority information"
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
                        <span><font color="white">Add New priority</font></span>
                    </v-tooltip>
                    {{pagination}}
                    <v-data-table
                        :headers="headers"
                        :items="priorities"
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
                                    <span><font color="white">Edit priority Information</font></span>
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
                                    <span><font color="white">Remove priority</font></span>
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
          <span class="headline">priority</span>
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
                <v-text-field
                    label="priority Name"
                    v-model="priority.name"
                    class="purple-input"
                    :rules="validationRules.name"
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
                sortable: true,
                text: "priority Name",
                value: "name",
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
        priorities: [],
        priority: {
            name: '',
        },
        valid: false,
        editMode: false,
        validationRules: {
            name: [
                v => !!v || "Priority Name is required",
                v =>
                    (v && v.length <= 20) ||
                    "Priority Name must be less than 20 characters"
            ],
        },
        priorities: []
    }),
    methods: {
        updatePagination() {
        },
        addItem() {
            this.priority = {}
            this.dialog = true
            this.loadDependencies()
        },
        async loadDependencies() {
            try {
                this.loading = true
                const response = await this.$axios.get('/api/v1/priorities-dependencies')
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
                const response = await this.$axios.post('/api/v1/priorities', this.priority)
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
                const response = await this.$axios.put(`/api/v1/priorities/${this.priority.id}`, this.priority)
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
                const baseURI = `/api/v1/priorities`
                const response = await this.$axios.get(baseURI)
                this.priorities = response.data.response.data.rows
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
            this.priority = Object.assign({}, item)
            this.dialog = true
            this.loadDependencies()
        },
        async deleteItem(item) {
            try {
                this.loading = true
                const response = await this.$axios.delete(`/api/v1/priorities/${item.id}`)
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
