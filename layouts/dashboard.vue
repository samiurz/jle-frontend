<template>
    <v-app>
        <core-filter />

        <core-toolbar />

        <core-drawer />

        <core-view />
    </v-app>
</template>

<script>
import coreFilter from '~/components/core/AppFilter';
import coreToolbar from '~/components/core/AppToolbar';
import coreDrawer from '~/components/core/AppDrawer';
import coreView from '~/components/core/AppView';
import Cookies from 'js-cookie'
export default {
    components: {
        coreFilter,
        coreToolbar,
        coreDrawer,
        coreView
    },
    created() {
        console.log('here')
        this.$axios.defaults.headers.common.Authorization = Cookies.get('token')
    },
}
</script>

<style lang="scss">
/* Remove in 1.2 */
.v-datatable thead th.column.sortable i {
    vertical-align: unset;
}
</style>
