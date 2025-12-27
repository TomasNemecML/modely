<script>
	import "./layout.css";
	let { children } = $props();
</script>

<div class="min-h-screen flex flex-col">
	<header class="glass-header p-6">
		<div class="max-w-2xl mx-auto flex justify-between items-center">
			<h1 class="text-2xl font-black tracking-tighter uppercase italic">
				<a href="/" class="text-gradient">Jurkova modelova zbierka</a>
			</h1>
		</div>
	</header>

	<main class="flex-grow p-4 max-w-2xl mx-auto w-full">
		{@render children()}
	</main>

	<footer class="p-12 text-center text-slate-400 text-xs uppercase tracking-widest border-t border-slate-100 mt-12">
		<div class="mb-2 font-bold text-slate-500">Nemec Development</div>
		&copy; {new Date().getFullYear()} Všetky práva vyhradené
	</footer>
</div>
