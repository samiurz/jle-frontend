<template>
    <v-content>
        <v-container fluid fill-height>
            <v-layout align-center justify-center>
                <v-flex xs12 sm8 md4>
                    <material-card color="success" elevation="12" title="Connexion">
                        <v-card-text>
                            <v-form>
                                <v-text-field
                                    type="text"
                                    v-model="user.email"
                                    prepend-icon="person"
                                    name="username"
                                    label="Login"
                                ></v-text-field>
                                <v-text-field
                                    type="password"
                                    v-model="user.password"
                                    prepend-icon="lock"
                                    name="password"
                                    label="Password"
                                ></v-text-field>
                            </v-form>
                        </v-card-text>
                        <v-card-actions>
                            <v-layout justify-center align-center>
                                <v-btn
                                    color="success"
                                    :disabled="isDisabled"
                                    @click="sendUserData()"
                                >Login</v-btn>
                            </v-layout>
                        </v-card-actions>
                    </material-card>
                </v-flex>
            </v-layout>
        </v-container>
    </v-content>
</template>

<script>
import { mapActions } from 'vuex'
import materialCard from '~/components/material/AppCard'
import Cookies from 'js-cookie'
export default {
    components: {
        materialCard
    },
    data() {
        return {
            user: {}
        }
    },
    methods: {
        ...mapActions({
            setUsername: 'user/setUsername'
        }),
        asyncRouterPush(route) {
            return new Promise(resolve => {
                resolve(this.$router.push({ path: 'dashboard' }))
            })
        },
        doSomething(myVar) {
            this.asyncRouterPush(myVar)
                .then(() => {
                    //this.$root.$emit('someEvent') 
                    console.log("testing")
                })
        },
        async authenticate() {
            await this.setUsername("test");
            this.$router.push({ path: 'dashboard' })
        },
        async sendUserData() {
            try {
                const response = await this.$axios.post('/api/v1/login', this.user)
                Cookies.set('token', `Bearer ${response.data.response.token}`)
                await this.setUsername(response)
                this.$router.push({ path: 'dashboard' })
                //console.log(response.data.response.token)

            } catch (e) {
                console.log(e)
            }
        }
    }
}
</script>
