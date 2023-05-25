<script lang="ts">
	import loader from '$lib/images/loader-primary.svg';
	import { onMount } from 'svelte';
	import Coffeecard from "./Coffeecard.svelte";

	interface IExtCoffeeItem {
		id: number;
		uid: string;
		blend_name: string;
		origin: string;
		variety: string;
		notes: string;
		intensifier: string;
	}

	interface ICoffeeItem {
		name: string;
		tags: Array<string>;
		params: Array<{title: string; value: string;}>;
		image: string;
	}

	let pageInterface = {
		isStartLoaderActive: true,
		isPushLoaderActive: false
	};

	let coffeeList: Array<ICoffeeItem> = [];

	function coffeeItemAdapter(coffeeItem: IExtCoffeeItem, image: string): ICoffeeItem {
		return {
			image: image,
			name: coffeeItem.blend_name,
			tags: coffeeItem.notes.split(",").map(item => {
				return item.trim().charAt(0).toLocaleUpperCase() + item.trim().slice(1)
			}),
			params: [
				{
					title: "Origin",
					value: coffeeItem.origin
				},
				{
					title: "Variety",
					value: coffeeItem.variety
				},
				{
					title: "Intensifier",
					value: coffeeItem.intensifier.charAt(0).toLocaleUpperCase() + coffeeItem.intensifier.slice(1)
				}
			].filter(item => !!item.value)
		};
	}

	function getCoffeeItem() {
		return Promise.all([getCoffeeContent(), getCoffeeImage()]);
	}

	function getCoffeeContent() {
		return fetch("https://random-data-api.com/api/coffee/random_coffee").then((response) => {
			return response.json();
		}).then((coffeeItem) => {
			return coffeeItem;
		});
	}

	function getCoffeeImage() {
		return fetch("https://loremflickr.com/500/500/coffee_bean").then((response) => {
			return response.url;
		})
	}

	function init() {
		pageInterface.isStartLoaderActive = true;
		getCoffeeItem().then((response) => {
			coffeeList.push(coffeeItemAdapter(response[0], response[1]));
			coffeeList = coffeeList;
			pageInterface.isStartLoaderActive = false;
			autoLoadingIntervalId = setInterval(addItem, 30000);
		})
	}

	let autoLoadingIntervalId = null;

	function addItem() {
		clearInterval(autoLoadingIntervalId);
		pageInterface.isPushLoaderActive = true;
		getCoffeeItem().then((response) => {
			coffeeList.push(coffeeItemAdapter(response[0], response[1]));
			coffeeList = coffeeList;
			pageInterface.isPushLoaderActive = false;
			autoLoadingIntervalId = setInterval(addItem, 30000);
			setTimeout(() => {
				window.scrollTo({
					left: 0,
					top: document.body.scrollHeight,
					behavior: "smooth"
				});
			}, 0);
		})
	}

	onMount(() => {
		init();
	});
</script>

<svelte:head>
	<title>Coffee API Client</title>
	<meta name="description" content="Coffee list" />
</svelte:head>

<div class="homepage">
	<div class="container">
		{#if pageInterface.isStartLoaderActive}
		<div class="homepage__starting-loader">
			<img src="{loader}" class="homepage__starting-loader-img">
		</div>
		{:else}
		<ul class="homepage__coffee-list">
			{#each coffeeList as item, i}
			<li class="homepage__coffee-list-item">
				<Coffeecard item="{item}" />
			</li>
			{/each}
		</ul>
		<div class="homepage__adder">
			<button on:click={addItem} class="homepage__adder-btn {pageInterface.isPushLoaderActive ? 'homepage__adder-btn_loading' : ''}"></button>
		</div>
		{/if}
	</div>
</div>

<style>
	.homepage__starting-loader {
		height: 400px;
		display: flex;
	}
	.homepage__starting-loader-img {
		margin: auto;
		width: 100px;
	}
	.homepage__adder {
		margin-top: 32px;
	}
	.homepage__adder-btn {
		display: table;
		width: 56px;
		height: 56px;
		border-radius: 50%;
		margin-left: auto;
		margin-right: auto;
		border: none;
		background-color: var(--primary);
		background-image: url("../lib/images/plus.svg");
		-webkit-background-size: 20px;
		background-size: 20px;
		background-position: center;
		transition: 0s;
	}
	.homepage__adder-btn_loading {
		pointer-events: none;
		opacity: 0.8;
		background-image: url("../lib/images/loader-white.svg");
		-webkit-background-size: 40px;
		background-size: 40px;
	}
	.homepage__coffee-list-item:not(:last-child) {
		margin-bottom: 16px;
	}
</style>
