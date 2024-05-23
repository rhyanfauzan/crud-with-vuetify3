<template>
    <div class="text-center pa-4">
        <v-dialog v-model="dialog" max-width="400" persistent>
            <template v-slot:activator="{ props: activatorProps }">
                <v-btn v-bind="activatorProps" icon="mdi-delete" variant="plain" @click="deleteItem($props.item)">
                </v-btn>
            </template>

            <v-card prepend-icon="mdi-delete" text="Apakah Anda yakin untuk menghapus produk ini?"
                title="Delete Product">
                <template v-slot:actions>
                    <v-spacer></v-spacer>
                    <v-btn @click="dialog = false">
                        Tidak
                    </v-btn>
                    <v-btn @click="fetchDeleteProduct" :loading="isLoading">
                        Hapus
                    </v-btn>
                </template>
            </v-card>
        </v-dialog>
    </div>
    <div class="text-center position-absolute">
        <v-snackbar color="red" v-model="snackbar" :timeout="timeout">
            {{ text }}
        </v-snackbar>
    </div>
</template>
<script>
import axios from "axios";
import { ref } from "vue";
import { BASE_URL } from '@/config/common';

const idItem = ref(0);

export default {
    props: {
        item: Object
    },
    data() {
        return {
            isLoading: false,
            dialog: false,
            snackbar: false,
            text: 'Berhasil menghapus product ini.',
            timeout: 2500,
        }
    },
    methods: {
        deleteItem(item) {
            idItem.value = item.id;
        },
        async fetchDeleteProduct() {
            this.isLoading = true;
            try {
                const response = await axios.delete(`${BASE_URL}/products/${idItem.value}`);

                this.text = `Berhasil menghapus ${response.data.title}`;
                this.isLoading = false;
                this.dialog = false;
                this.snackbar = true;
            } catch (error) {
                console.error('Error deleting product:', error);
                this.isLoading = false;
            }
        }
    },
    mounted() {
    },
}
</script>