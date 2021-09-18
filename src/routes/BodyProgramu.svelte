<script>
	import { onMount } from "svelte";
	import { navigate } from "svelte-routing";
	import StranaHeader from "../components/StranaHeader.svelte";

	export let strana;

	let bodyProgramu = [];

	onMount(() => {
		window.scrollTo(0, 0);
		getData();
	});

	async function getData() {
		const res = await fetch(
			"https://slibotechnyapi.pythonanywhere.com/get_body_programu/" +
				strana,
			{
				method: "POST",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json = await res.json();
		console.log(json);
		bodyProgramu = json.body_programu;
		strana = json.strana;
	}
</script>

<section>
	<StranaHeader {strana} />

	<div id="body-programu">
		{#each bodyProgramu as bp}
			<div
				class="bod-programu"
				on:click={() => {
					navigate("/upravit-bod/" + bp.id);
				}}
			>
				<div
					class="kategorie-stitek"
					style="background-color: {bp.kategorie_barva};"
				>
					<span id="hide-phone"> {bp.kategorie}</span>
				</div>
				<span
					class="nadpis"
					style="background-color: {strana.barva}; color: {strana.sekundarni_barva};"
				>
					{bp.nadpis}
				</span>
				{#if bp.splneno == 1}
					<div
						class="splneno"
						style="background-color: rgb(76, 175, 80);"
					>
						<img src={"/images/tick.png"} alt="Fajfka" />
					</div>
				{:else if bp.splneno == 2}
					<div
						class="splneno"
						style="background-color: rgb(223, 71, 89);"
					>
						<img src={"/images/cross.png"} alt="Křížek" />
					</div>
				{:else if bp.splneno == 3}
					<div
						class="splneno"
						style="background-color: rgb(255, 193, 7);"
					>
						<img src={"/images/qm.png"} alt="Otazník" />
					</div>
				{:else if bp.splneno == 4}
					<div
						class="splneno"
						style="background-color: rgb(130, 136, 144);"
					>
						<img src={"/images/-.png"} alt="Pomlčka" />
					</div>
				{/if}
			</div>
		{/each}
	</div>
</section>

<div
	id="novy-bod"
	on:click={() => {
		navigate("/novy-bod/" + strana.nazev);
	}}
>
	<span id="plus" />
</div>

<style>
	section {
		margin: 0px calc((100% - 70rem) / 2) 0px calc((100% - 70rem) / 2);
		padding-top: 50px;
	}

	#body-programu {
		margin-top: 30px;
	}

	.bod-programu {
		height: 40px;
		display: flex;
		margin-bottom: 15px;

		cursor: pointer;
		transition: 0.15s;
	}
	.bod-programu:hover {
		-webkit-filter: brightness(85%);
		filter: brightness(85%);
	}

	.kategorie-stitek {
		height: 100%;
		line-height: 40px;
		min-width: 160px;
		border-radius: 2px 0 0 2px;
		padding: 0 10px;

		color: #ffffff;
		font-weight: 500;
		font-size: 20px;
	}
	.nadpis {
		line-height: 40px;
		padding-left: 12px;
		width: calc(100% - 80px);

		font-size: 20px;
		font-weight: 500;
	}
	.splneno {
		position: relative;
		width: 70px;
		border-radius: 0 2px 2px 0;
	}
	.splneno img {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translateY(-50%) translateX(-50%);
	}

	#novy-bod {
		background-color: #4caf50;
		color: #ffffff;

		font-family: "Rokkitt";
		font-size: 24px;

		position: fixed;
		right: 30px;
		bottom: 30px;
		margin: 0;
		padding: 0;
		/* padding: 8px 15px; */
		border-radius: 5px;

		cursor: pointer;
		transition: 0.15s;
	}
	#plus {
		display: inline-block;
		vertical-align: middle;
		width: 50px;
		height: 50px;
		background: linear-gradient(#ffffff, #ffffff),
			linear-gradient(#ffffff, #ffffff);
		background-position: center;
		background-size: 50% 4px, 4px 50%; /*thickness = 2px, length = 50% (25px)*/
		background-repeat: no-repeat;
	}
	#novy-bod:hover {
		-webkit-filter: brightness(75%);
		filter: brightness(75%);
	}

	@media (max-width: 1200px) {
		section {
			margin: 0 20px;
		}
	}

	@media (max-width: 800px) {
		.bod-programu {
			height: 40px;
			display: flex;
			margin-bottom: 15px;
			font-size: 1px;
		}
		.kategorie-stitek {
			width: 12px;
			min-width: 5px;
			padding: 0;
		}
		#hide-phone {
			display: none;
		}
		.splneno {
			width: 50px;
		}
	}
</style>
