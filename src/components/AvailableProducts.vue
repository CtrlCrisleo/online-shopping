<template>
    <header id="alternova-shop">
        <img src="~\public\img\cropped-logo-Alternova.png">
    </header>
    <div class="container">
        <section class="products">
            <div id="availableproducts">
                <div class="product-title">
                    <h1>Nuestros productos</h1>
                </div>
                <div class="product-wrapper">
                    <div v-for="product in products" :key="product.name" class="product">
                        <div class="product-img">
                            <img src="~\public\img\online-bag.png" alt="Imagen genérica de producto" class="dimensions">
                        </div>
                        <p class="product-name">{{ product.name }}</p>
                        <div class="product-actions">
                            <input type="number" class="product-quantity" min="1" value="1">
                            <button class="product-add-to-cart" @click="addToCart(product, $event)">Agregar al carrito</button>
                        </div>
                    </div>
                </div>
            </div>
        </section>
        <shopping-cart :cartItems="cartItems"></shopping-cart>
    </div>
</template>

<script>
import ShoppingCart from './ShoppingCart.vue'

export default {
    data() {
        return {
            products: [],
            cartItems: []
        };
    },
    components: {
        ShoppingCart
    },
    mounted() {
        fetch ('/available-products.json')
        .then(response => response.json())
        .then(data => (this.products = data.products));
    },
    methods: {
        addToCart(product, event) {
            const quantity = parseInt(event.target.previousElementSibling.value);
            const total = quantity * product.unit_price;
            const item = {
            name: product.name,
            quantity: quantity,
            unit_price: product.unit_price,
            total_price: total
            };

            if (product.stock === 0) {
                alert("El producto no está disponible actualmente");
                return;
            }

            if (quantity > product.stock) {
                alert(`Solo queda(n) ${product.stock} unidad(es) disponible(s) de ${product.name}`);
                return;
            }

            const existingItem = this.cartItems.find(item => item.name === product.name);
            
            if (existingItem) {
                existingItem.quantity += quantity;
                existingItem.total_price += total;
            }
            else {
                if (Array.isArray(this.cartItems)) {
                    this.cartItems.push(item);
                }
                else {
                    this.cartItems = [item];
                }
            }

            product.stock -= quantity;
        }
    },
};
</script>

<style>
    header {
        display: flex;
        justify-content: center;
        padding: 15px;
        margin: 10px;
    }
    .container {
        display: flex;
        justify-content: space-between;
        max-width: 1200px;
        margin: 0 auto;
        padding: 20px;
    }
    .products {
        flex-basis: 50%;
        margin-right: 20px;
    }
    .product-title {
        display: flex;
        justify-content: center;
        margin-bottom: 20px;
    }
    .product-wrapper {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        gap: 20px;
    }
    .product {
        border: 1px solid #000;
        border-radius: 5px;
        padding: 5px;
        width: 45%;
        margin-bottom: 20px;
    }
    .product-img {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 50%;
    }
    .dimensions {
        width: 100px;
        height: auto;
    }
    .product-name {
        text-align: center;
        font-weight: bold;
        margin-top: 10px;
        border: 1px solid #000;
    }
    .product-actions {
        display: flex;
        justify-content: center;
        margin-top: 10px;
    }
    .product-quantity {
        flex: 1;
        max-width: 30%;
        text-align: center;
    }
    .product-add-to-cart {
        flex: 2;
        max-width: 70%;
        text-align: center;
    }
</style>