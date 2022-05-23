<script>
	import { onMount } from "svelte";

	let ref = null;
	let coins = [];
	const headers = [
		"#",
		"Name",
		"Symbol",
		"Price (USD)",
		"Market Cap",
		"Volume",
		"Change (24h)",
	];
	let filteredCoins = [];
	const loadData = async () => {
		const res = await fetch(
			"https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=200&page=1&sparkline=false"
		);
		const data = await res.json();
		coins = data;
		filteredCoins = data;
	};

	const searchCoin = (value) => {
		filteredCoins = coins.filter((coin) => {
			return (
				coin.name.toLowerCase().includes(value.toLowerCase()) ||
				coin.symbol.toLowerCase().includes(value.toLowerCase())
			);
		});
	};

	loadData();

	onMount(() => {
		ref.focus();
	});
</script>

<main class="container">
	<h1 class="p-2 m-5">CryptoMarket</h1>

	<input
		type="text"
		placeholder="Search"
		class="form-control bg-dark text-white border-0 rounded-1 my-5"
		on:keyup={({ target: { value } }) => searchCoin(value)}
		bind:this={ref}
	/>

	<table class="table table-dark">
		<thead>
			<tr>
				{#each headers as header}
					<th>{header}</th>
				{/each}
			</tr>
		</thead>
		<tbody>
			{#each filteredCoins as coin}
				<tr>
					<td><p class="cell">{coin.market_cap_rank}</p></td>
					<td
                        ><img
                            src={coin.image}
                            alt={coin.name}
                            class="crypto-icon"
                        /><span>{coin.name}</span></td
                    >
					<td class="text-uppercase"><p class="cell">{coin.symbol}</p></td>
                    <td><p class="cell">{coin.current_price.toLocaleString()}$</p></td>
					<td><p class="cell">{coin.market_cap.toLocaleString()}$</p></td>
                    <td><p class="cell">{coin.total_volume.toLocaleString()}$</p></td>
                    <td
                        class={coin.price_change_percentage_24h >= 0
                            ? "text-success"
                            : "text-danger"}
                        ><p class="cell">{coin.price_change_percentage_24h}%</p>
                    </td>
				</tr>
			{/each}
		</tbody>
	</table>
</main>

<style>
	.crypto-icon {
		width: 2rem;
		margin: 1rem;
	}
	.cell {
		margin: 1rem;
		line-height: 2rem;
	}
</style>
