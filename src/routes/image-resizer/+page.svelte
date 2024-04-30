<script>
	let fileSelected = false;
	let width = 0;
	let height = 0;

	const handleFileChange = (event) => {
		fileSelected = event.target.files.length > 0;
	};
</script>

<svelte:head><title>Alterease | Image</title></svelte:head>

<div class="min-h-screen text-center">
	<div class="m-10">
		<h1 class="font-bold text-xl">Image Resizer</h1>
	</div>

	<input
		type="file"
		accept="image/png, image/jpeg"
		class="file-input file-input-bordered w-full max-w-xs"
		on:change={handleFileChange}
	/>

	<div class="p-4 space-x-2">
		<input
			id="width"
			type="number"
			placeholder="Width"
			class="input input-bordered w-full max-w-[12rem]"
			bind:value={width}
			disabled={!fileSelected}
		/>
		<input
			id="height"
			type="number"
			placeholder="Height"
			class="input input-bordered w-full max-w-[12rem]"
			bind:value={height}
			disabled={!fileSelected}
		/>
	</div>

	<div class="p-4">
		{#if fileSelected}
			<button class="btn btn-wide btn-primary">Resize</button>
		{:else}
			<button class="btn btn-wide btn-disabled">Resize</button>
		{/if}
	</div>
</div>
