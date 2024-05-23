<template>
    <v-main>
        <v-container>
            <p class="text-h5 my-5">Product Management</p>

            <v-container>
                <!-- Start Show list porducts using datatable  -->
                <DataTable />
                <!-- End Show list porducts using datatable  -->
            </v-container>
        </v-container>
    </v-main>
</template>

<script setup>
import DataTable from '@/components/DataTable.vue';

</script>