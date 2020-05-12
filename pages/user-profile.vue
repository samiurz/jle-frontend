<template>
    <v-container fill-height fluid grid-list-xl>
        <v-layout justify-center wrap>
            <v-flex md12>
                <material-card
                    color="green"
                    title="Simple Table"
                    text="Here is a subtitle for this table"
                >
                    <v-tooltip bottom>
                        <template v-slot:activator="{ on }">
                            <v-btn
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
                        <span>Add</span>
                    </v-tooltip>
                    <v-data-table
                        :headers="headers"
                        :items="items"
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
                            <td>{{ item.country }}</td>
                            <td>{{ item.city }}</td>
                            <td class="text-xs-right">{{ item.salary }}</td>
                            <td align="center">
                                <v-tooltip top content-class="top">
                                    <v-btn
                                        slot="activator"
                                        class="v-btn--simple"
                                        color="success"
                                        icon
                                    >
                                        <v-icon color="primary">mdi-pencil</v-icon>
                                    </v-btn>
                                    <span>Edit</span>
                                </v-tooltip>
                                <v-tooltip top content-class="top">
                                    <v-btn
                                        slot="activator"
                                        class="v-btn--simple"
                                        color="danger"
                                        icon
                                    >
                                        <v-icon color="error">mdi-close</v-icon>
                                    </v-btn>
                                    <span>Close</span>
                                </v-tooltip>
                            </td>
                        </template>
                    </v-data-table>
                </material-card>
            </v-flex>
        </v-layout>

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
                <v-layout justify-center wrap>
                    <v-flex xs12 md8>
                        <material-card
                            color="green"
                            title="Create Profile"
                            text="Complete your profile"
                        >
                            <v-form>
                                <v-container py-0>
                                    <v-layout wrap>
                                        <v-flex xs12 md4>
                                            <v-select :items="branchs" label="Branch"></v-select>
                                        </v-flex>
                                        <v-flex xs12 md4>
                                            <v-text-field label="Full Name" class="purple-input" />
                                        </v-flex>
                                        <v-flex xs12 md4>
                                            <v-text-field class="purple-input" label="User Name" />
                                        </v-flex>
                                        <v-flex xs12 md4>
                                            <v-select :items="roles" label="Role"></v-select>
                                        </v-flex>
                                        <v-flex xs12 md4>
                                            <v-text-field
                                                label="Email Address"
                                                class="purple-input"
                                            />
                                        </v-flex>
                                        <v-flex xs12 md4>
                                            <v-text-field
                                                label="Password"
                                                type="password"
                                                class="purple-input"
                                            />
                                        </v-flex>
                                        <v-flex xs12 md12>
                                            <v-file-input accept="image/*" label="File input"></v-file-input>
                                        </v-flex>
                                        <v-flex xs12 text-xs-right>
                                            <v-btn
                                                class="mx-0 font-weight-light"
                                                color="success"
                                            >Update Profile</v-btn>
                                        </v-flex>
                                    </v-layout>
                                </v-container>
                            </v-form>
                        </material-card>
                    </v-flex>
                    <v-flex xs12 md4>
                        <material-card class="v-card-profile">
                            <v-avatar slot="offset" class="mx-auto d-block" size="130">
                                <img
                                    src="https://demos.creative-tim.com/vue-material-dashboard/img/marc.aba54d65.jpg"
                                />
                            </v-avatar>
                            <v-card-text class="text-xs-center">
                                <h6
                                    class="category text-gray font-weight-thin mb-3"
                                >{{ user.function }}</h6>
                                <h4 class="card-title font-weight-light">{{ fullname }}</h4>
                                <p class="card-description font-weight-light">{{ user.description }}</p>
                                <blockquote class="blockquote">{{ user.citation }}</blockquote>
                                <v-btn color="success" round class="font-weight-light">Follow</v-btn>
                            </v-card-text>
                        </material-card>
                    </v-flex>
                </v-layout>
            </v-card>
        </v-dialog>
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
        branchs: ['Auckland & Upper Northland', 'Newplymouth', 'International'],
        roles: ['Admin', 'Manager', 'IT', 'Office Support', 'Contractor', 'Technician'],
        headers: [
            {
                sortable: false,
                text: "Name",
                value: "name",
            },
            {
                sortable: true,
                text: "Country",
                value: "country"
            },
            {
                sortable: true,
                text: "City",
                value: "city"
            },
            {
                sortable: false,
                text: "Salary",
                value: "salary",
                align: "right"
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
        dialog: false
    }),


    computed: {
        ...mapGetters({
            user: "user/getUser",
            fullname: "user/getFullname"
        })
    },
    methods: {
        updatePagination() {
        },
        addItem() {
            this.dialog = true
            this.loadDependencies()
        },
        async loadDependencies() {
            try {
                const response = await this.$axios.get('/api/v1/users-dependencies')
                // await this.setUsername(response)
                // this.$router.push({ path: 'dashboard' })
                console.log(response)

            } catch (e) {
                console.log(e)
            }
        }
    },
};
</script>
