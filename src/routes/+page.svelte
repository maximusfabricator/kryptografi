<script>
	let metode = $state('caesar');
	let tekst = $state('');
	let nokkel = $state('');
	let resultat = $state('');
	let feil = $state('');
	let generert = $state('');

	function flytt(c, n) {
		let stor = c === c.toUpperCase();
		let pos = c.toUpperCase().charCodeAt(0) - 65;
		let ny = (pos + n) % 26;
		if (ny < 0) ny += 26;
		return String.fromCharCode(ny + (stor ? 65 : 97));
	}

	function kjor(dekrypter) {
		feil = '';
		resultat = '';
		generert = '';

		if (!tekst.trim()) {
			feil = 'Teksten kan ikke være tom.';
			return;
		}
		if (!/^[a-zA-Z ]+$/.test(tekst)) {
			feil = 'Teksten kan bare inneholde bokstaver og mellomrom.';
			return;
		}

		let brukt = nokkel;

		if (metode === 'caesar') {
			let n = Number(nokkel);
			if (nokkel.trim() === '') {
				feil = 'Du må skrive inn en nøkkel.';
				return;
			}
			if (!Number.isInteger(n) || n < 0 || n > 25) {
				feil = 'Nøkkelen må være et heltall fra 0 til 25.';
				return;
			}
		} else if (metode === 'otp' && !dekrypter) {
			brukt = '';
			for (let c of tekst) {
				if (c !== ' ') brukt += String.fromCharCode(65 + Math.floor(Math.random() * 26));
			}
			generert = brukt;
		} else {
			if (nokkel.trim() === '') {
				feil = 'Du må skrive inn en nøkkel.';
				return;
			}
			if (!/^[a-zA-Z]+$/.test(nokkel)) {
				feil = 'Nøkkelen kan bare inneholde bokstaver fra A til Z.';
				return;
			}
			if (metode === 'otp' && nokkel.length !== tekst.replace(/ /g, '').length) {
				feil = 'Nøkkelen må ha like mange bokstaver som teksten.';
				return;
			}
		}

		let ut = '';
		let i = 0;
		for (let c of tekst) {
			if (c === ' ') {
				ut += ' ';
				continue;
			}
			let skift = Number(nokkel);
			if (metode !== 'caesar') {
				let bokstav = brukt[metode === 'vigenere' ? i % brukt.length : i];
				skift = bokstav.toUpperCase().charCodeAt(0) - 65;
			}
			if (dekrypter) skift = -skift;
			ut += flytt(c, skift);
			i++;
		}
		resultat = ut;
	}
</script>

<h1>Kryptografiverktøy</h1>

<label>
	Metode
	<select bind:value={metode}>
		<option value="caesar">Caesar</option>
		<option value="vigenere">Vigenère</option>
		<option value="otp">One-Time Pad</option>
	</select>
</label>

<label>
	Tekst
	<textarea bind:value={tekst}></textarea>
</label>

<label>
	Nøkkel
	<input bind:value={nokkel} />
</label>

{#if metode === 'otp'}
	<p>Ved kryptering lages nøkkelen automatisk.</p>
{/if}

<button onclick={() => kjor(false)}>Krypter</button>
<button onclick={() => kjor(true)}>Dekrypter</button>

{#if feil}
	<p class="feil">{feil}</p>
{/if}
{#if generert}
	<p>Generert nøkkel: {generert}</p>
{/if}
{#if resultat}
	<p>Resultat: {resultat}</p>
{/if}

<style>
	label, textarea, input, select {
		display: block;
	}
	textarea {
		width: 300px;
		height: 80px;
	}
	button {
		margin-top: 8px;
	}
	.feil {
		color: red;
	}
	p {
		white-space: pre-wrap;
	}
</style>
