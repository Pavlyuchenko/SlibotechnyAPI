<script>
	import { onMount, tick } from "svelte";
	import { navigate } from "svelte-routing";
	import StranaHeader from "../components/StranaHeader.svelte";

	export let strana;
	let kategorie = [];

	let selectedKategorie;

	let splneno = [
		{
			obrazek: "tick.png",
			barva: "#4CAF50",
			cislo: 1,
			symbol: "✓",
		},
		{
			obrazek: "cross.png",
			barva: "#DF4759",
			cislo: 2,
			symbol: "X",
		},
		{
			obrazek: "qm.png",
			barva: "#FFC107",
			cislo: 3,
			symbol: "?",
		},
		{
			obrazek: "-.png",
			barva: "#828890",
			cislo: 4,
			symbol: "-",
		},
	];

	let selectedSplneno = splneno[3];

	let priorita = 5;
	let prioritaStrany = 5;

	onMount(() => {
		window.scrollTo(0, 0);
		getData();

		document.getElementById("telo").addEventListener("paste", function (e) {
			// cancel paste
			e.preventDefault();

			// get text representation of clipboard
			var text = (e.originalEvent || e).clipboardData.getData(
				"text/plain"
			);

			// insert text manually
			document.execCommand("insertHTML", false, text);
		});
	});

	async function getData() {
		const res = await fetch(
			"http://127.0.0.1:5000/get_novy_bod/" + strana,
			{
				method: "GET",
				headers: {
					"content-type": "application/json",
				},
			}
		);
		const json = await res.json();
		strana = json.strana;
		kategorie = json.kategorie;
		selectedKategorie = kategorie[0];
	}

	async function createBod() {
		const res = await fetch("http://127.0.0.1:5000/post_bod_programu", {
			method: "POST",
			headers: {
				"content-type": "application/json",
			},
			body: JSON.stringify({
				strana: strana.nazev,
				nadpis: nadpis,
				telo: telo,
				kategorie: selectedKategorie.jmeno,
				splneno: selectedSplneno.cislo,
				priorita: priorita,
				prioritaStrany: prioritaStrany,
			}),
		});
		window.history.back();
	}

	var nadpis;
	var telo;
</script>

<section style="color: {strana.barva};">
	<StranaHeader {strana} />
	<p
		id="p-nadpis"
		class="label"
		on:click={() => {
			document.getElementById("nadpis").focus();
		}}
	>
		Nadpis
	</p>
	<input
		type="text"
		id="nadpis"
		style="border: 4px solid {strana.barva}"
		bind:value={nadpis}
	/>
	<p
		class="label"
		on:click={() => {
			document.getElementById("telo").focus();
		}}
	>
		Tělo
	</p>
	<div
		contenteditable="true"
		id="telo"
		style="border: 4px solid {strana.barva}"
		bind:innerHTML={telo}
	/>

	<div class="flex">
		<div>
			<p class="label">Kategorie</p>
			<select
				name="kategorie"
				id="kategorie"
				style="background: url('/images/arrow.png') {selectedKategorie?.barva} no-repeat 95% !important;"
				bind:value={selectedKategorie}
			>
				{#each kategorie as k}
					<option value={k} style="background-color: {k.barva};">
						{k.jmeno}
					</option>
				{/each}
			</select>
		</div>

		<div>
			<p class="label" id="p-splneno">Splněno</p>
			<select
				name="splneno"
				id="splneno"
				style="background: url('/images/arrow_black.png') {selectedSplneno?.barva} no-repeat 95% !important;"
				bind:value={selectedSplneno}
			>
				{#each splneno as s}
					<option
						value={s}
						style="background-color: {s.barva}; background-image:url(/images/tick.png);"
					>
						{s.symbol}
					</option>
				{/each}
			</select>
		</div>

		<div>
			<p class="label" id="p-priorita">Priorita</p>
			<input
				bind:value={priorita}
				type="range"
				id="priorita"
				name="priorita"
				min="0"
				max="10"
			/>
			<p id="priorita-value">{priorita}</p>
		</div>

		<div>
			<p class="label" id="p-priorita-strany">Priorita Strany</p>
			<input
				bind:value={prioritaStrany}
				type="range"
				id="priorita-strany"
				name="priorita-strany"
				min="0"
				max="10"
			/>
			<p id="priorita-strany-value">{prioritaStrany}</p>
		</div>
	</div>

	<button
		id="ulozit"
		on:click={() => {
			createBod();
		}}
	>
		Uložit
	</button>
</section>

<style>
	section {
		margin: 0px calc((100% - 70rem) / 2) 0px calc((100% - 70rem) / 2);
		padding-top: 50px;
	}

	.label {
		font-size: 20px;
		font-weight: 700;
		margin-top: 20px;
		margin-bottom: 5px;
	}
	#p-nadpis {
		margin-top: 30px;
	}
	#nadpis {
		width: 100%;
		color: #2d2d2d;
		font-size: 20px;
		font-family: "Barlow";

		padding: 8px 10px;
		box-sizing: border-box;
		border-radius: 5px;
		transition: 0.15s;
	}
	#nadpis:focus {
		-webkit-filter: brightness(90%);
		filter: brightness(90%);
	}

	#telo {
		background-color: #ffffff;
		color: #2d2d2d;

		width: 100%;
		font-size: 20px;
		font-family: "Barlow";

		padding: 8px 10px;
		box-sizing: border-box;
		border-radius: 5px;

		height: 200px;
		overflow-y: scroll;

		transition: 0.15s;
	}
	#telo:focus {
		-webkit-filter: brightness(90%);
		filter: brightness(90%);
	}

	#kategorie {
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;

		border: 0;
		border-radius: 3px;
		color: #ffffff;

		width: 250px;
		height: 45px;
		padding: 0 10px;

		font-size: 22px;
		font-family: "Barlow";
		font-weight: 500;
	}
	#p-splneno {
		margin-left: 40px;
	}
	#splneno {
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;

		border: 0;
		border-radius: 3px;
		color: #ffffff;

		width: 80px;
		height: 45px;
		padding: 0 10px;
		margin-left: 40px;

		font-size: 22px;
		font-family: "Barlow";
		font-weight: 500;
	}

	.flex {
		display: flex;
	}

	#p-priorita {
		margin-left: 40px;
	}
	#priorita {
		margin-left: 40px;
		color: red;
	}
	#priorita-value {
		margin-left: 40px;
		font-size: 24px;
		font-weight: 700;
		text-align: center;
	}

	#p-priorita-strany {
		margin-left: 40px;
	}
	#priorita-strany {
		margin-left: 40px;
		color: red;
	}
	#priorita-strany-value {
		margin-left: 40px;
		font-size: 24px;
		font-weight: 700;
		text-align: center;
	}

	#ulozit {
		float: right;
		border: 0;
		border-radius: 3px;
		background-color: #4caf50;

		color: #ffffff;
		width: 120px;
		height: 40px;

		font-family: "Barlow";
		font-weight: 700;
		font-size: 22px;

		cursor: pointer;
		transition: 0.15s;
	}
	#ulozit:hover {
		-webkit-filter: brightness(90%);
		filter: brightness(90%);
	}

	@media (max-width: 1200px) {
		section {
			margin: 0 20px;
		}
		.flex {
			flex-direction: column;
			justify-content: flex-start;
		}
		.flex div p,
		.flex div select,
		.flex div input {
			margin-left: 0 !important;
		}
		#priorita-value {
			text-align: left;
		}
		#priorita-strany-value {
			text-align: left;
		}
		#ulozit {
			margin-bottom: 20px;
		}
	}
</style>
