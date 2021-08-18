<script>
	import { navigate } from "svelte-routing";

	export let strana;
	export let back = "";
</script>

<header
	style="background-color: {strana?.barva}; color: {strana?.sekundarni_barva};"
>
	<div
		class="arrow"
		on:click={() => {
			if (back != "") {
				window.history.back();
			} else {
				navigate("/");
			}
		}}
	/>
	{strana.nazev}
</header>

<style>
	header {
		text-align: center;
		height: 200px;
		line-height: 200px;
		font-size: 80px;
		font-family: "Rokkitt";
		border-radius: 5px;
		position: relative;
	}

	.arrow {
		border: solid white;
		border-width: 0 6px 6px 0;
		display: inline-block;
		padding: 6px;
		transform: rotate(135deg);
		-webkit-transform: rotate(135deg);

		position: absolute;
		left: 20px;
		top: 20px;

		cursor: pointer;
	}
</style>
