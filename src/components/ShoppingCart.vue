<template>
    <section class="shopping-cart">
        <div class="cart-border">
            <h1>Carrito ({{ totalQuantity }})</h1>
            <div class="cart-header">
                <p class="header-item">Producto</p>
                <p class="header-item">Cantidad</p>
                <p class="header-item">Precio Unitario</p>
                <p class="header-item">Precio total</p>
            </div>
            <div v-for="item in cartItems" :key="item.name">
                <div class="show-cart">
                    <p class="cart-item">{{ item.name }}</p>
                    <p class="cart-item">{{ item.quantity }}</p>
                    <p class="cart-item">${{ item.unit_price }}</p>
                    <p class="cart-item">${{ item.quantity * item.unit_price }}</p>
                </div>
            </div>
            <div class="cart-final">
                <p class="total-value">Total Orden ${{ getTotalValue }}</p>
                <button class="create-order-button" @click="createOrder">Crear orden</button>
            </div>
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
            },
            totalQuantity() {
                return this.cartItems.reduce((acc, item) => acc + item.quantity, 0);
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
        padding: 20px;
        /*Responsive*/
        max-width: 100%;
        margin: 0 auto;
    }
    .cart-border {
        border: 2px solid #000;
        padding: 20px;
    }
    .cart-header {
        display: flex;
        justify-content: space-between;
        margin-top: 10px;
        border-bottom: 2px solid #000;
        font-weight: bold;
    }
    .header-item {
        flex: 1;
        text-align: center;
    }
    .show-cart {
        display: flex;
        justify-content: center;
        margin-top: 10px;
    }
    .cart-final {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-top: 10px;
    }
    .cart-item{
        flex: 1;
        max-width: 100%;
        text-align: center;
        padding: 10px;
        margin: 10px;
    }
    .total-value {
        font-weight: bold;
    }
</style>