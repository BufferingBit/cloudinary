<script lang="ts">
	import { enhance } from '$app/forms';
	import { Button } from "$lib/components/ui/button";

	let loading = $state(false);

	interface UploadResult {
		success?: boolean;
		url?: string;
		error?: string;
	}

	let result = $state<UploadResult | undefined>(undefined);

	let fileInput: HTMLInputElement;
	let form: HTMLFormElement;

	function onButtonClick() {
		loading = true;
	}

	function handleDragOver(event: DragEvent) {
		event.preventDefault();
	}

	function handleDrop(event: DragEvent) {
		event.preventDefault();
		loading = true;
		const files = event.dataTransfer?.files;
		if (!files || files.length === 0) {
			loading = false;
			return;
		}

		const file = files[0];

		// Assign dropped file to the file input
		const dataTransfer = new DataTransfer();
		dataTransfer.items.add(file);
		fileInput.files = dataTransfer.files;

		// Programmatically submit the form (will trigger use:enhance)
		form.requestSubmit();
	}
</script>

<div class="max-w-xl mx-auto mt-10 p-6 bg-white dark:bg-gray-800 rounded-lg shadow-md text-center space-y-4 box">
	<h1 class="text-2xl font-bold mb-4">Upload Your File</h1>

	<div
		class="dropzone border-2 border-dashed border-gray-400 dark:border-gray-600 p-10 rounded-lg hover:bg-gray-100 dark:hover:bg-gray-700 transition cursor-pointer"
		role="button"
		tabindex="0"
		ondragover={handleDragOver}
		ondrop={handleDrop}
	>
		<p class="text-gray-600 dark:text-gray-300">Drag & drop your file here, or click below</p>
	</div>

	<form
		bind:this={form}
		action="?/photoUpload"
		method="post"
		enctype="multipart/form-data"
		use:enhance={() => {
			return async ({ result: actionResult }) => {
				if ('data' in actionResult) {
					result = actionResult.data as UploadResult;
				}
				loading = false;
			};
		}}
		class="space-y-4"
	>
		<input
			bind:this={fileInput}
			type="file"
			name="user_photo"
			required
			class="w-full text-sm file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-blue-600 file:text-white hover:file:bg-blue-700 transition"
		/>
		<!-- <button
			type="submit"
			onclick={onButtonClick}
			class="w-full bg-blue-600 hover:bg-blue-700 text-white font-semibold py-2 px-4 rounded-md transition"
		>
			{loading ? 'Uploading...' : 'Upload'}
		</button> -->
		<Button variant="ghost" type="submit" onclick={onButtonClick} class="w-full bg-blue-600 hover:bg-blue-700 text-white font-semibold py-2 px-4 rounded-md transition">{loading ? 'Uploading...' : 'Upload'}</Button>
	</form>

	{#if result?.success}
		<p class="text-green-600 font-medium">✅ Upload successful!</p>
		<a class="text-blue-500 underline" href={result.url} target="_blank">{result.url}</a>
		<img src={result.url} alt="Uploaded" class="mt-4 max-h-60 mx-auto rounded shadow" />
	{:else if result?.error}
		<p class="text-red-600 font-medium">❌ {result.error}</p>
	{/if}
</div>


<style>
	.dropzone {
		height: 50vh;
		margin: 1rem 0;
		padding: 2rem;
		border: 3px dashed #888;
		text-align: center;
		cursor: pointer;
		user-select: none;
	}
	.dropzone:hover {
		background-color: #f0f0f0;
	}
</style>
