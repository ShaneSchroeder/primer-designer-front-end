<script>
	import { Button, TextArea } from 'carbon-components-svelte';
	import { Slider } from 'carbon-components-svelte';

	const API_URL =
		process.env.NODE_ENV === 'production'
			? 'http://backend-service:8000' // Kubernetes internal service
			: 'http://127.0.0.1:8000'; // Local development
	let dnaSequence = '';
	let primerLength = 20;
	let gcContent = 50.0;

	/**
	 * @type {any}
	 */
	let result;

	async function submitForm() {
		const payload = {
			sequence: dnaSequence,
			primer_length: primerLength,
			gc_content: gcContent
		};

		try {
			const response = await fetch('http://127.0.0.1:8000/design-primer/', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify(payload)
			});

			if (response.ok) {
				result = await response.json();
				console.log('Designed Primers:', result);
				// Display the result in your UI
			} else {
				console.error('Failed to design primers:', response.statusText);
			}
		} catch (error) {
			console.error('Error:', error);
		}
	}
</script>

<form on:submit|preventDefault={submitForm}>
	<TextArea
		labelText="DNA sequence"
		bind:value={dnaSequence}
		placeholder="Enter a DNA sequence..."
		required
	/>
	<Slider labelText="Primer Length" bind:value={primerLength} min={10} max={30} required />
	<Slider labelText="GC Content (%)" bind:value={gcContent} min={40} max={60} step={0.1} required />
	<Button type="submit">Design Primers</Button>
</form>

{#if result}
	<hr />
	<div>
		<h1>Results</h1>
		<div class="row">
			<div class="col-4">
				{#if result.left_primer.length > 0}
					<div class="card">
						<div class="card-body">
							<h4 class="card-title">Left Primer</h4>
							<p class="card-text">{result.left_primer.sequence}</p>
						</div>
					</div>
				{:else}
					<h1>No Data</h1>
				{/if}
			</div>
			<div class="col-4">
				{#if result.internal_primers.length > 0}
					<div class="card">
						<div class="card-body">
							<h4 class="card-title">Internal Primers</h4>
							<p class="card-text">{result.internal_primers.sequence}</p>
						</div>
					</div>
				{:else}
					<h1>No Data</h1>
				{/if}
			</div>
			<div class="col-4">
				{#if result.right_primer.length > 0}
					<div class="card">
						<div class="card-body">
							<h4 class="card-title">Right Primer</h4>
							<p class="card-text">{result.right_primer.sequence}</p>
						</div>
					</div>
				{:else}
					<h1>No Data</h1>
				{/if}
			</div>
		</div>
	</div>
{/if}
