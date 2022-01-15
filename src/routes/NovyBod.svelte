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

		document
			.getElementById("popis")
			.addEventListener("paste", function (e) {
				// cancel paste
				e.preventDefault();

				// get text representation of clipboard
				var text = (e.originalEvent || e).clipboardData.getData(
					"text/plain"
				);

				// insert text manually
				document.execCommand("insertHTML", false, text);
			});

		document
			.getElementById("navrh1")
			.addEventListener("paste", function (e) {
				e.preventDefault();
				var text = (e.originalEvent || e).clipboardData.getData(
					"text/plain"
				);
				document.execCommand("insertHTML", false, text);
			});
	});

	async function getData() {
		const res = await fetch(
			"https://slibotechnyapi.pythonanywhere.com/get_novy_bod/" + strana,
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
		const res = await fetch(
			"https://slibotechnyapi.pythonanywhere.com/post_bod_programu",
			{
				method: "POST",
				headers: {
					"content-type": "application/json",
				},
				body: JSON.stringify({
					strana: strana.nazev,
					nadpis: nadpis,
					popis: popis,
					navrhy: navrhy,
					citace: citace,
					odkaz: odkaz,
					kategorie: selectedKategorie.jmeno,
					splneno: selectedSplneno.cislo,
					priorita: priorita,
					prioritaStrany: prioritaStrany,
					komentar: komentar,
				}),
			}
		);
		window.history.back();
	}

	var nadpis = "";
	var popis = "";
	var citace = "";
	var odkaz = "";
	var komentar = "";

	var navrhy = [{ id: 1, text: "", splneno: 4 }];
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
		class="text-input"
		style="border: 4px solid {strana.barva}"
		bind:value={nadpis}
	/>
	<p
		class="label"
		on:click={() => {
			document.getElementById("popis").focus();
		}}
	>
		Popis
	</p>
	<div
		contenteditable="true"
		id="popis"
		style="border: 4px solid {strana.barva}"
		bind:innerHTML={popis}
	/>

	<div id="navrhy">
		<p
			id="p-citace"
			class="label"
			on:click={() => {
				document.getElementById("navrh1").focus();
			}}
		>
			Navrhy
		</p>
		<div>
			{#each navrhy as navrh}
				<div class="flex">
					<select
						class="splneno"
						style="background: {splneno[navrh.splneno - 1]
							?.barva} no-repeat 95% !important;"
						bind:value={navrh.splneno}
					>
						{#each splneno as s}
							<option
								value={s.cislo}
								style="background-color: {s.barva};"
							>
								{s.symbol}
							</option>
						{/each}
					</select>
					<input
						type="text"
						id={"navrh" + navrh.id}
						class="navrh"
						style="border: 4px solid {strana.barva}"
						bind:value={navrh.text}
					/>
				</div>
			{/each}
			<div
				on:click={() => {
					navrhy.push({
						id: navrhy[navrhy.length - 1].id + 1,
						text: "",
						splneno: 1,
					});
					navrhy = navrhy;
					setTimeout(() => {
						document
							.getElementById(
								"navrh" + navrhy[navrhy.length - 1].id
							)
							.focus();

						document
							.getElementById(
								"navrh" + navrhy[navrhy.length - 1].id
							)
							.addEventListener("paste", function (e) {
								e.preventDefault();
								var text = (
									e.originalEvent || e
								).clipboardData.getData("text/plain");
								document.execCommand("insertHTML", false, text);
							});
					}, 1);
				}}
				id="dalsi-navrh"
				style="background-color: {strana.barva}; color: {strana.sekundarni_barva};"
			>
				Další návrh
			</div>
		</div>
	</div>

	<p
		id="p-citace"
		class="label"
		on:click={() => {
			document.getElementById("citace").focus();
		}}
	>
		Citace
	</p>
	<textarea
		id="citace"
		class="text-input"
		style="border: 4px solid {strana.barva}"
		rows="3"
		bind:value={citace}
	/>
	<p
		id="p-odkaz"
		class="label"
		on:click={() => {
			document.getElementById("odkaz").focus();
		}}
	>
		Odkaz
	</p>
	<input
		type="text"
		id="odkaz"
		class="text-input"
		style="border: 4px solid {strana.barva}"
		bind:value={odkaz}
	/>

	<p
		class="label"
		on:click={() => {
			document.getElementById("komentar").focus();
		}}
	>
		Komentář
	</p>
	<div
		contenteditable="true"
		id="komentar"
		style="border: 4px solid {strana.barva}"
		bind:innerHTML={komentar}
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
	[contenteditable] {
		-webkit-user-select: text;
		user-select: text;
	}
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
	.text-input {
		width: 100%;
		color: #2d2d2d;
		font-size: 20px;
		font-family: "Barlow";

		padding: 8px 10px;
		box-sizing: border-box;
		border-radius: 5px;
		transition: 0.15s;
	}
	.text-input:focus {
		-webkit-filter: brightness(90%);
		filter: brightness(90%);
	}

	#popis {
		background-color: #ffffff;
		color: #2d2d2d;

		width: 100%;
		font-size: 20px;
		font-family: "Barlow";

		padding: 8px 10px;
		box-sizing: border-box;
		border-radius: 5px;

		height: 150px;
		overflow-y: scroll;

		transition: 0.15s;
	}
	#popis:focus {
		-webkit-filter: brightness(90%);
		filter: brightness(90%);
	}
	#komentar {
		background-color: #ffffff;
		color: #2d2d2d;

		width: 100%;
		font-size: 20px;
		font-family: "Barlow";

		padding: 8px 10px;
		box-sizing: border-box;
		border-radius: 5px;

		height: 150px;
		overflow-y: scroll;

		transition: 0.15s;
	}
	#komentar:focus {
		-webkit-filter: brightness(90%);
		filter: brightness(90%);
	}
	.navrh {
		background-color: #ffffff;
		color: #2d2d2d;

		width: 100%;
		font-size: 20px;
		font-family: "Barlow";

		padding: 8px 10px;
		margin-bottom: 5px;

		box-sizing: border-box;
		border-radius: 5px;

		overflow-y: scroll;

		transition: 0.15s;
	}
	.navrh:focus {
		-webkit-filter: brightness(90%);
		filter: brightness(90%);
	}
	#dalsi-navrh {
		padding: 8px 15px;
		display: inline-block;
		cursor: pointer;
		border-radius: 5px;
		transition: 0.15s;
	}
	#dalsi-navrh:hover {
		filter: brightness(75%);
		-webkit-filter: brightness(75%);
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
	.splneno {
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;

		border: 0;
		border-radius: 3px;
		color: #ffffff;

		width: 40px;
		height: 45px;
		padding: 0 10px;
		margin-right: 5px;

		font-size: 25px;
		font-family: "Barlow";
		font-weight: 500;
		text-align: center;
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
