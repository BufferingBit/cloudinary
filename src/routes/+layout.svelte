<script lang="ts">
	import '../app.css';
</script>

<header class="bg-gray-900 text-white shadow-md sticky top-0 z-50">
	<div class="max-w-6xl mx-auto px-4 py-3 flex justify-center items-center">
		<a href="/" class="text-xl font-bold">📦 CloudVault</a>
	</div>
</header>

<main class="min-h-[calc(100vh-60px)] bg-gray-50 dark:bg-gray-950 text-gray-900 dark:text-gray-100">
	<slot />
</main>

