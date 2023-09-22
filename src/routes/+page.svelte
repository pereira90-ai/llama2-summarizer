<script lang="ts">
	// backend IP
	let colabAPI: string;
	let apiEndpointConfigured = false;

	// component states
	let summarizedText: string = '';
	let textInput = '';

	let isLoading = false;

	const summarizeTextHttp = async () => {
		isLoading = true;
		const endpoint = new URL(`${colabAPI}/summarize`);
		endpoint.searchParams.append('text', textInput);

		try {
			const apiResponse = await fetch(endpoint, {
				headers: {
					'ngrok-skip-browser-warning': 'UNIMPORTANT_VALUE'
				}
			});

			const apiJson = await apiResponse.json();
			summarizedText = await apiJson.summarized_text;
		} catch (err) {
			// request failed
		} finally {
			isLoading = false;
		}
	};
</script>

<div class="container">
	<h1 class="pt-5">
		<b>Llama-2 Summarizer 🦙</b>
	</h1>

	{#if apiEndpointConfigured}
		<div class="mt-4">
			<textarea
				class="form-control"
				id="text-input"
				placeholder="Paste your paragraph here"
				bind:value={textInput}
			/>
		</div>

		<button class="btn btn-primary mt-3" disabled={isLoading} on:click={() => summarizeTextHttp()}>
			Summarize
		</button>

		{#if !isLoading && summarizedText}
			<div class="py-3">
				<b>Summarized Text: </b>
				<p>{summarizedText}</p>
			</div>
		{/if}
	{:else}
		<div class="mt-4">
			<textarea
				class="form-control"
				id="text-input"
				placeholder="Paste Your Google Colab ngrok URL here"
				bind:value={colabAPI}
			/>

			<button
				on:click={() => {
					apiEndpointConfigured = true;
				}}
				class="mt-3 btn btn-primary">Proceed</button
			>
		</div>
	{/if}
</div>
