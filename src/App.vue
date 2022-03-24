<template>
	<main>
		<header>Estampitiency</header>
		<section>
			<article v-for="product of products.value" :key="product.id">
				<img :src="product.image" :alt="product.description" />
				<div>
					<p>{{ product.title }}</p>
					<p>{{ product.description }}</p>
				</div>
				<div v-if="isInCarrito(product)">
					<button @click="updateCarrito(product, 1)">+</button>
					<p>{{ amountInCarrito(product) }}</p>
					<button @click="updateCarrito(product, -1)">-</button>
				</div>
				<button v-else @click="addNewProduct(product)">Agregar</button>
			</article>
		</section>
		<aside>
			<button>{{ carritoMessage }}</button>
		</aside>
		<footer>
			Encontrá la consigna de este ejercicio y otros más{" "}
			<a
				href="https://github.com/goncy/interview-challenges/tree/main/simple-cart"
				>acá</a
			>
		</footer>
	</main>
</template>

<script>
/* eslint-disable no-mixed-spaces-and-tabs */
import { onMounted, reactive, computed } from "vue";
import api from "./api/api";

export default {
	setup() {
		const products = reactive({ value: [] });
		const carrito = reactive({ value: [] });

		const addNewProduct = (product) => {
			carrito.value = [...carrito.value, { ...product, quantity: 1 }];
		};

		const positionInCarrito = (product) =>
			carrito.value.findIndex(
				(productOfCarrito) => productOfCarrito.id === product.id,
			);

		const isInCarrito = (product) => positionInCarrito(product) > -1;

		const quantityOf = () => 2; //(index) => carrito.value[index].quantity;

		const updateCarrito = (product, value) => {
			const indexInCarrito = positionInCarrito(product);
			carrito.value.splice(indexInCarrito, 1, {
				...product,
				quantity: quantityOf() + value,
			});
		};

		const carritoMessage = computed(() => {
			const carritoData = carrito.value.reduce(
				(previousValue, currentValue) => {
					return {
						quantity: previousValue.quantity + currentValue.quantity,
						amount:
							previousValue.amount + currentValue.quantity * currentValue.price,
					};
				},
				{ quantity: 0, amount: 0 },
			);
			return `${carritoData.quantity} productos ($${carritoData.amount})`;
		});

		const amountInCarrito = (product) => quantityOf(positionInCarrito(product));

		onMounted(() => {
			api.list().then((data) => (products.value = data));
		});

		return {
			products,
			addNewProduct,
			carritoMessage,
			isInCarrito,
			updateCarrito,
			amountInCarrito,
		};
	},
};
</script>

<style>
html,
body {
	margin: 0;
	height: 100%;
	background-color: whitesmoke;
	font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
		"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
		sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}

code {
	font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
		monospace;
}

#root {
	max-width: 1250px;
	min-height: 100vh;
	margin: auto;
	background-color: white;
	box-shadow: 0 0 3px rgba(0, 0, 0, 0.1);
}

* {
	box-sizing: border-box;
	margin: 0;
	padding: 0;
}

a {
	color: black;
}

img {
	width: 100%;
	object-fit: contain;
}

button {
	color: white;
	background-color: dodgerblue;
	border: none;
	line-height: 48px;
	border-radius: 4px;
	font-size: 18px;
	font-weight: 500;
	cursor: pointer;
	padding: 0 16px;
}

/* Blocks */
main {
	display: flex;
	flex-direction: column;
	justify-content: space-between;
}

header {
	padding: 16px;
	border-bottom: 1px solid gainsboro;
	font-weight: bold;
	font-size: 24px;
}

footer {
	padding: 16px;
	border-top: 1px solid gainsboro;
	text-align: center;
	color: gray;
}

section {
	padding: 16px;
	flex: 1;
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
	gap: 12px;
}

article {
	display: flex;
	flex-direction: column;
	gap: 16px;
}

article > div {
	display: flex;
	flex-direction: column;
	gap: 6px;
	height: 100%;
}

article > div > p:nth-of-type(1) {
	font-weight: 500;
	font-size: 20px;
}

article > div > p:nth-of-type(2) {
	color: gray;
}

article > div > button {
	margin-top: auto;
}

aside {
	position: sticky;
	bottom: 0;
	margin: auto;
	padding-bottom: 16px;
}

aside > button {
	box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
}
</style>
