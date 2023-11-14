<script>
	import '../app.css';
</script>

<header class="px-8 py-4">
	<a href="/" class="text-2xl font-bold">
		<span class="text-green-500">+Diff</span>checker
	</a>
</header>

<div class="px-8">
	<slot />
</div>

<footer class="text-center mx-8 mt-8 py-8 border-t text-sm text-gray-500">
	<span
		>&copy; {new Date().getFullYear()} Diffchecker by
		<a
			href="https://github.com/qhungg289"
			target="_blank"
			rel="noopener noreferrer"
			class="hover:underline"
		>
			@qhungg289
		</a>
	</span>
</footer>
