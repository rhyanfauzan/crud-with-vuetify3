<template>
    <div class="pa-4 text-center">
        <v-dialog v-model="dialog" max-width="600">
            <template v-slot:activator="{ props: activatorProps }">
                <v-btn v-bind="activatorProps" icon="mdi-pen" variant="plain" @click="getSingleProduct($props.item)">
                </v-btn>
            </template>
            <v-form @submit.prevent="editProduct">
                <v-card prepend-icon="mdi-image" title="Update Product">
                    <v-card-text>
                        <v-row dense>
                            <v-col cols="12" md="6">
                                <v-text-field label="Name*" required v-model="name" :rules="rules"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="6">
                                <v-text-field label="Brand*" required v-model="brand" :rules="rules"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="6">
                                <v-select :items="['smartphone', 'tablet', 'tv', 'console']" label="Category*" required
                                    v-model="category" :rules="rules"></v-select>
                            </v-col>

                            <v-col cols="12" md="6">
                                <v-text-field label="Description" v-model="description"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="6">
                                <v-text-field label="Price*" required v-model="price" type="number"
                                    :rules="rules"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="6">
                                <v-text-field label="Stock" v-model="stock" type="number"></v-text-field>
                            </v-col>
                        </v-row>

                        <small class="text-caption text-medium-emphasis">*indicates required field</small>
                    </v-card-text>

                    <v-divider></v-divider>

                    <v-card-actions>
                        <v-spacer></v-spacer>

                        <v-btn text="Close" variant="plain" @click="dialog = false"></v-btn>

                        <v-btn color="primary" text="Save" variant="tonal" type="submit" :loading="isLoading"></v-btn>
                    </v-card-actions>
                </v-card>
            </v-form>
        </v-dialog>
    </div>
    <div class="text-center position-absolute">
        <v-snackbar color="green" v-model="snackbar" :timeout="timeout">
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
            name: '',
            brand: '',
            category: '',
            price: '',
            description: '',
            stock: '',
            dialog: false,
            rules: [
                value => {
                    if (value) return true

                    return 'This field is required.'
                },
            ],
            snackbar: false,
            text: 'Berhasil mengubah product.',
            timeout: 2500,
        };
    },
    methods: {
        async updateProduct() {
            this.isLoading = true;
            try {
                const response = await axios.put(`${BASE_URL}/products/${idItem.value}`, {
                    title: this.name,
                    brand: this.brand,
                    category: this.category,
                    price: this.price,
                    description: this.description,
                    stock: this.stock,
                    // other product data
                }, {
                    headers: {
                        'Content-Type': 'application/json'
                    }
                });
                this.isLoading = false;
                this.dialog = false;
                this.snackbar = true;
            } catch (error) {
                console.error('Error adding product:', error);
                this.isLoading = false;
            }
        },
        editProduct() {
            if (this.name != '' && this.brand != "" && this.category != "" && this.price != "") {
                this.updateProduct();
            }
        },
        async getSingleProduct(item) {
            try {
                const response = await axios
                    .get(
                        `${BASE_URL}/products/${item.id}`
                    );
                this.name = response.data.title;
                this.brand = response.data.brand;
                this.category = response.data.category;
                this.price = response.data.price;
                this.description = response.data.description;
                this.stock = response.data.stock;
                idItem.value = item.id;
            } catch (error) {
                console.error(error)
            }
        }
    },
    mounted() {

    },
};
</script>