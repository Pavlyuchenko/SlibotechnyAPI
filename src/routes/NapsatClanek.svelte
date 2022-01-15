<script>
	let text = "";
	let podnadpis = "";
	let nadpis = "";
	let autor = "";
	let obrazek = "";

	async function createClanek() {
		if (
			text.length < 1 ||
			podnadpis.length < 1 ||
			nadpis.length < 1 ||
			autor.length < 1 ||
			obrazek.length < 1
		) {
			return;
		}

		await fetch("https://slibotechnyapi.pythonanywhere.com/create_clanek", {
			method: "POST",
			headers: {
				"content-type": "application/json",
			},
			body: JSON.stringify({
				text: text,
				podtitulek: podnadpis,
				nadpis: nadpis,
				autor: autor,
				obrazek: obrazek,
			}),
		})
			.then(() => {
				window.history.back();
			})
			.catch((error) => {
				console.log(error);
			});
	}

	let showHowTo = false;
</script>

<section>
	<h1>Napsat článek</h1>

	<p
		class="label"
		on:click={() => {
			document.getElementById("nadpis").focus();
		}}
	>
		Nadpis
	</p>
	<input
		type="text"
		name="Nadpis"
		id="nadpis"
		class="input"
		bind:value={nadpis}
	/>

	<p
		class="label"
		on:click={() => {
			document.getElementById("autor").focus();
		}}
	>
		Autor
	</p>
	<input
		type="text"
		name="Autor"
		id="autor"
		class="input"
		bind:value={autor}
	/>

	<p
		class="label"
		on:click={() => {
			document.getElementById("obrazek").focus();
		}}
	>
		Obrázek
	</p>
	<input
		type="text"
		name="Obrázek"
		id="obrazek"
		class="input"
		bind:value={obrazek}
	/>
	<u
		style="font-size: 24px; font-weight: 700; cursor: pointer;"
		on:click={() => {
			showHowTo = !showHowTo;
		}}
	>
		Jak nahrát obrázek?
	</u>

	{#if showHowTo}
		<div id="how-to">
			Nejprve si stáhněte libovolný obrázek z internetu ("Uložit
			jako..."). Pak přejděte na stránku
			<a href="https://www.iloveimg.com/crop-image" target="blank">
				<u>https://www.iloveimg.com/crop-image</u>
			</a>
			a stažený obrázek tam nahrejte. Nyní obrázek ořízněte do čtvercové podoby
			a klikněte vpravo dole na Crop IMAGE. To obrázek ořízne a nabídne ke
			stažení. Až obrázek stáhnete, přejděte na stránku
			<a href="https://imgbb.com/" target="blank">
				<u>https://imgbb.com/</u>
			</a>. Tam svůj obrázek nahrejte (při nahrávání zvolte "Automaticky
			nemazat"). Až bude hotovo, vytvoří se odkaz ve formátu
			https://ibb.co/2h4PmH7. To však ještě NENÍ výsledný odkaz! Na tento
			vygenerovaný odkaz přejděte, klikněte na váš obrázek pravým
			tlačítkem a zvolte "Kopírovat adresu obrázku". Tu pak vložte zde na
			stránku do pole "Obrázek".
		</div>
	{/if}

	<p
		class="label"
		on:click={() => {
			document.getElementById("podnadpis").focus();
		}}
	>
		Podnadpis
	</p>
	<div
		contenteditable="true"
		id="podnadpis"
		class="cedit"
		bind:innerHTML={podnadpis}
	/>
	<p
		class="label"
		on:click={() => {
			document.getElementById("text").focus();
		}}
	>
		Text
	</p>
	<div contenteditable="true" id="text" class="cedit" bind:innerHTML={text} />

	<button id="publish" on:click={createClanek}>Publikovat</button>

	<div id="warning">
		Varování: Pokud odejdete pryč z této stránky bez publikování, všechna
		data budou ztracena! Doporučuji proto psát článek někde jinde, třeba ve
		Wordu, a pak jen vše přenést zde a publikovat.
	</div>
</section>

<style>
	section {
		padding: 0px calc((100% - 85rem) / 2) 0px calc((100% - 85rem) / 2);
	}

	h1 {
		font-family: "Spectral";
		margin-top: 0;
		margin-bottom: 20px;
		font-size: 75px;
	}
	.label {
		font-size: 24px;
		font-weight: 700;
		margin-top: 20px;
		margin-bottom: 5px;
	}
	.cedit {
		background-color: #ffffff;
		color: #2d2d2d;

		width: 100%;
		font-size: 20px;
		font-family: "Barlow";

		padding: 8px 10px;
		box-sizing: border-box;
		border-radius: 5px;
		border: 3px solid #000;

		height: 150px;
		overflow-y: scroll;

		transition: 0.15s;
	}
	.cedit:focus {
		-webkit-filter: brightness(90%);
		filter: brightness(90%);
	}
	.input {
		background-color: #ffffff;
		color: #2d2d2d;

		width: 100%;
		font-size: 20px;
		font-family: "Barlow";

		padding: 8px 10px;
		box-sizing: border-box;
		border-radius: 5px;
		border: 3px solid #000;

		transition: 0.15s;
	}
	.input:focus {
		-webkit-filter: brightness(90%);
		filter: brightness(90%);
	}

	#warning {
		position: fixed;
		bottom: 0;
		left: 0;

		background-color: #ff1744;
		color: #fff;
		width: 100%;
		font-size: 24px;
		padding: 10px 15px;
		font-weight: 500;
	}

	#publish {
		border: 0;
		background-color: #000;
		color: #fff;
		font-family: "Spectral";
		font-weight: 500;
		font-size: 24px;
		margin-top: 20px;
		margin-bottom: 250px;
		float: right;
		cursor: pointer;
	}

	#how-to {
		background-color: #fff;
		padding: 10px 15px;
		font-size: 22px;
	}
</style>
