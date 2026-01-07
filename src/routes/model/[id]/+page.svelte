<script>
	import pb from "$lib/pb";
	import { page } from "$app/state";

	const modelId = page.params.id;

	let currentImageIndex = $state(0);

	function handleScroll(e) {
		const target = e.target;
		if (target) {
			currentImageIndex = Math.round(target.scrollLeft / target.clientWidth);
		}
	}

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

	async function downloadQr() {
		try {
			const url = `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=${encodeURIComponent(page.url.href)}`;
			const response = await fetch(url);
			const blob = await response.blob();
			const blobUrl = URL.createObjectURL(blob);

			const link = document.createElement("a");
			link.href = blobUrl;
			link.download = `qrcode-${modelId}.png`;
			document.body.appendChild(link);
			link.click();
			document.body.removeChild(link);
			URL.revokeObjectURL(blobUrl);
		} catch (error) {
			console.error("Error downloading QR code:", error);
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
			{console.log(model)}
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
					{#if model.expand?.Policka}
						<span class="mx-1 text-slate-300">|</span>
						<span class="text-slate-500">{model.expand.Policka.Cislo}. {model.expand.Policka.Nazov}</span>
					{/if}
				</a>
			</div>

			<div class="premium-card mb-12">
				<div class="aspect-video bg-slate-100 relative">
					{#if model.Obrazok && model.Obrazok.length > 0}
						{@const images = Array.isArray(model.Obrazok) ? model.Obrazok : [model.Obrazok]}
						<div class="flex overflow-x-auto snap-x snap-mandatory w-full h-full" onscroll={handleScroll}>
							{#each images as obrazok}
								<img
									src={pb.files.getURL(model, obrazok)}
									alt={model.Nazov}
									class="w-full h-full object-cover shrink-0 snap-center"
								/>
							{/each}
						</div>

						{#if images.length > 1}
							<div class="absolute bottom-4 left-0 w-full flex justify-center gap-2 z-10">
								{#each images as _, i}
									<div
										class="w-2 h-2 rounded-full transition-all duration-300 shadow-sm {i ===
										currentImageIndex
											? 'bg-white scale-125 opacity-100'
											: 'bg-white/50 opacity-70'}"
									></div>
								{/each}
							</div>
						{/if}
					{:else}
						<div class="w-full h-full flex items-center justify-center text-slate-300 italic">
							Fotka nie je k dispozícii
						</div>
					{/if}
					<div
						class="absolute bottom-0 left-0 w-full h-28 bg-linear-to-t from-black/10 to-transparent pointer-events-none"
					></div>
				</div>
				<div class="p-6">
					<div class="text-aviation-accent text-xs font-bold uppercase tracking-[0.2em] mb-3">
						{model.Aerolinka || "Neznáma aerolinka"}
					</div>
					<h2 class="text-4xl font-black uppercase tracking-tighter text-aviation-blue mb-8">
						{model.Nazov}
					</h2>

					<div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-4 mb-10">
						<div class="bg-slate-50 p-4 rounded-xl border border-slate-100 col-span-2">
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
									><circle cx="12" cy="12" r="10" /><path d="M12 2a14.5 14.5 0 0 0 0 20" /><path
										d="M2 12h20"
									/></svg
								>
								Aerolinka
							</div>
							<div class={model.Aerolinka ? "font-bold text-aviation-blue" : "text-slate-400"}>
								{model.Aerolinka || "-"}
							</div>
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
								Výrobca
							</div>
							<div class={model.Vyrobca ? "font-bold text-aviation-blue" : "text-slate-400"}>
								{model.Vyrobca || "-"}
							</div>
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
									><path
										d="M17.8 19.2 16 11l3.5-3.5C21 6 21.5 4 21 3c-1-.5-3 0-4.5 1.5L13 8 4.8 6.2c-.5-.1-.9.1-1.1.5l-.3.5c-.2.5-.1 1 .3 1.3L9 12l-2 3H4l-1 1 3 2 2 3 1-1v-3l3-2 3.5 5.3c.3.4.8.5 1.3.3l.5-.2c.4-.3.6-.7.5-1.2z"
									/></svg
								>
								Model
							</div>
							<div class={model.Model ? "font-bold text-aviation-blue" : "text-slate-400"}>
								{model.Model || "-"}
							</div>
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
								Stavebnica
							</div>
							<div class={model.Stavebnica ? "font-bold text-aviation-blue" : "text-slate-400"}>
								{model.Stavebnica || "-"}
							</div>
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
									><path d="m11 17 2 2 4-4" /><path d="m11 17-2 2-4-4" /><path d="M13 3h8v8" /><path
										d="M3 11V3h8"
									/></svg
								>
								Mierka
							</div>
							<div class={model.Mierka ? "font-bold text-aviation-blue" : "text-slate-400"}>
								{model.Mierka || "-"}
							</div>
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
									><circle cx="12" cy="12" r="10" /><polyline points="12 6 12 12 16 14" /></svg
								>
								Postavené
							</div>
							<div class={model.CasPostavania ? "font-bold text-aviation-blue" : "text-slate-400"}>
								{model.CasPostavania || "-"}
							</div>
						</div>

						<!-- <div class="bg-slate-50 p-4 rounded-xl border border-slate-100">
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
									><path
										d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"
									/><polyline points="3.27 6.96 12 12.01 20.73 6.96" /><line
										x1="12"
										y1="22.08"
										x2="12"
										y2="12"
									/></svg
								>
								Materiál
							</div>
							<div class="font-bold text-aviation-blue">{model.Material || "N/A"}</div>
						</div> -->

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
									><rect x="2" y="5" width="20" height="14" rx="2" /><line
										x1="2"
										x2="22"
										y1="10"
										y2="10"
									/></svg
								>
								Registrácia
							</div>
							<div class={model.SPZ ? "font-bold text-aviation-blue" : "text-slate-400"}>
								{model.SPZ || "-"}
							</div>
						</div>
					</div>

					<div class="max-w-none">
						<div class="flex items-center gap-3 mb-4">
							<div class="h-px grow bg-slate-100"></div>
							<h4 class="text-xs font-bold uppercase tracking-[0.2em] text-slate-400">Popis Lietadla</h4>
							<div class="h-px grow bg-slate-100"></div>
						</div>
						<p class="text-slate-600 leading-relaxed text-lg">
							{model.Popis || "K tomuto modelu nie je k dispozícii žiadny popis."}
						</p>

						{#if model.wiki}
							<div class="mt-6 flex justify-center">
								<a
									href={model.wiki}
									target="_blank"
									rel="noopener noreferrer"
									class="inline-flex items-center gap-2 px-4 py-2 bg-slate-100 hover:bg-slate-200 text-aviation-blue rounded-full text-sm font-semibold transition-colors"
								>
									<svg
										xmlns="http://www.w3.org/2000/svg"
										width="16"
										height="16"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="2"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71" />
										<path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71" />
									</svg>
									Wikipedia
								</a>
							</div>
						{/if}

						<div class="mt-12 flex flex-col items-center border-t border-slate-100 pt-8">
							<h4 class="text-xs font-bold uppercase tracking-[0.2em] text-slate-400 mb-6">
								Zdieľať model
							</h4>

							<div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm mb-4">
								<img
									src={`https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=${encodeURIComponent(page.url.href)}`}
									alt="QR Code"
									class="w-32 h-32"
								/>
							</div>

							<button
								class="flex items-center gap-2 text-xs text-slate-500 hover:text-aviation-accent transition-colors cursor-pointer"
								onclick={downloadQr}
							>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									width="16"
									height="16"
									viewBox="0 0 24 24"
									fill="none"
									stroke="currentColor"
									stroke-width="2"
									stroke-linecap="round"
									stroke-linejoin="round"
								>
									<path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4" />
									<polyline points="7 10 12 15 17 10" />
									<line x1="12" y1="15" x2="12" y2="3" />
								</svg>
								Stiahnuť QR kód
							</button>
						</div>
					</div>
				</div>
			</div>
		{/if}
	{/await}
</div>
