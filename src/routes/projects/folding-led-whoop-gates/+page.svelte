<script lang="ts">
	const photos = {
		track: {
			src: '/whoop_gates/PXL_20260301_030743515.MP.webp',
			alt: 'Lit whoop gates arranged on a course in a parking garage',
			caption: 'Parking garage course'
		},
		foldingJoint: {
			src: '/whoop_gates/PXL_20260202_054717495.webp',
			alt: '3D-printed folding joint wrapped around a PVC pole',
			caption: 'Folding joint'
		},
		mr60: {
			src: '/whoop_gates/PXL_20260202_054705143.webp',
			alt: 'MR60 connector holder at the top of a PVC gate pole',
			caption: 'MR60 holder'
		},
		power: {
			src: '/whoop_gates/PXL_20260307_071254745.MP.webp',
			alt: 'WLED controller and power splitter wiring for several gates',
			caption: 'Shared power wiring'
		},
		controlBox: {
			src: '/whoop_gates/20261117_231706.jpg',
			alt: '3D-printed case with the WLED controller and fuse box beside the power supply',
			caption: 'Controller and fuse box'
		},
		lighting: {
			src: '/whoop_gates/Screenshot_20261117_123210_Photos~2.webp',
			alt: 'LED strip, WLED controller, power supply, and wires on a work surface',
			caption: 'Lighting components'
		},
		indoorTest: {
			src: '/whoop_gates/PXL_20260211_232045248.MP.webp',
			alt: 'LED-lit folding gates during an indoor test',
			caption: 'Indoor lighting test'
		}
	};

	const parts = [
		{
			name: 'BTF-Lighting WS2811 LED strip, 16.4 ft, 60 LEDs/m, 20 pixels/m',
			packPrice: '$20.99',
			perGate: '$8.91',
			href: 'https://www.amazon.com/dp/B01CNL6LBK?ref=ppx_yo2ov_dt_b_fed_asin_title&th=1'
		},
		{
			name: '10 ft 200 PSI thin-wall PVC pipe, 3/4 in',
			packPrice: '$2.89',
			perGate: '$0.54',
			href: 'https://www.lowes.com/pd/Charlotte-Pipe-3-4-in-dia-x-10-ft-L-200-PSI-SDR-21-PVC-Pipe/3133103'
		},
		{
			name: 'Amass MR60 plugs, 10 pairs',
			packPrice: '$15.99',
			perGate: '$1.60',
			href: 'https://www.amazon.com/dp/B0CNXD4WHY?ref=ppx_yo2ov_dt_b_fed_asin_title'
		},
		{
			name: 'Hillman #6-32 × 2 in machine screws, 75 count',
			packPrice: '$5.98',
			perGate: '$0.32',
			href: 'https://www.lowes.com/pd/Hillman-6-32-x-2-in-Phillips-Slotted-Combination-Drive-Machine-Screws-75-Count/3037601?store=&cm_mmc=shp-_-c-_-prd-_-tah-_-ggl-_-PMAX_TAH_000_Priority_Item_Omni-_-3037601-_-online-_-0-_-0&gclsrc=aw.ds&gad_source=1&gad_campaignid=23536714732&gclid=Cj0KCQiAy6vMBhDCARIsAK8rOgmg--cDjh_vbIP7AQDfyQHSFbJvQV4Agy-xuJq-FuIhVHlT3Fl4wtEaAuYJEALw_wcB'
		}
	];

	const controlBoxParts = [
		{
			name: '50 A, 600 W, 12 V DC regulated switching power supply',
			role: 'Converts wall power to 12 V DC for the LEDs.',
			href: 'https://www.amazon.com/dp/B09NJLNYKH'
		},
		{
			name: 'BTF-LIGHTING SP901E addressable LED signal amplifier / repeater',
			role: 'Repeats the control signal for the addressable LED strips.',
			href: 'https://www.amazon.com/dp/B07RDKRW5T'
		},
		{
			name: '12 V six-way ATO/ATC fuse box with LED indicators',
			role: 'Each branch has a 10 A automotive fuse. The supply is rated for 50 A total.',
			href: 'https://www.amazon.com/dp/B08XWQHMZJ'
		},
		{
			name: 'Eternaglo Plus ESP32 WLED controller, 6 outputs, Ethernet, relay terminal',
			role: 'Runs WLED and provides six outputs for the LED strips.',
			href: 'https://www.amazon.com/dp/B0DBFS6VCP'
		}
	];

	const stages = [
		{
			number: '01',
			label: 'Structure & connection',
			title: 'Frame and folding joints',
			text: 'All gates use the same parts. Arrange them in stacks of two or three gates. The frames use 3/4-inch, 200 PSI thin-wall PVC, which lets more LED light through than Schedule 40 pipe. Each 3D-printed joint folds for storage and grips the pole when open. A printed holder keeps the MR60 connector at the top of the pole.',
			photos: [photos.foldingJoint, photos.mr60]
		},
		{
			number: '02',
			label: 'Power & control',
			title: 'Power and WLED',
			text: 'I control the addressable LED strips in WLED. Soldered three-way splitters carry power from one supply to several gates, so they do not need separate batteries. Long power runs can lose voltage and make LEDs at the far end dim or change color.',
			photos: [photos.lighting, photos.controlBox, photos.power]
		},
		{
			number: '03',
			label: 'On the course',
			title: 'Test run',
			text: 'I tested the gates indoors and in a parking garage. The lights make the frames visible across the course, and the folding joints make them easier to pack after flying.',
			photos: [photos.indoorTest]
		}
	];
</script>

<svelte:head>
	<title>Folding LED Whoop Gates | Jacob Keil</title>
	<meta
		name="description"
		content="Folding LED whoop gates for FPV racing. Build stacks of two or three gates and control their addressable lighting with one WLED interface and shared power."
	/>
	<link rel="canonical" href="https://jacobkeil.com/projects/folding-led-whoop-gates" />
</svelte:head>

<div class="mx-auto w-[calc(100%-80px)] max-w-[1240px] max-[800px]:w-[calc(100%-40px)] max-[800px]:max-w-[600px]">
	<header class="relative z-10 flex h-[94px] items-center justify-between max-[800px]:h-[78px]">
		<a class="text-[1.55rem] font-extrabold tracking-[-0.045em] whitespace-nowrap" href="/" aria-label="Jacob Keil, home">JK<span class="text-accent">.</span></a>
		<a class="text-[.79rem] font-semibold transition-colors duration-150 hover:text-accent" href="/#projects">← Back to projects</a>
	</header>

	<main id="top">
		<nav class="mb-5 flex items-center gap-2 text-[.68rem] font-semibold text-muted" aria-label="Breadcrumb">
			<a class="transition-colors hover:text-accent" href="/#projects">Projects</a>
			<span aria-hidden="true">/</span>
			<span class="text-paper">Folding LED whoop gates</span>
		</nav>

		<section class="relative grid min-h-[510px] grid-cols-[1fr_1fr] items-center gap-10 overflow-hidden rounded-2xl border border-[#363636] bg-[radial-gradient(ellipse_at_76%_40%,rgb(237_117_90_/_9%),transparent_36%),linear-gradient(140deg,#222_0%,#181818_58%,#1e1917_100%)] p-10 text-paper max-[800px]:grid-cols-1 max-[800px]:gap-7 max-[800px]:p-8 max-[520px]:p-6" aria-labelledby="hero-title">
			<div class="relative z-[1] py-5 max-[800px]:py-0">
				<p class="mb-4 text-[.68rem] font-bold tracking-[.15em] text-muted uppercase"><span class="mr-2 inline-block size-[7px] rounded-full bg-accent align-[1px] shadow-[0_0_0_4px_rgb(237_117_90_/_12%)]"></span>FPV racing <span class="px-1 text-muted">/</span> Gate build</p>
				<h1 id="hero-title" class="mb-5 text-[clamp(2.8rem,5.3vw,4.8rem)] leading-[1.02] font-medium tracking-[-.04em]">Folding LED<br /><span class="text-accent">whoop gates.</span></h1>
				<p class="mb-7 max-w-[480px] text-[.94rem] leading-[1.8] text-[#c5c5c5]">I built these folding gates for FPV whoop racing using PVC poles, 3D-printed joints, and a shared WLED power system.</p>
				<div class="mb-8 flex flex-wrap gap-[7px]">
					{#each ['3D-printed joints', 'MR60 connectors', 'WLED lighting', 'Shared supply'] as item}
						<span class="rounded-md border border-line bg-carbon-black/40 px-2.5 py-[7px] text-[.62rem] font-medium text-[#d0d0d0]">{item}</span>
					{/each}
				</div>
				<a class="inline-flex items-center gap-3 text-[.77rem] font-bold text-paper transition-colors duration-150 hover:text-accent" href="#build">See the build details <span class="text-accent" aria-hidden="true">↓</span></a>
			</div>
			<figure class="relative m-0 overflow-hidden rounded-[13px] border-[5px] border-[#222] bg-panel-soft shadow-[0_24px_55px_rgb(0_0_0_/_38%)]">
				<img class="aspect-[1.18/1] w-full object-cover object-center max-[800px]:aspect-[1.6/1] max-[520px]:aspect-[1.25/1]" src={photos.track.src} alt={photos.track.alt} fetchpriority="high" />
				<figcaption class="absolute right-0 bottom-0 left-0 bg-[linear-gradient(transparent,rgb(19_19_19_/_88%))] px-4 pt-10 pb-3 text-[.65rem] font-semibold text-paper">{photos.track.caption}</figcaption>
				<span class="absolute top-3 left-3 rounded-md border border-paper/25 bg-carbon-black/80 px-2.5 py-[7px] text-[.56rem] font-bold tracking-[.12em] text-paper uppercase">Build 03</span>
			</figure>
			<div class="pointer-events-none absolute top-[30px] right-[34px] text-[1.1rem] text-accent" aria-hidden="true">✳</div>
		</section>

		<section id="build" class="pt-[70px] pb-[22px] max-[800px]:pt-[55px]" aria-labelledby="build-title">
			<div class="mb-7 flex items-end justify-between gap-6 max-[520px]:items-start max-[520px]:flex-col max-[520px]:gap-2">
				<div>
					<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">Design and wiring</p>
					<h2 id="build-title" class="mt-[11px] mb-0 text-[clamp(2.2rem,4vw,3.25rem)] leading-tight font-medium tracking-[-.035em]">How I built the gates<span class="text-accent">.</span></h2>
				</div>
				<span class="mb-1 text-[.68rem] text-[#aaaaaa]">Structure · Power · Lighting</span>
			</div>

			<div class="border-b border-line">
				{#each stages as stage, index (stage.number)}
					<article class="grid grid-cols-2 items-center gap-[7%] border-t border-line py-8 max-[800px]:grid-cols-1 max-[800px]:gap-6 max-[520px]:py-6">
						<div class={`max-w-[500px] ${index % 2 === 1 ? 'order-2 max-[800px]:order-1' : 'order-1'}`}>
							<p class="mb-3 text-[.64rem] font-bold tracking-[.14em] text-accent uppercase">{stage.number} <span class="text-muted">/ {stage.label}</span></p>
							<h3 class="mb-4 text-[clamp(1.6rem,3vw,2.3rem)] leading-[1.12] font-medium tracking-[-.03em]">{stage.title}</h3>
							<p class="m-0 text-[.84rem] leading-[1.8] text-[#c5c5c5]">{stage.text}</p>
						</div>
						<div class={`grid min-w-0 gap-3 ${stage.number === '02' ? 'grid-cols-2 grid-rows-2 max-[520px]:grid-cols-1 max-[520px]:grid-rows-none' : stage.photos.length > 1 ? 'grid-cols-2' : 'grid-cols-1'} ${index % 2 === 1 ? 'order-1 max-[800px]:order-2' : 'order-2'}`}>
							{#each stage.photos as photo (photo.src)}
								<figure class={`group relative m-0 min-w-0 overflow-hidden rounded-[11px] border border-line bg-panel-soft ${stage.number === '02' && photo.src === photos.lighting.src ? 'row-span-2 aspect-auto h-full max-[520px]:row-span-1 max-[520px]:aspect-[1.25/1]' : 'aspect-[1.12/1] max-[800px]:aspect-[1.4/1]'}`}>
									<img class={`block size-full transition-transform duration-300 group-hover:scale-[1.03] ${stage.number === '02' && photo.src === photos.lighting.src ? 'object-contain' : 'object-cover'}`} src={photo.src} alt={photo.alt} loading="lazy" />
									<figcaption class="absolute right-0 bottom-0 left-0 bg-[linear-gradient(transparent,rgb(19_19_19_/_88%))] px-3 pt-7 pb-[9px] text-[.6rem] font-semibold text-paper">{photo.caption}</figcaption>
								</figure>
							{/each}
						</div>
					</article>
				{/each}
			</div>
		</section>

		<section class="pt-[34px] pb-[54px]" aria-labelledby="parts-title">
			<div class="mb-6">
				<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">Build materials</p>
				<h2 id="parts-title" class="mt-[11px] mb-2 text-[clamp(2.2rem,4vw,3.25rem)] leading-tight font-medium tracking-[-.035em]">Per-gate cost<span class="text-accent">.</span></h2>
				<p class="m-0 max-w-[700px] text-[.82rem] leading-[1.8] text-[#c5c5c5]">Prices below show the portion of each purchase used for one gate. The frame materials cost <strong class="text-paper">$11.37 per gate</strong>. This excludes the control box, wiring, and printed parts.</p>
			</div>
			<div class="overflow-x-auto rounded-[12px] border border-line">
				<table class="w-full min-w-[600px] border-collapse text-left text-[.78rem]">
					<thead class="bg-panel-soft text-[.66rem] font-bold tracking-[.1em] text-muted uppercase">
						<tr>
							<th scope="col" class="px-5 py-3">Part</th>
							<th scope="col" class="px-5 py-3">Purchase price</th>
							<th scope="col" class="px-5 py-3">Cost per gate</th>
						</tr>
					</thead>
					<tbody>
						{#each parts as part (part.name)}
							<tr class="border-t border-line text-[#d0d0d0]">
								<th scope="row" class="px-5 py-4 font-medium text-paper">
									<a class="transition-colors hover:text-accent" href={part.href} target="_blank" rel="noreferrer">{part.name} <span class="text-accent" aria-hidden="true">↗</span></a>
								</th>
								<td class="px-5 py-4">{part.packPrice}</td>
								<td class="px-5 py-4">{part.perGate}</td>
							</tr>
						{/each}
					</tbody>
					<tfoot class="border-t border-line bg-panel-soft text-paper">
						<tr>
							<th scope="row" colspan="2" class="px-5 py-4 text-left font-semibold">Total per gate</th>
							<td class="px-5 py-4 font-semibold">$11.37</td>
						</tr>
					</tfoot>
				</table>
			</div>
		</section>

		<section class="pb-[54px]" aria-labelledby="control-box-title">
			<div class="mb-6">
				<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">System components</p>
				<h2 id="control-box-title" class="mt-[11px] mb-2 text-[clamp(2.2rem,4vw,3.25rem)] leading-tight font-medium tracking-[-.035em]">Control box<span class="text-accent">.</span></h2>
				<p class="m-0 max-w-[700px] text-[.82rem] leading-[1.8] text-[#c5c5c5]">The box holds the power supply, fuse box, signal amplifier, and WLED controller.</p>
			</div>
			<div class="overflow-x-auto rounded-[12px] border border-line">
				<table class="w-full min-w-[600px] border-collapse text-left text-[.78rem]">
					<thead class="bg-panel-soft text-[.66rem] font-bold tracking-[.1em] text-muted uppercase">
						<tr>
							<th scope="col" class="px-5 py-3">Component</th>
							<th scope="col" class="px-5 py-3">Role in the setup</th>
						</tr>
					</thead>
					<tbody>
						{#each controlBoxParts as part (part.name)}
							<tr class="border-t border-line text-[#d0d0d0]">
								<th scope="row" class="px-5 py-4 font-medium text-paper">
									<a class="transition-colors hover:text-accent" href={part.href} target="_blank" rel="noreferrer">{part.name} <span class="text-accent" aria-hidden="true">↗</span></a>
								</th>
								<td class="px-5 py-4">{part.role}</td>
							</tr>
						{/each}
					</tbody>
				</table>
			</div>
		</section>

		<section class="py-[56px] max-[800px]:py-[42px]" aria-labelledby="more-projects-title">
			<div class="flex items-center justify-between gap-5 rounded-[14px] border border-line bg-[radial-gradient(ellipse_at_88%_50%,rgb(237_117_90_/_9%),transparent_38%),#242424] px-6 py-5 max-[520px]:flex-col max-[520px]:items-start max-[520px]:gap-3 max-[520px]:px-5 max-[520px]:py-4">
				<div>
					<p id="more-projects-title" class="mb-1 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">More projects</p>
					<p class="m-0 text-[.82rem] text-[#c5c5c5]">See the other projects on my home page.</p>
				</div>
				<a class="inline-flex items-center gap-2 rounded-lg border border-line bg-carbon-black/40 px-4 py-3 text-[.78rem] font-semibold text-paper transition-colors duration-150 hover:border-accent hover:text-accent" href="/#projects">See all projects <span class="text-accent" aria-hidden="true">→</span></a>
			</div>
		</section>
	</main>
</div>

<footer class="min-h-[92px] bg-[#101010] text-paper max-[800px]:min-h-[90px]">
	<div class="relative mx-auto flex min-h-[inherit] w-[calc(100%-80px)] max-w-[1240px] items-center justify-between max-[800px]:w-[calc(100%-40px)] max-[800px]:max-w-[600px] max-[800px]:gap-[18px] max-[520px]:grid max-[520px]:grid-cols-2 max-[520px]:grid-rows-[auto_auto] max-[520px]:gap-y-[14px] max-[520px]:py-[19px]">
		<a class="text-[1.4rem] font-extrabold tracking-[-.09em]" href="/" aria-label="Back to home">JK<span class="text-accent">.</span></a>
		<p class="absolute left-1/2 m-0 -translate-x-1/2 text-[.82rem] text-[#aaaaaa] max-[520px]:static max-[520px]:col-span-2 max-[520px]:row-start-2 max-[520px]:justify-self-center max-[520px]:translate-x-0">Jacob Keil</p>
		<div class="flex items-center gap-9 text-[.79rem] font-semibold max-[520px]:col-start-2 max-[520px]:row-start-1 max-[520px]:justify-self-end max-[520px]:gap-[19px]">
			<a class="transition-colors duration-150 hover:text-accent" href="https://github.com/JacobKeil" target="_blank" rel="noreferrer">GitHub ↗</a>
			<a class="transition-colors duration-150 hover:text-accent" href="https://www.linkedin.com/in/jacobkeil/" target="_blank" rel="noreferrer">LinkedIn ↗</a>
		</div>
	</div>
</footer>
