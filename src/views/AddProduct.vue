<script setup></script>

<template>
    <div class="container center">
        <div v-if="submitted">
            <h3>Produto criado com sucesso.</h3>
            <button class="btn btn-primary mt-4" @click="newProduct()">
                Novo Produto
            </button>
            <div class="row text-center mt-4">
                <router-link to="/products"
                    >Voltar à lista de produtos</router-link
                >
            </div>
        </div>
        <div v-else>
            <h1>Novo produto</h1>
            <div class="form row g-3 m-4 needs-validation" novalidate>
                <div class="row">
                    <label for="name" class="form-label">Nome</label>
                    <input
                        type="text"
                        class="form-control mb-4"
                        id="name"
                        name="name"
                        placeholder="Produto exemplo"
                        required
                        v-model="product.name"
                    />
                </div>
                <div class="row">
                    <label for="image" class="form-label">Imagem</label>
                    <input
                        type="file"
                        class="form-control mb-4"
                        id="image"
                        ref="image"
                        accept="image/*"
                        required
                        @change="setImage"
                    />
                </div>
                <div class="row">
                    <label for="category" class="form-label">Categoria</label>
                    <select
                        name="category"
                        id="category"
                        class="form-control"
                        v-model="product.categoryId"
                    >
                        <option
                            v-for="category in categories"
                            :value="category.id"
                        >
                            {{ category.name }}
                        </option>
                    </select>
                </div>
                <div class="row mt-4">
                    <div class="col-md-6">
                        <label for="quantity" class="form-label"
                            >Quantidade</label
                        >
                        <input
                            type="number"
                            class="form-control"
                            id="quantity"
                            name="quantity"
                            placeholder="0"
                            value="0"
                            required
                            v-model="product.quantity"
                        />
                    </div>
                    <div class="col-md-6">
                        <label for="price" class="form-label"
                            >Preço de venda</label
                        >
                        <input
                            type="number"
                            class="form-control"
                            id="price"
                            name="price"
                            placeholder="0"
                            value="1.00"
                            step="0.01"
                            required
                            v-model="product.price"
                        />
                    </div>
                </div>
                <div class="row my-4 g-4">
                    <button class="btn btn-primary" @click="saveProduct()">
                        Adicionar
                    </button>
                    <router-link to="/products" class="btn btn-danger"
                        >Cancelar</router-link
                    >
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import ProductDataService from "../services/ProductDataService";
import CategoryDataService from "../services/CategoryDataService";
import ImageDataService from "../services/ImageDataService";

import { session } from "../session";

export default {
    name: "new-product",
    data() {
        return {
            submitted: false,
            product: {
                name: "",
                quantity: "",
                price: "",
                categoryId: "",
            },
            file: "",
            categories: [],
        };
    },
    methods: {
        getAllCategories() {
            CategoryDataService.getAll()
                .then((res) => {
                    this.categories = res.data;
                })
                .catch((err) => {
                    console.log(err);
                });
        },
        saveProduct() {
            var data = {
                name: this.product.name,
                quantity: this.product.quantity,
                price: this.product.price,
                categoryId: this.product.categoryId,
            };

            var formData = new FormData();
            formData.append("file", this.file);

            ProductDataService.create(data, session().token)
                .then((res) => {
                    formData.append("productId", res.data.id);
                    ImageDataService.create(formData)
                        .then((res) => {
                            this.submitted = true;
                        })
                        .catch((err) => {
                            console.log(err);
                        });
                })
                .catch((err) => {
                    console.log(err);
                });
        },
        setImage() {
            var file = this.$refs.image.files.item(0);
            this.file = file;
        },
        newProduct() {
            (this.submitted = false), (this.product = {});
        },
    },
    mounted() {
        this.getAllCategories();
    },
};
</script>

<style></style>
