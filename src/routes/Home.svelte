<script>
	import { onMount } from "svelte";
	import { navigate } from "svelte-routing";

	import Logo from "../components/Logo.svelte";

	onMount(() => {
		window.scrollTo(0, 0);

		getData();
	});

	var strany = [];

	async function getData() {
		const res = await fetch(
			"https://slibotechnyapi.pythonanywhere.com/get_strany",
			{
				method: "GET",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json = await res.json();
		strany = json.strany;
	}
</script>

<Logo />
<section>
	{#each strany as strana}
		<div
			class="strana-item"
			style="background-color: {strana.barva}; color: {strana.sekundarni_barva};"
			on:click={() => {
				navigate("/strana/" + strana.nazev);
			}}
		>
			<span>{strana.nazev}</span>
		</div>
	{/each}

	<a href="/napsat-clanek" id="napsat-clanek">Napsat článek</a>
</section>

<style>
	#napsat-clanek {
		position: fixed;
		bottom: 15px;
		right: 15px;

		color: #fff;
		background-color: #4caf50;
		padding: 10px 15px;

		font-size: 28px;
		font-weight: 700;
		transition: 0.15s;
	}
	#napsat-clanek:hover {
		background-color: #3d9140;
	}
	section {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-between;
		padding: 0px calc((100% - 85rem) / 2) 0px calc((100% - 85rem) / 2);

		margin-top: 40px;
	}
	.strana-item {
		position: relative;
		width: 22%;
		margin-top: 30px;
		border-radius: 5px;
		text-align: center;

		cursor: pointer;
		transition: 0.15s;

		display: inline-block;
		height: 0;
		padding-bottom: 22%;
	}
	.strana-item:hover {
		-webkit-filter: brightness(75%);
		filter: brightness(75%);
	}
	.strana-item span {
		position: absolute;
		left: 50%;
		top: 50%;
		transform: translateX(-50%) translateY(-50%);

		font-family: "Rokkitt";
		font-weight: 700;
		font-size: 70px;
	}

	@media (max-width: 1400px) {
		section {
			padding: 0 20px;
		}
	}

	@media (max-width: 800px) {
		section {
			flex-wrap: nowrap;
			justify-content: left;
			padding: 0;

			flex-direction: column;
			align-items: center;

			margin-top: 10px;
		}
		.strana-item {
			width: 90%;
			padding-bottom: 40%;
		}
	}
</style>
