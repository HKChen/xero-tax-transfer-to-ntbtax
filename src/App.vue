<template>
    <v-app>
        <v-app-bar app color="primary" dark>
            <v-toolbar-title>Xero 會計檔案轉檔為國稅局營業稅轉檔軟體</v-toolbar-title>
            <v-spacer></v-spacer>
            Copyright © 2020-2023 HK Chen
        </v-app-bar>

        <v-main>
            <Upload />
        </v-main>
    </v-app>
</template>

<script>
import Upload from "./components/Upload";

export default {
    name: "App",

    components: {
        Upload
    },

    data: () => ({
        drawer: false,
        group: null,
    }),
};
</script>
