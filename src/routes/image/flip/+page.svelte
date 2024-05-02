<script>
	import { PUBLIC_API_URL } from '$env/static/public';

	let fileSelected = false;
	let loading = false;
	let isError = false;
	let isFlipped = false;
	let flipDirection = '';
	let imageUrl = '';

	const handleFileChange = (event) => {
		fileSelected = event.target.files.length > 0;
	};

	const handleFlip = async () => {
		if (!fileSelected) return;

		const fileInput = document.querySelector('input[type="file"]');
		const file = fileInput.files[0];

		const formData = new FormData();
		formData.append('image', file);
		formData.append('flipDirection', flipDirection);

		loading = true;

		try {
			const response = await fetch(`${PUBLIC_API_URL}/image/flip`, {
				method: 'POST',
				body: formData
			});

			if (response.ok) {
				const responseData = await response.json();
				imageUrl = responseData.image.url;
				isFlipped = true;
			} else {
				isError = true;
				isFlipped = false;
			}
		} catch (error) {
			isError = true;
			isFlipped = false;
			console.error(error);
		} finally {
			loading = false;
		}
	};

	const handleReloadPage = () => {
		const fileInput = document.querySelector('input[type="file"]');
		fileInput.value = '';
		location.reload();
	};
</script>

<svelte:head><title>Alterease | Image Flip</title></svelte:head>

<div class="min-h-screen text-center">
	<div class="m-10">
		<h1 class="font-bold text-xl">Image Flip</h1>
	</div>

	<div class="p-4">
		<h2 class="text-md mb-4">Select image file:</h2>
		<input
			type="file"
			accept="image/png, image/jpeg"
			class="file-input file-input-bordered w-full max-w-xs"
			on:change={handleFileChange}
		/>
	</div>

	<div class="p-4">
		<h2 class="text-md mb-4">Select flip direction:</h2>
		<div class="join">
			<input
				class="join-item btn"
				type="radio"
				name="options"
				aria-label="Horizontal"
				value="H"
				disabled={!fileSelected}
				bind:group={flipDirection}
			/>
			<input
				class="join-item btn"
				type="radio"
				name="options"
				aria-label="Vertical"
				value="V"
				disabled={!fileSelected}
				bind:group={flipDirection}
			/>
		</div>
	</div>

	<div class="p-4">
		{#if !isFlipped}
			{#if fileSelected}
				{#if loading}
					<button class="btn btn-wide btn-disabled">
						<span class="loading loading-spinner loading-md"></span> Processing
					</button>
				{:else}
					<button class="btn btn-wide btn-accent" on:click={handleFlip}>Flip</button>
				{/if}
			{:else}
				<button class="btn btn-wide btn-disabled">Flip</button>
			{/if}
		{:else}
			<div class="p-2">
				<a href={imageUrl} target="_blank">
					<button class="btn btn-wide btn-success">Download</button>
				</a>
			</div>
			<div class="p-2">
				<button class="btn btn-wide btn-primary" on:click={handleReloadPage}>
					Flip another image
				</button>
			</div>
		{/if}
	</div>

	{#if isError}
		<div class="p-4">
			<h2 class="text-md mb-4 text-error">An error occurred, please try again!</h2>
		</div>
	{/if}
</div>
