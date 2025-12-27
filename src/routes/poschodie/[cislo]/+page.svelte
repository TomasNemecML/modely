<script>
	import pb from "$lib/pb";
	import { page } from "$app/state";

	const shelfId = page.params.cislo;

	async function getModels() {
		// Logic: Find shelf by number, then get models for that shelf
		// Assuming 'policky' collection has a 'cislo' field and 'modely' has a 'policka' relation
		try {
			const shelf = await pb.collection("Policky").getFirstListItem(`Cislo="${shelfId}"`);
			const models = await pb.collection("Modely").getList(1, 50, {
				filter: `Policka="${shelf.id}"`,
			});
			return { shelf, models: models.items };
		} catch (e) {
			console.error(e);
			return { shelf: { name: `Shelf ${shelfId}` }, models: [] };
		}
	}

	const dataPromise = getModels();
</script>

<div class="py-8">
	{#await dataPromise}
		<div class="flex justify-center py-20">
			<div class="animate-spin rounded-full h-10 w-10 border-b-2 border-aviation-accent"></div>
		</div>
	{:then { shelf, models }}
		<div class="mb-10 flex items-end justify-between border-b border-slate-100 pb-6">
			<div>
				<div class="text-aviation-accent text-[10px] font-bold uppercase tracking-[0.2em] mb-2">
					Prehliadka poschodia
				</div>
				<h2 class="text-4xl font-black uppercase tracking-tighter text-aviation-blue">
					{shelf.Nazov || `Poschodie ${shelfId}`}
				</h2>
				<p class="text-slate-400 text-sm mt-1 font-medium">{models.length} modelov v tejto sekcii</p>
			</div>
			<a
				href="/"
				class="group flex items-center gap-2 text-xs uppercase tracking-widest font-bold text-slate-400 hover:text-aviation-accent transition-colors"
			>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					width="16"
					height="16"
					viewBox="0 0 24 24"
					fill="none"
					stroke="currentColor"
					stroke-width="3"
					stroke-linecap="round"
					stroke-linejoin="round"
					class="transform group-hover:-translate-x-1 transition-transform"><path d="m15 18-6-6 6-6" /></svg
				>
				Späť
			</a>
		</div>

		{#if models.length === 0}
			<div class="premium-card p-20 text-center">
				<div class="w-16 h-16 bg-slate-50 rounded-full flex items-center justify-center mx-auto mb-4">
					<svg
						xmlns="http://www.w3.org/2000/svg"
						width="24"
						height="24"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="text-slate-300"
						><circle cx="12" cy="12" r="10" /><line x1="12" y1="8" x2="12" y2="12" /><line
							x1="12"
							y1="16"
							x2="12.01"
							y2="16"
						/></svg
					>
				</div>
				<p class="text-slate-400 font-medium">Na tomto poschodí sa zatiaľ nenachádzajú žiadne modely.</p>
			</div>
		{:else}
			<div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
				{#each models as model}
					<a href="/model/{model.id}/" class="premium-card group">
						<div class="aspect-[4/3] relative overflow-hidden bg-slate-100">
							{#if model.Obrazok}
								<img
									src={pb.files.getURL(model, model.Obrazok)}
									alt={model.Nazov}
									class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-700"
								/>
							{:else}
								<div
									class="w-full h-full flex items-center justify-center text-slate-300 italic text-sm"
								>
									Bez fotky
								</div>
							{/if}
							<div
								class="absolute inset-0 bg-gradient-to-t from-black/20 to-transparent opacity-0 group-hover:opacity-100 transition-opacity"
							></div>
						</div>
						<div class="p-6">
							<div class="text-aviation-accent text-[10px] font-bold uppercase tracking-widest mb-2">
								{model.Aerolinka || "Neznáma aerolinka"}
							</div>
							<h3
								class="text-xl font-bold text-aviation-blue group-hover:text-aviation-accent transition-colors"
							>
								{model.Nazov}
							</h3>
						</div>
					</a>
				{/each}
			</div>
		{/if}
	{/await}
</div>
