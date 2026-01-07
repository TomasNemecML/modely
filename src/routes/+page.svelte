<script>
	import pb from "$lib/pb";

	async function getShelves() {
		try {
			const records = await pb.collection("Kategorie").getFullList({
				sort: "Cislo",
			});
			return records;
		} catch (e) {
			console.error(e);
			return [];
		}
	}

	const shelvesPromise = getShelves();
</script>

<div class="space-y-6 py-8">
	<div class="mb-10 text-center">
		<h2 class="text-4xl font-black uppercase tracking-tighter text-aviation-blue mb-2">Moja zbierka</h2>
		<div class="h-1 w-20 bg-aviation-accent mx-auto rounded-full"></div>
		<p class="text-slate-500 text-sm mt-4 font-medium">Prehliadajte si modely lietadiel podľa poschodí</p>
	</div>

	{#await shelvesPromise}
		<div class="flex justify-center py-20">
			<div class="animate-spin rounded-full h-10 w-10 border-b-2 border-aviation-accent"></div>
		</div>
	{:then shelves}
		<div class="grid gap-6">
			{#each shelves as shelf}
				<a
					href="/poschodie/{shelf.Cislo}/"
					class="premium-card p-2 flex items-center group relative overflow-hidden"
				>
					<div
						class="absolute top-0 left-0 w-1 h-full bg-aviation-accent opacity-0 group-hover:opacity-100 transition-opacity"
					></div>

					<div class="grow p-6">
						<div class="text-aviation-accent text-[10px] font-bold uppercase tracking-[0.2em] mb-2">
							Poschodie {shelf.Cislo}
						</div>
						<h3 class="text-2xl font-bold group-hover:text-aviation-accent transition-colors">
							{shelf.Nazov || shelf.Name || ""}
						</h3>
					</div>

					<div class="flex items-center gap-6 pr-6">
						{#if shelf.Logo}
							<div class="w-28 h-28 flex items-center justify-center p-2">
								<img
									src={pb.files.getURL(shelf, shelf.Logo)}
									alt={shelf.Nazov}
									class="max-w-full max-h-full object-contain group-hover:scale-110 transition-transform duration-700"
								/>
							</div>
						{/if}
						<div
							class="text-slate-200 group-hover:text-aviation-accent transition-all transform group-hover:translate-x-1"
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								width="24"
								height="24"
								viewBox="0 0 24 24"
								fill="none"
								stroke="currentColor"
								stroke-width="3"
								stroke-linecap="round"
								stroke-linejoin="round"><path d="m9 18 6-6-6-6" /></svg
							>
						</div>
					</div>
				</a>
			{/each}
		</div>
	{/await}
</div>
