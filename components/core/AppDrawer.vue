<template>
    <v-navigation-drawer
        id="app-drawer"
        v-model="inputValue"
        app
        dark
        floating
        persistent
        mobile-break-point="991"
        width="260"
    >
        <v-img :src="image" height="100%">
            <v-layout class="fill-height" tag="v-list" column>
                <v-list dense>
                    <v-list-tile avatar to="/">
                        <v-list-tile-avatar color>
                            <v-img :src="logo" height="50" contain />
                        </v-list-tile-avatar>
                        <v-list-tile-title class="title">JWARE</v-list-tile-title>
                    </v-list-tile>
                </v-list>
                <v-divider />
                <v-list dense>
                    <v-list-tile v-if="responsive">
                        <v-text-field
                            class="purple-input search-input"
                            label="Search..."
                            color="purple"
                        />
                    </v-list-tile>
                    <v-list-tile
                        v-for="(link, i) in links"
                        :key="i"
                        :to="link.to"
                        :active-class="color"
                        avatar
                        class="v-list-item"
                    >
                        <v-list-tile-action>
                            <v-icon>{{ link.icon }}</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-title v-text="link.text" />
                    </v-list-tile>
                </v-list>
            </v-layout>
        </v-img>
    </v-navigation-drawer>
</template>

<script>
// Utilities
import { mapActions, mapGetters } from 'vuex'

export default {
    data() {
        return {
            logo: '/jle.png',
            links: [
                {
                    to: '/dashboard',
                    icon: 'mdi-view-dashboard',
                    text: 'Dashboard'
                },
                {
                    to: '/user-profile',
                    icon: 'mdi-account',
                    text: 'User Profile'
                },
                {
                    to: '/job',
                    icon: 'mdi-adjust',
                    text: 'Job'
                },
                {
                    to: '/expense',
                    icon: 'mdi-atom',
                    text: 'Expense'
                },
                {
                    to: '/branch',
                    icon: 'mdi-anchor',
                    text: 'Branch'
                },
                {
                    to: '/customer',
                    icon: 'mdi-all-inclusive',
                    text: 'Customer'
                },
                {
                    to: '/gl',
                    icon: 'mdi-archive',
                    text: 'General Ledger Code'
                },
                {
                    to: '/priority',
                    icon: 'mdi-arrange-bring-to-front',
                    text: 'Priority'
                },
                // {
                //     to: '/table-list',
                //     icon: 'mdi-clipboard-outline',
                //     text: 'Table List'
                // },
                // {
                //   to: '/typography',
                //   icon: 'mdi-format-font',
                //   text: 'Typography'
                // },
                // {
                //   to: '/icons',
                //   icon: 'mdi-chart-bubble',
                //   text: 'Icons'
                // },
                // {
                //   to: '/maps',
                //   icon: 'mdi-map-marker',
                //   text: 'Maps'
                // },
                // {
                //   to: '/notifications',
                //   icon: 'mdi-bell',
                //   text: 'Notifications'
                // }
            ],
            responsive: true
        }
    },
    computed: {
        ...mapGetters({
            image: 'app/getImage',
            color: 'app/getColor',
            drawer: 'app/getDrawer'
        }),


        inputValue: {
            get() {
                return this.drawer
            },
            set(val) {
                this.setDrawer(val)
            }
        }
    },
    mounted() {
        this.onResponsiveInverted()
        window.addEventListener('resize', this.onResponsiveInverted)
    },
    beforeDestroy() {
        window.removeEventListener('resize', this.onResponsiveInverted)
    },
    methods: {
        ...mapActions({
            setDrawer: 'app/setDrawer'
        }),

        onResponsiveInverted() {
            this.responsive = window.innerWidth < 991;
        }
    }
}
</script>

<style lang="scss">
#app-drawer {
    &.v-navigation-drawer .v-list {
        background: rgba(27, 27, 27, 0.4);
        padding: 0;
    }

    .v-divider {
        margin: 0;
    }

    .v-list__tile {
        border-radius: 4px;

        &--buy {
            margin-top: auto;
            margin-bottom: 17px;
        }

        &__title {
            color: #ffffff;
        }
    }

    .v-image__image--contain {
        top: 9px;
        height: 60%;
    }

    .search-input {
        margin-bottom: 30px !important;
        padding-left: 15px;
        padding-right: 15px;
    }
}
</style>
