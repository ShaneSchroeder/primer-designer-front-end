<script>
	import { sequence } from '@sveltejs/kit/hooks';
	import { Button, TextArea } from 'carbon-components-svelte';
	import { Slider, Modal, ProgressBar } from 'carbon-components-svelte';

	const API_URL =
		process.env.NODE_ENV === 'production'
			? 'http://primered-api.shane-schroeder.com' // Kubernetes internal service
			: 'http://127.0.0.1:8000'; // Local development
	let dnaSequence = '';
	let primerLength = 20;
	let gcContent = 50.0;

	/**
	 * @type {any}
	 */
	let result;

	/**
	 * @type {boolean}
	 */
	let loadingPrimers;

	/**
	 * @type {boolean}
	 */
	let showFailRequestModal;

	async function submitForm() {
		const payload = {
			sequence: dnaSequence,
			primer_length: primerLength,
			gc_content: gcContent
		};

		try {
			loadingPrimers = true;
			const response = await fetch(`${API_URL}/design-primer/`, {
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
				showFailRequestModal = true;
				console.error('Failed to design primers:', response.statusText);
			}
		} catch (error) {
			showFailRequestModal = true;
			console.error('Error:', error);
		} finally {
			loadingPrimers = false;
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
{#if loadingPrimers}
	<ProgressBar />
{/if}
{#if result && !loadingPrimers}
	<hr />
	<div>
		<h1>Results</h1>
		<div class="row">
			<div class="col-4">
				<div class="card">
					<div class="card-body">
						<h4 class="card-title">Left Primer</h4>
						<p class="card-text">
							{#if result.left_primer.length > 0}
								Sequence: {result.left_primer.sequence}<br />
								GC%: {result.left_primer.gc_percent}<br />
								Start Position: {result.left_primer.start}<br />
								TM: {result.left_primer.tm}<br />
								Penalty: {result.left_primer.penalty}
							{:else}No Data{/if}
						</p>
					</div>
				</div>
			</div>
			<div class="col-4">
				<div class="card">
					<div class="card-body">
						<h4 class="card-title">Internal Primers</h4>
						<p class="card-text">
							{#if result.internal_primers.length > 0}
								{#each result.internal_primers as ip, i}
									Primer {i}<br />
									Sequence: {ip.sequence}<br />
									GC%: {ip.gc_percent}<br />
									Start Position: {ip.start}<br />
									TM: {ip.tm}<br />
									Penalty: {ip.penalty}
								{/each}
							{:else}No Data{/if}
						</p>
					</div>
				</div>
			</div>
			<div class="col-4">
				<div class="card">
					<div class="card-body">
						<h4 class="card-title">Right Primer</h4>
						<p class="card-text">
							{#if result.right_primer.length > 0}
								Sequence: {result.right_primer.sequence}<br />
								GC%: {result.right_primer.gc_percent}<br />
								Start Position: {result.right_primer.start}<br />
								TM: {result.right_primer.tm}<br />
								Penalty: {result.right_primer.penalty}
							{:else}No Data{/if}
						</p>
					</div>
				</div>
			</div>
		</div>
	</div>
{/if}

<Modal bind:open={showFailRequestModal}>
	Your Primer request failed. Please try again or contact the customer support.
</Modal>
