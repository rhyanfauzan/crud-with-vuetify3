<template>
    <div class="d-flex justify-space-between mb-2">
        <div class="w-50 w-md-25">
            <v-text-field v-model="name" density="compact" placeholder="Search by name..." hide-details></v-text-field>
        </div>
        <CreateProduct />
    </div>
    <v-data-table-server v-model:items-per-page="itemsPerPage" :headers="headers" :items="serverItems"
        :items-length="totalItems" :loading="loading" :search="search" item-value="name" @update:options="loadItems">
    </v-data-table-server>
</template>
<script>
import axios from "axios";
import { ref } from "vue";
import { BASE_URL } from "../config/common.js";
import CreateProduct from "./CreateProduct.vue";
const products = ref([]);

const fetchProducts = async () => {
    axios
        .get(
            `${BASE_URL}/products`
        )
        .then((response) => {
            products.value = response.data.products;
        });
}

const GetProducts = {
    async fetch({ page, itemsPerPage, sortBy, search }) {
        return new Promise(resolve => {
            setTimeout(() => {
                const start = (page - 1) * itemsPerPage
                const end = start + itemsPerPage
                const items = products.value.slice().filter(item => {
                    if (search.name && !item.title.toLowerCase().includes(search.name.toLowerCase())) {
                        return false
                    }

                    return true
                })

                if (sortBy.length) {
                    const sortKey = sortBy[0].key
                    const sortOrder = sortBy[0].order
                    items.sort((a, b) => {
                        const aValue = a[sortKey]
                        const bValue = b[sortKey]
                        return sortOrder === 'desc' ? bValue - aValue : aValue - bValue
                    })
                }

                const paginated = items.slice(start, end)
                resolve({ items: paginated, total: items.length })
            }, 500)
        })
    },
}

export default {
    data: () => ({
        itemsPerPage: 5,
        headers: [
            { title: 'Name', key: 'title', align: 'start' },
            { title: 'Brand', key: 'brand', align: 'start' },
            { title: 'category', key: 'category', align: 'center' },
            { title: 'price', key: 'price', align: 'center' },
            { title: 'stock', key: 'stock', align: 'center' },
        ],
        serverItems: [],
        loading: true,
        totalItems: 0,
        name: '',
        search: '',
    }),
    watch: {
        name() {
            this.search = String(Date.now())
        },
        calories() {
            this.search = String(Date.now())
        },
    },
    methods: {
        loadItems({ page, itemsPerPage, sortBy }) {
            this.loading = true
            GetProducts.fetch({ page, itemsPerPage, sortBy, search: { name: this.name } }).then(({ items, total }) => {
                this.serverItems = items
                this.totalItems = total
                this.loading = false
            })
        },
    },
    mounted() {
        fetchProducts();
    },
}
</script>