<script lang="ts">
	import { enhance } from '$app/forms';

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

<div class="container">
	<div
		class="box dropzone"
		role="button"
		tabindex="0"
		ondragover={handleDragOver}
		ondrop={handleDrop}
	>
		Drag & drop your image here, or use the file selector below
	</div>
</div>

<!-- Your existing form with enhance -->
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
>
	<input bind:this={fileInput} type="file" name="user_photo" accept="image/*" required />
	<input type="submit" value="Upload Photo" onclick={onButtonClick}/>
</form>

{#if loading}
	<p>Uploading...</p>
{/if}

{#if result?.success}
	<p>✅ Upload successful!</p>
	<a href={result.url} target="_blank">{result.url}</a>
	<img src={result.url} alt="Uploaded" style="max-width: 100%; margin-top: 1rem;" />
{:else if result?.error}
	<p>❌ Error: {result.error}</p>
{/if}

<style>
	.container {
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.box {
		width: 1000px;
		height: 800px;
		border: 10px solid black;
		position: relative;
		z-index: 1;
	}
	.dropzone {
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
