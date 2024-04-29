<svelte:head><title>Alterease | Image</title></svelte:head>

<div class="min-h-screen text-center">
	<div class="m-10">
		<h1 class="font-bold text-xl">Image Resizer</h1>
	</div>

	<div class="md:px-16 px-8 mx-auto">
		<div class="bg-gray-700">Upload</div>
	</div>

	<div class="p-4 space-x-2">
		<input
			id="width"
			type="number"
			placeholder="Width"
			class="input input-bordered w-full max-w-[12rem]"
		/>
		<input
			id="height"
			type="number"
			placeholder="Height"
			class="input input-bordered w-full max-w-[12rem]"
		/>
	</div>

	<div class="p-4">
		<button class="btn btn-wide btn-primary">Resize</button>
	</div>
</div>
