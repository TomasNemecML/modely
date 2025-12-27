<script>
	import pb from "$lib/pb";
	import { page } from "$app/state";

	const modelId = page.params.id;

	async function getModel() {
		try {
			return await pb.collection("Modely").getOne(modelId, {
				expand: "Policka",
			});
		} catch (e) {
			console.error(e);
			return null;
		}
	}

	const modelPromise = getModel();
</script>

<div class="py-8">
	{#await modelPromise}
		<div class="flex justify-center py-20">
			<div class="animate-spin rounded-full h-10 w-10 border-b-2 border-aviation-accent"></div>
		</div>
	{:then model}
		{#if !model}
			<div class="text-center py-20">
				<div class="w-20 h-20 bg-slate-50 rounded-full flex items-center justify-center mx-auto mb-6">
					<svg
						xmlns="http://www.w3.org/2000/svg"
						width="32"
						height="32"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="text-slate-300"
						><path d="M14.5 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V7.5L14.5 2z" /><polyline
							points="14 2 14 8 20 8"
						/><line x1="9" y1="15" x2="15" y2="15" /></svg
					>
				</div>
				<h2 class="text-2xl font-bold mb-4 text-aviation-blue">Model sa nenašiel</h2>
				<a href="/" class="text-aviation-accent font-bold hover:underline">Vrátiť sa domov</a>
			</div>
		{:else}
			<div class="mb-8">
				<a
					href="/poschodie/{model.expand?.Policka?.Cislo || 1}/"
					class="group inline-flex items-center gap-2 text-xs uppercase tracking-widest font-bold text-slate-400 hover:text-aviation-accent transition-colors"
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
						class="transform group-hover:-translate-x-1 transition-transform"
						><path d="m15 18-6-6 6-6" /></svg
					>
					Späť na poschodie
				</a>
			</div>

			<div class="premium-card mb-12">
				<div class="aspect-video bg-slate-100 relative">
					{#if model.Obrazok}
						<img
							src={pb.files.getURL(model, model.Obrazok)}
							alt={model.Nazov}
							class="w-full h-full object-cover"
						/>
					{:else}
						<div class="w-full h-full flex items-center justify-center text-slate-300 italic">
							Fotka nie je k dispozícii
						</div>
					{/if}
					<div
						class="absolute bottom-0 left-0 w-full h-24 bg-gradient-to-t from-black/40 to-transparent"
					></div>
				</div>
				<div class="p-8">
					<div class="text-aviation-accent text-xs font-bold uppercase tracking-[0.2em] mb-3">
						{model.Aerolinka || "Neznáma aerolinka"}
					</div>
					<h2 class="text-4xl font-black uppercase tracking-tighter text-aviation-blue mb-8">
						{model.Nazov}
					</h2>

					<div class="grid grid-cols-2 sm:grid-cols-4 gap-4 mb-10">
						<div class="bg-slate-50 p-4 rounded-xl border border-slate-100">
							<div
								class="text-[10px] text-slate-400 uppercase tracking-widest mb-2 flex items-center gap-2"
							>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									width="12"
									height="12"
									viewBox="0 0 24 24"
									fill="none"
									stroke="currentColor"
									stroke-width="2.5"
									stroke-linecap="round"
									stroke-linejoin="round"
									class="text-aviation-accent"
									><path d="m11 17 2 2 4-4" /><path d="m11 17-2 2-4-4" /><path d="M13 3h8v8" /><path
										d="M3 11V3h8"
									/></svg
								>
								Mierka
							</div>
							<div class="font-bold text-aviation-blue">{model.Mierka || "N/A"}</div>
						</div>
						<div class="bg-slate-50 p-4 rounded-xl border border-slate-100">
							<div
								class="text-[10px] text-slate-400 uppercase tracking-widest mb-2 flex items-center gap-2"
							>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									width="12"
									height="12"
									viewBox="0 0 24 24"
									fill="none"
									stroke="currentColor"
									stroke-width="2.5"
									stroke-linecap="round"
									stroke-linejoin="round"
									class="text-aviation-accent"
									><rect width="18" height="18" x="3" y="4" rx="2" ry="2" /><line
										x1="16"
										x2="16"
										y1="2"
										y2="6"
									/><line x1="8" x2="8" y1="2" y2="6" /><line x1="3" x2="21" y1="10" y2="10" /></svg
								>
								Rok
							</div>
							<div class="font-bold text-aviation-blue">{model.Rok || "N/A"}</div>
						</div>
						<div class="bg-slate-50 p-4 rounded-xl border border-slate-100">
							<div
								class="text-[10px] text-slate-400 uppercase tracking-widest mb-2 flex items-center gap-2"
							>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									width="12"
									height="12"
									viewBox="0 0 24 24"
									fill="none"
									stroke="currentColor"
									stroke-width="2.5"
									stroke-linecap="round"
									stroke-linejoin="round"
									class="text-aviation-accent"
									><path d="M12 2H2v10h10V2z" /><path d="m12 12 9 9" /><path d="m21 12-9 9" /><path
										d="M22 22h-10V12h10v10z"
									/></svg
								>
								Značka
							</div>
							<div class="font-bold text-aviation-blue">{model.Znacka || "N/A"}</div>
						</div>
						<div class="bg-slate-50 p-4 rounded-xl border border-slate-100">
							<div
								class="text-[10px] text-slate-400 uppercase tracking-widest mb-2 flex items-center gap-2"
							>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									width="12"
									height="12"
									viewBox="0 0 24 24"
									fill="none"
									stroke="currentColor"
									stroke-width="2.5"
									stroke-linecap="round"
									stroke-linejoin="round"
									class="text-aviation-accent"
									><path d="M3 3v18h18" /><path d="M7 16h8" /><path d="M7 11h12" /><path
										d="M7 6h3"
									/></svg
								>
								Poschodie
							</div>
							<div class="font-bold text-aviation-blue">{model.expand?.Policka?.Cislo || "N/A"}</div>
						</div>
					</div>

					<div class="max-w-none">
						<div class="flex items-center gap-3 mb-4">
							<div class="h-px flex-grow bg-slate-100"></div>
							<h4 class="text-xs font-bold uppercase tracking-[0.2em] text-slate-400">Príbeh modelu</h4>
							<div class="h-px flex-grow bg-slate-100"></div>
						</div>
						<p class="text-slate-600 leading-relaxed text-lg">
							{model.Popis || "K tomuto modelu nie je k dispozícii žiadny popis."}
						</p>
					</div>
				</div>
			</div>
		{/if}
	{/await}
</div>
