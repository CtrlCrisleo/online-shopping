<template>
    <section class="shopping-cart">
        <h1>Carrito</h1>
        <div v-for="item in cartItems" :key="item.name">
            <div class="show-cart">
                <p class="item">{{ item.name }}</p>
                <p class="item">{{ item.quantity }}</p>
                <p class="item">{{ item.unit_price }}</p>
                <p class="item">{{ item.quantity * item.unit_price }}</p>
            </div>
        </div>
        <div class="cart-final">
            <p class="total-value">{{ getTotalValue }}</p>
            <button class="create-order-button" @click="createOrder">Crear orden</button>
        </div>
    </section>
</template>

<script>
    export default {
        props: {
            cartItems: {
                type: Array,
                default: () => []
            }
        },
        computed: {
            getTotalValue() {
                return this.cartItems.reduce((acc, item) => acc + (item.quantity * item.unit_price), 0);
            }
        },
        methods: {
            createOrder() {
                const cart = this.cartItems.map(item => ({
                    product: item.name,
                    quantity: item.quantity,
                    "total product": item.quantity * item.unit_price
                }));

                const newOrder = {
                    cart: cart,
                    "total order": this.getTotalValue
                };

                const json = JSON.stringify(newOrder);
                const blob = new Blob([json], { type: "application/json" });
                const url = URL.createObjectURL(blob);
                
                const a = document.createElement("a");
                a.href = url;
                a.download = "new-order.json";
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
            }
        }
    };
</script>

<style>
    .shopping-cart {
        flex-basis: 50%;
        border: 1px solid #000;
        padding: 20px;
    }
    .show-cart {
        display: flex;
        justify-content: center;
        margin-top: 10px;
    }
    .cart-final {
        display: flex;
        justify-content: center;
        margin-top: 10px;
    }
    .item{
        flex: 1;
        max-width: 100%;
        text-align: center;
        border: 1px solid #000;
        padding: 10px;
        margin: 10px;
    }
</style>