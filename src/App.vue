<template>
    <v-app>
        <v-main>
            <NetHuntTable/>
        </v-main>
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
