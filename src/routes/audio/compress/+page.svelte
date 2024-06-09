<script>
	import { PUBLIC_API_URL } from '$env/static/public';

	const MAX_FILE_SIZE = 15 * 1024 * 1024;
	let fileSelected = false;
	let loading = false;
	let isError = false;
	let isCompressed = false;
	let level = '';
	let audioUrl = '';

	const handleFileChange = (event) => {
		fileSelected = event.target.files.length > 0;
		if (fileSelected) {
			const file = event.target.files[0];
			if (file.size > MAX_FILE_SIZE) {
				alert('File size exceeds the limit of 15MB.');
				event.target.value = '';
				fileSelected = false;
			} else {
				fileSelected = true;
			}
		} else {
			fileSelected = false;
		}
	};

	const handleCompress = async () => {
		if (!fileSelected) return;

		const fileInput = document.querySelector('input[type="file"]');
		const file = fileInput.files[0];

		const formData = new FormData();
		formData.append('audio', file);
		formData.append('level', level);

		loading = true;

		try {
			const response = await fetch(`${PUBLIC_API_URL}/audio/compress`, {
				method: 'POST',
				body: formData
			});

			if (response.ok) {
				const responseData = await response.json();
				audioUrl = responseData.audio.url;
				isCompressed = true;
			} else {
				isError = true;
				isCompressed = false;
			}
		} catch (error) {
			isError = true;
			isCompressed = false;
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

<svelte:head><title>Alterease | Audio Compress</title></svelte:head>

<div class="min-h-screen justify-center text-center">
	<div class="m-10">
		<h1 class="font-bold text-xl">Audio Compress</h1>
	</div>

	<div class="p-4">
		<h2 class="text-md mb-4">Select audio file (only MPEG audio):</h2>
		<input
			type="file"
			accept="audio/mpeg"
			class="file-input file-input-bordered w-full max-w-xs"
			on:change={handleFileChange}
		/>
		<h2 class="text-md mt-4 font-bold text-red-300">Limit 15 MB</h2>
		<h2 class="font-bold">
			*Dikarenakan resource server kecil (gratisan 😭), file besar akan lambat diunggah dan
			diproses.
		</h2>
	</div>

	<div class="p-4">
		<h2 class="text-md mb-4">Select compression level:</h2>
		<div class="join">
			<input
				class="join-item btn"
				type="radio"
				name="options"
				aria-label="Low"
				value="low"
				disabled={!fileSelected}
				bind:group={level}
			/>
			<input
				class="join-item btn"
				type="radio"
				name="options"
				aria-label="Medium"
				value="medium"
				disabled={!fileSelected}
				bind:group={level}
			/>
			<input
				class="join-item btn"
				type="radio"
				name="options"
				aria-label="High"
				value="high"
				disabled={!fileSelected}
				bind:group={level}
			/>
		</div>
	</div>

	<div class="p-4">
		{#if !isCompressed}
			{#if fileSelected}
				{#if loading}
					<button class="btn btn-wide btn-disabled">
						<span class="loading loading-spinner loading-md"></span> Processing
					</button>
				{:else}
					<button class="btn btn-wide btn-primary" on:click={handleCompress}>Compress</button>
				{/if}
			{:else}
				<button class="btn btn-wide btn-disabled">Compress</button>
			{/if}
		{:else}
			<div class="p-2">
				<a href={audioUrl} target="_blank">
					<button class="btn btn-wide btn-success">Download</button>
				</a>
			</div>
			<div class="p-2">
				<button class="btn btn-wide btn-primary" on:click={handleReloadPage}>
					Compress another audio
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
