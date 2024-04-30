<script>
	let fileSelected = false;
	let loading = false;
	let isResized = false;
	let width = 0;
	let height = 0;
	let imageUrl = '';

	const handleFileChange = (event) => {
		fileSelected = event.target.files.length > 0;
	};

	const handleResize = async () => {
		if (!fileSelected) return;

		const fileInput = document.querySelector('input[type="file"]');
		const file = fileInput.files[0];

		const formData = new FormData();
		formData.append('image', file);
		formData.append('width', width);
		formData.append('height', height);

		loading = true;

		try {
			const response = await fetch('http://localhost:3000/image/resize', {
				method: 'POST',
				body: formData
			});

			if (response.ok) {
				const responseData = await response.json();
				imageUrl = responseData.image.url;
				isResized = true;
			} else {
				// const responseData = await response.json();
				isResized = false;
			}
		} catch (error) {
			isResized = false;
			console.error(error);
		} finally {
			loading = false;
		}
	};

	const handleReloadPage = () => {
		location.reload();
	};
</script>

<svelte:head><title>Alterease | Image</title></svelte:head>

<div class="min-h-screen text-center">
	<div class="m-10">
		<h1 class="font-bold text-xl">Image Resizer</h1>
	</div>

	<div class="p-4">
		<input
			type="file"
			accept="image/png, image/jpeg"
			class="file-input file-input-bordered w-full max-w-xs"
			on:change={handleFileChange}
		/>
	</div>

	<div class="p-4 space-x-2 flex justify-center">
		<table class="table-auto">
			<tr>
				<td class="p-2">
					<label for="width">Width</label>
				</td>
				<td class="p-2">
					<input
						id="width"
						type="number"
						min="1"
						class="input input-bordered w-full max-w-[12rem]"
						disabled={!fileSelected}
						bind:value={width}
					/>
				</td>
			</tr>
			<tr>
				<td class="p-2">
					<label for="height">Height</label>
				</td>
				<td class="p-2">
					<input
						id="height"
						type="number"
						min="1"
						class="input input-bordered w-full max-w-[12rem]"
						disabled={!fileSelected}
						bind:value={height}
					/>
				</td>
			</tr>
		</table>
	</div>

	<div class="p-4">
		{#if !isResized}
			{#if fileSelected && width > 0 && height > 0}
				{#if loading}
					<button class="btn btn-wide btn-disabled">
						<span class="loading loading-spinner loading-md"></span> Processing
					</button>
				{:else}
					<button class="btn btn-wide btn-primary" on:click={handleResize}>Resize</button>
				{/if}
			{:else}
				<button class="btn btn-wide btn-disabled">Resize</button>
			{/if}
		{:else}
			<div class="p-2">
				<a href={imageUrl} download>
					<button class="btn btn-wide btn-success">Download</button>
				</a>
			</div>
			<div class="p-2">
				<button class="btn btn-wide btn-primary" on:click={() => location.reload()}>
					Resize again
				</button>
			</div>
		{/if}
	</div>
</div>
