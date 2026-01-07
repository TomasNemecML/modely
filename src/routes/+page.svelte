<script>
	import pb from "$lib/pb";
	import { page } from "$app/state";

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

	async function downloadQr() {
		try {
			const url = `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=${encodeURIComponent(page.url.href)}`;
			const response = await fetch(url);
			const blob = await response.blob();
			const blobUrl = URL.createObjectURL(blob);

			const link = document.createElement("a");
			link.href = blobUrl;
			link.download = `qrcode-zbierka.png`;
			document.body.appendChild(link);
			link.click();
			document.body.removeChild(link);
			URL.revokeObjectURL(blobUrl);
		} catch (error) {
			console.error("Error downloading QR code:", error);
		}
	}

	const shelvesPromise = getShelves();
</script>

<div class="space-y-6 py-8">
	<div class="mb-10 text-center">
		<h2 class="text-4xl font-black uppercase tracking-tighter text-aviation-blue mb-2">Moja zbierka</h2>
		<div class="h-1 w-20 bg-aviation-accent mx-auto rounded-full"></div>
		<p class="text-slate-500 text-sm mt-4 font-medium">Prehliadajte si kompletnú zbierku modelov</p>
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
							#{shelf.Cislo}
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

	<div class="mt-12 flex flex-col items-center border-t border-slate-100 pt-8">
		<h4 class="text-xs font-bold uppercase tracking-[0.2em] text-slate-400 mb-6">Zdieľať zbierku</h4>

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
