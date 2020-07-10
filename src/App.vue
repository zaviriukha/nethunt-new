<template>
    <v-app>
        <v-content>
            <NetHuntTable/>
        </v-content>
    </v-app>
</template>

<script>
    import NetHuntTable from "./components/netHuntTable";

    export default {
        name: 'App',

        components: {
            NetHuntTable
        },

        data: () => ({
            //
        }),
    };
</script>
