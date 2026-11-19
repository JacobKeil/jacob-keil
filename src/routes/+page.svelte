<script lang="ts">
	import { tick } from 'svelte';

	let menuOpen = $state(false);
	let resumeOpen = $state(false);
	let resumeCloseButton: HTMLButtonElement | undefined = $state(undefined);
	let resumeModalElement: HTMLElement | undefined = $state(undefined);
	let resumeTrigger: HTMLButtonElement | undefined = $state(undefined);
	let resumePreviousBodyOverflow = '';
	type GalleryPhoto = { src: string; alt: string; caption: string };
	type PhotoGallery = { title: string; photos: GalleryPhoto[] };
	let activeGallery = $state<PhotoGallery | null>(null);
	let activePhotoIndex = $state(0);
	let closePhotoButton: HTMLButtonElement | undefined = $state(undefined);
	let photoCarouselElement: HTMLElement | undefined = $state(undefined);
	let returnFocusElement: HTMLElement | null = null;
	let previousBodyOverflow = '';

	const projects = [
		{
			number: '01',
			name: 'Clover Trivia',
			kind: 'Product · Full stack',
			description:
				'Hosts can create live trivia games, track teams and scores, and share recaps with players.',
			stack: ['SvelteKit', 'TypeScript', 'PostgreSQL', 'Docker'],
			links: [
				{ href: 'https://clovertrivia.com/', label: 'Visit the site' },
				{ href: 'https://github.com/JacobKeil?tab=repositories', label: 'View on GitHub' }
			],
			visual: 'clover'
		},
		{
			number: '02',
			name: 'Castercurse',
			kind: 'Product · Streaming',
			description:
				'Lets viewers watch several streams at once, adjust each stream’s volume, and arrange them on screen.',
			stack: ['SvelteKit', 'Twitch OAuth', 'Docker'],
			links: [{ href: 'https://github.com/JacobKeil/castercurse', label: 'View on GitHub' }],
			visual: 'caster'
		},
		{
			number: '03',
			name: 'Folding LED Whoop Gates',
			kind: 'FPV racing · Hardware',
			description:
				'Folding LED gates for FPV racing. Arrange them in stacks of two or three gates, with one WLED controller and power supply for the lights.',
			stack: ['3D printing', 'WLED', 'LEDs', 'Soldering', 'MR60', 'Power distribution'],
			links: [{ href: '/projects/folding-led-whoop-gates', label: 'See the gate build' }],
			visual: 'gates'
		}
	];

	const gatePhotos: GalleryPhoto[] = [
		{
			src: '/whoop_gates/PXL_20260301_030743515.MP.webp',
			alt: 'Lit whoop gates arranged in a parking garage',
			caption: 'Gate course'
		},
		{
			src: '/whoop_gates/PXL_20260202_054705143.webp',
			alt: 'MR60 connector holder mounted at the top of a PVC gate pole',
			caption: 'MR60 holder'
		},
		{
			src: '/whoop_gates/PXL_20260202_054717495.webp',
			alt: 'Fold-out printed connector gripping a PVC pole',
			caption: 'Folding connector'
		},
		{
			src: '/whoop_gates/PXL_20260211_232045248.MP.webp',
			alt: 'Folding gates lit during an indoor test',
			caption: 'Indoor lighting test'
		},
		{
			src: '/whoop_gates/PXL_20260307_071254745.MP.webp',
			alt: 'WLED controller with soldered three-way power splitters',
			caption: 'WLED power splitters'
		},
		{
			src: '/whoop_gates/20261117_231706.jpg',
			alt: '3D-printed case with the WLED controller and fuse box beside the power supply',
			caption: 'Controller and fuse box'
		},
		{
			src: '/whoop_gates/Screenshot_20261117_123210_Photos~2.webp',
			alt: 'LED strip, WLED controller, power supply, and connecting wires laid out together',
			caption: 'LED and WLED setup'
		}
	];
	const dronePhotos: GalleryPhoto[] = [
		{
			src: '/drones/PXL_20250920_030905528.webp',
			alt: "Close-up of a custom FPV quad's flight controller and wiring",
			caption: 'Flight controller and wiring'
		},
		{
			src: '/drones/PXL_20251013_172353250.webp',
			alt: 'Soldered flight controller and wiring on a carbon fiber FPV frame',
			caption: 'Soldered frame wiring'
		},
		{
			src: '/drones/PXL_20251217_032312976.webp',
			alt: 'Small ducted FPV whoop drone',
			caption: 'FPV whoop'
		},
		{
			src: '/drones/PXL_20260309_211952993.webp',
			alt: 'Custom FPV drone frame and components laid out on a workbench',
			caption: 'Drone build on the bench'
		},
		{
			src: '/drones/PXL_20260312_030416833.webp',
			alt: 'Close-up of a custom yellow FPV drone',
			caption: 'Custom yellow drone'
		},
		{
			src: '/drones/PXL_20260429_021225274.MP.webp',
			alt: 'Custom FPV drone held for inspection',
			caption: 'FPV drone build'
		},
		{
			src: '/drones/PXL_20260503_172615852.webp',
			alt: 'FPV drone and radio transmitter at an outdoor flying field',
			caption: 'Drone and radio at the field'
		},
		{
			src: '/drones/PXL_20260628_013756172.webp',
			alt: 'Several custom FPV drones arranged together',
			caption: 'Drone collection'
		}
	];

	async function openPhotoGallery(photos: GalleryPhoto[], title: string, index: number, trigger: HTMLButtonElement) {
		returnFocusElement = trigger;
		previousBodyOverflow = document.body.style.overflow;
		document.body.style.overflow = 'hidden';
		activeGallery = { title, photos };
		activePhotoIndex = index;
		await tick();
		closePhotoButton?.focus();
	}

	async function closePhotoGallery() {
		activeGallery = null;
		document.body.style.overflow = previousBodyOverflow;
		await tick();
		returnFocusElement?.focus();
		returnFocusElement = null;
	}

	async function openResume(trigger: HTMLButtonElement) {
		resumeTrigger = trigger;
		resumePreviousBodyOverflow = document.body.style.overflow;
		document.body.style.overflow = 'hidden';
		resumeOpen = true;
		await tick();
		resumeCloseButton?.focus();
	}

	async function closeResume() {
		resumeOpen = false;
		document.body.style.overflow = resumePreviousBodyOverflow;
		await tick();
		resumeTrigger?.focus();
		resumeTrigger = undefined;
	}

	function stepPhoto(direction: number) {
		if (!activeGallery) return;
		activePhotoIndex = (activePhotoIndex + direction + activeGallery.photos.length) % activeGallery.photos.length;
	}

	function handlePhotoGalleryKeydown(event: KeyboardEvent) {
		if (resumeOpen) {
			if (event.key === 'Escape') {
				event.preventDefault();
				void closeResume();
			} else if (event.key === 'Tab') {
				const controls = resumeModalElement?.querySelectorAll<HTMLElement>('button, a[href]');
				if (!controls?.length) return;
				const first = controls[0];
				const last = controls[controls.length - 1];
				const focusIsInside = resumeModalElement?.contains(document.activeElement);
				if (event.shiftKey && (document.activeElement === first || !focusIsInside)) {
					event.preventDefault();
					last.focus();
				} else if (!event.shiftKey && (document.activeElement === last || !focusIsInside)) {
					event.preventDefault();
					first.focus();
				}
			}
			return;
		}
		if (!activeGallery) return;
		if (event.key === 'Escape') {
			event.preventDefault();
			void closePhotoGallery();
		} else if (event.key === 'ArrowLeft') {
			event.preventDefault();
			stepPhoto(-1);
		} else if (event.key === 'ArrowRight') {
			event.preventDefault();
			stepPhoto(1);
		} else if (event.key === 'Tab') {
			const buttons = photoCarouselElement?.querySelectorAll<HTMLButtonElement>('button');
			if (!buttons?.length) return;
			const first = buttons[0];
			const last = buttons[buttons.length - 1];
			const focusIsInside = photoCarouselElement?.contains(document.activeElement);
			if (event.shiftKey && (document.activeElement === first || !focusIsInside)) {
				event.preventDefault();
				last.focus();
			} else if (!event.shiftKey && (document.activeElement === last || !focusIsInside)) {
				event.preventDefault();
				first.focus();
			}
		}
	}

</script>

<svelte:head>
	<title>Jacob Keil | Software &amp; Systems Engineer</title>
	<meta
		name="description"
		content="Jacob Keil builds web applications, works with IT systems, and makes FPV drones and LED whoop gates while learning embedded systems."
	/>
	<meta property="og:title" content="Jacob Keil | Software &amp; Systems Engineer" />
	<meta property="og:url" content="https://jacobkeil.com/" />
	<link rel="canonical" href="https://jacobkeil.com/" />
	<meta
		property="og:description"
		content="Web applications, IT systems, FPV drones, and folding LED whoop gates."
	/>
</svelte:head>

{#snippet sectionSeparator()}
	<div class="relative flex h-[180px] items-center max-[800px]:h-[130px]" aria-hidden="true">
		<span class="h-px w-full bg-gradient-to-r from-transparent via-line/80 to-transparent"></span>
		<span class="absolute left-1/2 h-[3px] w-8 -translate-x-1/2 rounded-full bg-accent shadow-[0_0_12px_rgb(237_117_90_/_18%)]"></span>
	</div>
{/snippet}

<div class="mx-auto w-[calc(100%-80px)] max-w-[1240px] max-[800px]:w-[calc(100%-40px)] max-[800px]:max-w-[600px]">
	<header class="relative z-10 flex h-[94px] items-center justify-between max-[800px]:h-[78px]">
		<a class="text-[1.55rem] font-extrabold tracking-[-0.045em] whitespace-nowrap" href="#top" aria-label="Jacob Keil, home">JK<span class="text-accent">.</span></a>
		<button
			class="group hidden size-11 cursor-pointer flex-col items-center justify-center gap-[5px] rounded-lg border border-line bg-[#292929] p-0 text-paper max-[800px]:flex"
			type="button"
			aria-label={menuOpen ? 'Close navigation menu' : 'Open navigation menu'}
			aria-expanded={menuOpen}
			aria-controls="main-navigation"
			onclick={() => (menuOpen = !menuOpen)}
		>
			<span class="h-0.5 w-[17px] rounded-sm bg-current transition duration-150 group-aria-expanded:translate-y-[7px] group-aria-expanded:rotate-45"></span>
			<span class="h-0.5 w-[17px] rounded-sm bg-current transition duration-150 group-aria-expanded:opacity-0"></span>
			<span class="h-0.5 w-[17px] rounded-sm bg-current transition duration-150 group-aria-expanded:-translate-y-[7px] group-aria-expanded:-rotate-45"></span>
		</button>
		<nav
			id="main-navigation"
			class={`flex items-center gap-9 text-[.79rem] font-semibold transition-[opacity,transform,visibility] duration-150 max-[800px]:absolute max-[800px]:top-[calc(100%-1px)] max-[800px]:right-0 max-[800px]:left-0 max-[800px]:z-[5] max-[800px]:flex-col max-[800px]:items-stretch max-[800px]:gap-0 max-[800px]:rounded-[10px] max-[800px]:border max-[800px]:border-line max-[800px]:bg-panel-soft max-[800px]:p-2 max-[800px]:shadow-[0_16px_35px_rgb(0_0_0_/_24%)] ${menuOpen ? 'max-[800px]:visible max-[800px]:translate-y-0 max-[800px]:opacity-100 max-[800px]:pointer-events-auto' : 'max-[800px]:invisible max-[800px]:-translate-y-1.5 max-[800px]:opacity-0 max-[800px]:pointer-events-none'}`}
			aria-label="Main navigation"
		>
			<a class="transition-colors duration-150 hover:text-accent max-[800px]:border-b max-[800px]:border-line max-[800px]:px-3 max-[800px]:py-[13px] max-[800px]:text-[.82rem]" href="#work" onclick={() => (menuOpen = false)}>Work</a>
			<a class="transition-colors duration-150 hover:text-accent max-[800px]:border-b max-[800px]:border-line max-[800px]:px-3 max-[800px]:py-[13px] max-[800px]:text-[.82rem]" href="#projects" onclick={() => (menuOpen = false)}>Projects</a>
			<a class="transition-colors duration-150 hover:text-accent max-[800px]:border-b max-[800px]:border-line max-[800px]:px-3 max-[800px]:py-[13px] max-[800px]:text-[.82rem]" href="#skills" onclick={() => (menuOpen = false)}>Skills</a>
			<a class="transition-colors duration-150 hover:text-accent max-[800px]:border-b max-[800px]:border-line max-[800px]:px-3 max-[800px]:py-[13px] max-[800px]:text-[.82rem]" href="#about" onclick={() => (menuOpen = false)}>About</a>
			<a class="transition-colors duration-150 hover:text-accent max-[800px]:border-b max-[800px]:border-line max-[800px]:px-3 max-[800px]:py-[13px] max-[800px]:text-[.82rem]" href="#learning" onclick={() => (menuOpen = false)}>Learning</a>
			<a class="transition-colors duration-150 hover:text-accent max-[800px]:border-b max-[800px]:border-line max-[800px]:px-3 max-[800px]:py-[13px] max-[800px]:text-[.82rem]" href="#contact" onclick={() => (menuOpen = false)}>Contact</a>
			<a class="py-3 pl-4 transition-colors duration-150 hover:text-accent max-[800px]:block max-[800px]:border-b-0 max-[800px]:px-3 max-[800px]:py-[13px] max-[800px]:text-[.82rem]" href="https://github.com/JacobKeil" target="_blank" rel="noreferrer">
				GitHub <span class="ml-1 text-accent" aria-hidden="true">↗</span>
			</a>
		</nav>
	</header>

	<main id="top">
		<div class="mb-4 flex min-h-[76px] items-center justify-between gap-6 rounded-xl border border-[#363636] bg-[linear-gradient(105deg,#1e1e1e,#191919)] px-[21px] py-[14px] max-[800px]:flex-col max-[800px]:items-start max-[800px]:gap-3 max-[800px]:px-[18px] max-[800px]:py-4" aria-label="Jacob Keil’s contact and profile links">
			<p class="m-0 flex-none text-[1.3rem] font-bold tracking-[-0.045em] text-paper">Jacob Keil<span class="text-accent">.</span></p>
			<div class="flex flex-wrap items-center justify-end gap-x-[25px] gap-y-3 max-[800px]:justify-start max-[800px]:gap-x-5">
				<a class="inline-flex items-center gap-[7px] text-[.74rem] font-semibold text-[#d0d0d0] transition-colors duration-150 hover:text-accent" href="mailto:jacobkeil13@gmail.com">
					<span class="text-[.61rem] font-bold tracking-[.08em] text-muted uppercase">Email</span> jacobkeil13@gmail.com
				</a>
				<a class="inline-flex items-center gap-[7px] text-[.74rem] font-semibold text-[#d0d0d0] transition-colors duration-150 hover:text-accent" href="https://www.linkedin.com/in/jacobkeil/" target="_blank" rel="noreferrer">
					LinkedIn <span class="text-accent" aria-hidden="true">↗</span>
				</a>
				<a class="inline-flex items-center gap-[7px] text-[.74rem] font-semibold text-[#d0d0d0] transition-colors duration-150 hover:text-accent" href="https://github.com/JacobKeil" target="_blank" rel="noreferrer">
					GitHub <span class="text-accent" aria-hidden="true">↗</span>
				</a>
			</div>
		</div>

		<section class="relative grid min-h-[590px] grid-cols-[1.08fr_.92fr] items-center overflow-hidden rounded-2xl border border-[#363636] bg-[radial-gradient(ellipse_at_76%_40%,rgb(237_117_90_/_9%),transparent_36%),linear-gradient(140deg,#222_0%,#181818_58%,#1e1917_100%)] px-[7.5%] pt-[66px] pb-[90px] text-paper max-[800px]:min-h-0 max-[800px]:grid-cols-1 max-[800px]:px-[8%] max-[800px]:pt-[62px] max-[800px]:pb-[76px] max-[520px]:px-[25px] max-[520px]:pt-[55px] max-[520px]:pb-[68px]" aria-labelledby="hero-title">
			<div class="relative z-[1]">
				<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-[#aaaaaa] uppercase"><span class="mr-2 inline-block size-[7px] rounded-full bg-accent align-[1px] shadow-[0_0_0_4px_rgb(237_117_90_/_12%)]"></span> Software · Systems · Hardware</p>
				<h1 id="hero-title" class="my-[27px] mb-[23px] text-[clamp(3.1rem,5.7vw,5.4rem)] leading-[1.04] font-medium tracking-[-.035em] max-[800px]:text-[clamp(3.2rem,10vw,5.1rem)] max-[520px]:text-[clamp(2.85rem,12vw,4rem)]">Software &amp;<br /><em class="font-sans text-accent not-italic font-medium">systems engineer.</em></h1>
				<p class="mb-[30px] max-w-[420px] text-[.97rem] leading-[1.8] text-[#c5c5c5] max-[520px]:text-[.89rem]">
					I build web applications and work with IT systems. Outside of work, I build FPV drones and LED whoop gates
					while learning embedded systems.
				</p>
				<div class="flex flex-wrap items-center gap-[14px] max-[520px]:gap-[10px]">
					<a class="group inline-flex min-h-[49px] items-center justify-center gap-[25px] rounded-[9px] bg-paper px-[18px] text-[.76rem] font-bold text-carbon-black transition duration-150 hover:-translate-y-0.5 hover:bg-accent max-[520px]:min-h-[46px] max-[520px]:gap-[15px] max-[520px]:px-[13px] max-[520px]:text-[.7rem]" href="#projects">View projects <span class="text-accent transition-colors duration-150 group-hover:text-carbon-black" aria-hidden="true">↓</span></a>
					<button class="inline-flex min-h-[49px] cursor-pointer items-center justify-center gap-[25px] rounded-[9px] border-0 bg-accent px-[18px] font-sans text-[.76rem] font-bold text-carbon-black transition duration-150 hover:-translate-y-0.5 hover:bg-paper max-[520px]:min-h-[46px] max-[520px]:gap-[15px] max-[520px]:px-[13px] max-[520px]:text-[.7rem]" type="button" onclick={(event) => openResume(event.currentTarget)}>
						View resume <span aria-hidden="true">↗</span>
					</button>
					<a class="inline-flex min-h-[49px] items-center justify-center gap-[12px] rounded-[9px] border border-line bg-[#303030] px-[18px] text-[.76rem] font-bold text-paper transition-colors duration-150 hover:border-accent hover:text-accent max-[520px]:min-h-[46px] max-[520px]:px-[13px] max-[520px]:text-[.7rem]" href="mailto:jacobkeil13@gmail.com">Email me <span class="text-accent" aria-hidden="true">↗</span></a>
				</div>
			</div>
			<div class="relative grid aspect-square w-full max-w-[440px] place-items-center justify-self-end max-[800px]:mx-auto max-[800px]:mt-[35px] max-[800px]:mb-1 max-[800px]:w-[min(76%,350px)] max-[800px]:justify-self-center max-[520px]:w-[86%]" aria-hidden="true">
				<div class="group relative isolate aspect-square w-full max-w-[440px]">
					<img
						class="absolute top-[9%] left-[5%] z-[1] block h-[76%] w-[65%] rotate-[-11deg] border-[5px] border-[#222] rounded-[13px] object-cover shadow-[0_24px_55px_rgb(0_0_0_/_38%)] transition-[top,left,right,width,height,transform] duration-[360ms] ease-[cubic-bezier(.22,1,.36,1)] motion-reduce:transition-none group-hover:top-[8%] group-hover:left-0 group-hover:h-[70%] group-hover:w-[58%] group-hover:rotate-[-15deg]"
						src="/whoop_gates/PXL_20260211_232045248.MP.webp"
						alt=""
						loading="eager"
					/>
					<img
						class="absolute top-[14%] right-[4%] z-[2] block h-[76%] w-[65%] rotate-[10deg] rounded-[13px] border-[5px] border-[#222] object-cover shadow-[0_24px_55px_rgb(0_0_0_/_38%)] transition-[top,left,right,width,height,transform] duration-[360ms] ease-[cubic-bezier(.22,1,.36,1)] motion-reduce:transition-none group-hover:top-[5%] group-hover:right-0 group-hover:h-[70%] group-hover:w-[58%] group-hover:rotate-[15deg]"
						src="/whoop_gates/PXL_20260301_030743515.MP.webp"
						alt=""
						loading="eager"
					/>
					<img
						class="absolute top-[12%] left-[18%] z-[3] block h-[76%] w-[65%] rotate-[-1deg] rounded-[13px] border-[5px] border-[#222] object-cover shadow-[0_24px_55px_rgb(0_0_0_/_38%)] transition-[top,left,right,width,height,transform] duration-[360ms] ease-[cubic-bezier(.22,1,.36,1)] motion-reduce:transition-none group-hover:top-[28%] group-hover:left-[21%] group-hover:h-[70%] group-hover:w-[58%]"
						src="/drones/PXL_20260628_013756172.webp"
						alt=""
						loading="eager"
					/>
					<span class="absolute right-[2%] bottom-[8%] z-[4] rounded-md border border-paper/25 bg-carbon-black/80 px-3 py-[9px] text-[.58rem] font-bold tracking-[.12em] text-paper uppercase">FPV drones &amp; gate builds</span>
				</div>
			</div>
			<div class="absolute right-[7.5%] bottom-[27px] left-[7.5%] flex justify-end text-[.6rem] tracking-[.12em] text-[#aaaaaa] uppercase max-[800px]:right-[8%] max-[800px]:bottom-[22px] max-[800px]:left-[8%] max-[520px]:right-[25px] max-[520px]:left-[25px] max-[520px]:text-[.54rem]"><span>Portfolio · 2026</span></div>
			<div class="absolute top-[45px] right-[43px] text-[1.25rem] text-accent max-[800px]:top-[26px] max-[800px]:right-[27px]" aria-hidden="true">✳</div>
		</section>
		{@render sectionSeparator()}

		<section class="py-[10px]" id="work" aria-labelledby="work-title">
			<div class="mb-10 flex items-end justify-between gap-[30px] max-[800px]:items-start">
				<div>
					<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">University of South Florida</p>
					<h2 id="work-title" class="mt-[11px] mb-0 text-[clamp(2.4rem,4vw,3.55rem)] leading-tight font-medium tracking-[-.035em] max-[520px]:text-[2.6rem]">Work experience<span class="text-accent">.</span></h2>
				</div>
				<div class="mb-[5px] text-right text-[#aaaaaa] max-[800px]:text-left">
					<p class="mb-[5px] text-[.79rem] font-semibold text-paper">Technology &amp; Systems Analyst</p>
					<span class="text-[.68rem]">Mar 2023 to Aug 2026</span>
				</div>
			</div>

			<article class="flex items-end justify-between gap-[30px] rounded-[14px] border border-line bg-panel px-7 py-[27px] max-[800px]:flex-col max-[800px]:items-start max-[800px]:gap-[22px]">
				<div class="max-w-[700px]">
					<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">November 2023</p>
					<h3 class="mt-[10px] mb-[9px] text-[1.7rem] font-medium tracking-[-.02em] text-paper">Personal Budget Calculator</h3>
					<p class="mb-[17px] max-w-[610px] text-[.84rem] leading-[1.7] text-[#c5c5c5]">
						Created a semester cost calculator that estimates expenses down to the dollar. Students can save
						results and return to them later.
					</p>
					<ul class="mb-0 flex list-none flex-wrap gap-[7px] p-0" aria-label="Technologies">
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">SvelteKit</li>
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">TypeScript</li>
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">Prisma</li>
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">PostgreSQL</li>
					</ul>
				</div>
				<a
					class="flex-none pb-1 text-[.77rem] font-bold text-paper transition-colors duration-150 hover:text-accent"
					href="https://tup-ofa.forest.usf.edu/personal_budget_calculator"
					target="_blank"
					rel="noreferrer"
				>
					View calculator <span class="ml-[5px] text-accent" aria-hidden="true">↗</span>
				</a>
			</article>

			<div class="mt-[30px]">
				<h3 class="mb-[13px] text-base font-semibold tracking-[-.01em]">Other internal projects</h3>
				<div class="grid grid-cols-2 gap-4 max-[800px]:grid-cols-1">
					<article class="rounded-[14px] border border-line bg-panel-soft px-[22px] py-[21px]">
						<div class="mb-[18px] flex flex-wrap items-center justify-between gap-x-[14px] gap-y-2">
							<p class="m-0 text-[.6rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">Nov 2024 to Aug 2026</p>
							<span class="text-[.62rem] font-semibold tracking-[.04em] text-accent uppercase">Internal · Retired</span>
						</div>
						<h4 class="mb-[9px] text-[1.18rem] font-medium tracking-[-.015em] text-paper">Lobby Queue System</h4>
						<p class="m-0 text-[.79rem] leading-[1.7] text-[#c5c5c5]">
							Built a live check-in queue so students could check in remotely and keep their place in line. Queue reports helped staff plan coverage.
						</p>
					</article>
					<article class="rounded-[14px] border border-line bg-panel-soft px-[22px] py-[21px]">
						<div class="mb-[18px] flex flex-wrap items-center justify-between gap-x-[14px] gap-y-2">
							<p class="m-0 text-[.6rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">May 2023 to Oct 2023</p>
							<span class="text-[.62rem] font-semibold tracking-[.04em] text-accent uppercase">Internal</span>
						</div>
						<h4 class="mb-[9px] text-[1.18rem] font-medium tracking-[-.015em] text-paper">Internal Operations Portal</h4>
						<p class="m-0 text-[.79rem] leading-[1.7] text-[#c5c5c5]">
							Built an Azure single sign-on portal for tickets, referrals, appointments, training, and document
							tracking. It replaced paper forms and several separate tools.
						</p>
					</article>
				</div>
			</div>

			<div class="mt-[30px] border-t border-line pt-[22px]">
				<h3 class="mb-[17px] text-base font-semibold">Earlier experience</h3>
				<article class="grid grid-cols-[minmax(190px,.42fr)_1fr] gap-7 rounded-[14px] border border-line bg-panel-soft px-[22px] py-[21px] max-[800px]:grid-cols-1 max-[800px]:gap-[14px]">
					<div>
						<p class="mb-2 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">CAE USA</p>
						<h4 class="mb-[9px] text-[1.18rem] font-medium tracking-[-.015em] text-paper">IT / Networking Co-op</h4>
						<span class="text-[.68rem] text-muted">Jan 2019 to Nov 2022</span>
					</div>
					<ul class="m-0 grid list-disc gap-[7px] pl-[17px] text-[.79rem] leading-[1.65] text-[#c5c5c5] marker:text-accent">
						<li>Imaged workstations, set them up for users, and helped train on-site staff.</li>
						<li>Configured and maintained switches, routers, load balancers, wireless infrastructure, and Active Directory.</li>
						<li>Monitored systems in SolarWinds and resolved incidents and recurring issues.</li>
					</ul>
				</article>
			</div>
		</section>
		{@render sectionSeparator()}

		<section class="py-[10px]" id="projects" aria-labelledby="projects-title">
			<div class="mb-10 flex items-end justify-between max-[520px]:mb-[27px] max-[520px]:flex-col max-[520px]:items-start max-[520px]:gap-[15px]">
				<div>
					<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">Selected projects</p>
					<h2 id="projects-title" class="mt-[11px] mb-0 text-[clamp(2.4rem,4vw,3.55rem)] leading-tight font-medium tracking-[-.035em] max-[520px]:text-[2.6rem]">Projects<span class="text-accent">.</span></h2>
				</div>
				<a class="pb-[9px] text-[.77rem] font-bold text-paper transition-colors duration-150 hover:text-accent max-[520px]:pb-0" href="https://github.com/JacobKeil" target="_blank" rel="noreferrer">
					More on GitHub <span class="ml-[5px] text-accent" aria-hidden="true">↗</span>
				</a>
			</div>

			<div class="grid grid-cols-2 gap-[23px] max-[800px]:grid-cols-1 max-[800px]:gap-[18px]">
				{#each projects as project (project.number)}
					<article class={`flex min-h-full flex-col overflow-hidden rounded-[14px] border border-line bg-panel p-[14px] transition-[translate,box-shadow,border-color] duration-[220ms] ease-[cubic-bezier(.4,0,.2,1)] hover:-translate-y-1 hover:border-accent/60 hover:shadow-[0_14px_32px_rgb(0_0_0_/_16%)] motion-reduce:transition-none max-[520px]:p-[11px] ${project.visual === 'gates' ? 'col-span-full max-[800px]:col-auto' : ''}`}>
						<div class={`relative grid place-items-center overflow-hidden rounded-[9px] p-[10px] ${project.visual === 'clover' ? 'h-[260px] max-[800px]:h-[250px] max-[520px]:h-[225px]' : project.visual === 'caster' ? 'h-[260px] max-[800px]:h-[250px] max-[520px]:h-[225px]' : 'order-1 h-auto min-h-0 w-full bg-[radial-gradient(ellipse_at_50%_56%,rgb(237_117_90_/_12%),transparent_55%),#1e1e1e]'}`} aria-hidden={project.visual !== 'gates'}>
							{#if project.visual === 'clover'}
							<img class="block size-full rounded-[8px] object-contain" src="/clover-trivia.png" alt="" loading="lazy" />
							{:else if project.visual === 'caster'}
							<img class="block size-full rounded-[8px] object-contain" src="/castercurse.png" alt="" loading="lazy" />
							{:else}
							<div class="grid w-full grid-cols-4 gap-[10px] max-[800px]:grid-cols-2" role="group" aria-label="Photos of the folding LED whoop gates">
									{#each gatePhotos as photo, index (photo.src)}
									<figure class="relative aspect-[1.4] min-w-0 overflow-hidden rounded-[7px] bg-panel-soft">
											<button
										class="block size-full cursor-zoom-in border-0 bg-transparent p-0"
												aria-label={`Open photo ${index + 1}: ${photo.caption}`}
												onclick={(event) => openPhotoGallery(gatePhotos, 'Whoop gate photos', index, event.currentTarget)}
											>
												<img class="block size-full object-cover" src={photo.src} alt={photo.alt} loading="lazy" />
											</button>
										<figcaption class="absolute right-0 bottom-0 left-0 bg-[linear-gradient(transparent,rgb(19_19_19_/_88%))] px-[9px] pt-5 pb-[7px] text-[.62rem] font-semibold text-paper">{photo.caption}</figcaption>
										</figure>
									{/each}
								</div>
							{/if}
						</div>
						<div class={`flex flex-1 flex-col p-[10px] ${project.visual === 'gates' ? 'order-[-1]' : ''}`}>
							<div class="flex items-center justify-between">
								<p class="mb-0 text-[.62rem] font-bold tracking-[.1em] text-muted uppercase">{project.kind}</p>
								<span class="font-serif text-[.85rem] text-accent">{project.number}</span>
							</div>
							<h3 class="mt-0 mb-[9px] text-[1.65rem] font-medium tracking-[-.02em] text-paper">{project.name}</h3>
							<p class={`mb-[17px] min-h-[49px] max-w-[510px] text-[.83rem] leading-[1.7] text-[#c5c5c5] ${project.visual === 'gates' ? 'max-w-none min-h-0 leading-[1.8]' : ''} max-[800px]:min-h-0`}>{project.description}</p>
							<ul class={`mb-5 flex list-none flex-wrap gap-[7px] p-0 ${project.visual === 'gates' ? 'mb-0' : ''}`} aria-label="Technologies">
								{#each project.stack as technology (technology)}
									<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">{technology}</li>
								{/each}
							</ul>
							{#if project.links.length}
								<div class="mt-auto flex flex-wrap items-center gap-x-5 gap-y-3 pt-1">
									{#each project.links as link (link.href)}
											<a class={`inline-flex min-h-11 items-center self-start text-[.77rem] font-bold transition-colors duration-150 ${link.label === 'See the gate build' ? 'rounded-[8px] bg-accent px-4 text-carbon-black hover:bg-paper' : 'hover:text-accent'}`} href={link.href} target={link.href.startsWith('/') ? undefined : '_blank'} rel={link.href.startsWith('/') ? undefined : 'noreferrer'}>
												{link.label}<span class={`ml-[5px] ${link.label === 'See the gate build' ? 'text-carbon-black' : 'text-accent'}`} aria-hidden="true">{link.href.startsWith('/') ? '→' : '↗'}</span>
										</a>
									{/each}
								</div>
							{/if}
						</div>
					</article>
				{/each}
			</div>
		</section>
		{@render sectionSeparator()}

		<section class="my-[10px] grid grid-cols-[.92fr_1.08fr] items-center gap-[8%] rounded-2xl bg-panel px-[8%] py-[74px] text-paper max-[800px]:grid-cols-1 max-[800px]:gap-10 max-[800px]:py-14 max-[520px]:px-[25px] max-[520px]:py-[45px]" id="about" aria-labelledby="about-title">
			<div>
				<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-[#aaaaaa] uppercase">Background</p>
				<h2 id="about-title" class="relative z-[1] mt-[18px] mb-0 text-[clamp(2.2rem,3.4vw,3.25rem)] leading-[1.12] font-medium tracking-[-.03em] max-[520px]:text-[2.4rem]">Software development<br /><em class="font-sans font-medium text-accent not-italic">and IT systems.</em></h2>
			</div>
			<div class="max-w-[500px] text-[.87rem] leading-[1.85] text-[#c5c5c5] max-[520px]:text-[.83rem]">
				<p class="mb-[18px]">
					I’ve built a trivia platform, a multi-stream viewer, and software for university students and staff. My work
					has also included IT systems and network support.
				</p>
				<p class="mb-[18px] last:mb-0">
					Outside of work, I build FPV drones and folding LED whoop gates. The gates use 3D-printed joints,
					soldered wiring, WLED, and one shared power supply.
				</p>
			</div>
		</section>
		{@render sectionSeparator()}

		<section class="py-[10px]" id="skills" aria-labelledby="skills-title">
			<div class="mb-10 flex items-end justify-between max-[520px]:mb-[27px] max-[520px]:flex-col max-[520px]:items-start max-[520px]:gap-[15px]">
				<div>
					<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">Skills and education</p>
					<h2 id="skills-title" class="mt-[11px] mb-0 text-[clamp(2.4rem,4vw,3.55rem)] leading-tight font-medium tracking-[-.035em] max-[520px]:text-[2.6rem]">Technical skills<span class="text-accent">.</span></h2>
				</div>
			</div>
			<div class="grid grid-cols-[minmax(0,1.6fr)_minmax(260px,.8fr)] items-stretch gap-4 max-[800px]:grid-cols-1">
				<div class="rounded-[14px] border border-line bg-panel p-6 max-[520px]:p-[19px]">
					<p class="mb-[18px] text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-accent uppercase">Tools and languages</p>
					<div class="grid">
						<div class="grid grid-cols-[165px_minmax(0,1fr)] items-start gap-[18px] border-t border-line py-[14px] max-[520px]:grid-cols-1 max-[520px]:gap-2">
							<h3 class="mt-[7px] mb-0 text-[.7rem] font-semibold tracking-[.06em] text-[#aaaaaa] uppercase">Languages</h3>
							<ul class="m-0 flex list-none flex-wrap gap-[7px] p-0">
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">TypeScript / JavaScript</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">C++ <span class="ml-1 text-[.52rem] font-semibold tracking-[.04em] text-accent uppercase">Currently learning</span></li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">HTML</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">CSS</li>
							</ul>
						</div>
						<div class="grid grid-cols-[165px_minmax(0,1fr)] items-start gap-[18px] border-t border-line py-[14px] max-[520px]:grid-cols-1 max-[520px]:gap-2">
							<h3 class="mt-[7px] mb-0 text-[.7rem] font-semibold tracking-[.06em] text-[#aaaaaa] uppercase">Frameworks</h3>
							<ul class="m-0 flex list-none flex-wrap gap-[7px] p-0">
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">SvelteKit</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">Prisma</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">Express</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">Tailwind</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">.NET <span class="ml-1 text-[.52rem] font-semibold tracking-[.04em] text-accent uppercase">Familiar</span></li>
							</ul>
						</div>
						<div class="grid grid-cols-[165px_minmax(0,1fr)] items-start gap-[18px] border-t border-line py-[14px] max-[520px]:grid-cols-1 max-[520px]:gap-2">
							<h3 class="mt-[7px] mb-0 text-[.7rem] font-semibold tracking-[.06em] text-[#aaaaaa] uppercase">Data and infrastructure</h3>
							<ul class="m-0 flex list-none flex-wrap gap-[7px] p-0">
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">PostgreSQL</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">MongoDB <span class="ml-1 text-[.52rem] font-semibold tracking-[.04em] text-accent uppercase">Familiar</span></li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">Docker</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">NGINX</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">Linux</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">Windows Server</li>
							</ul>
						</div>
						<div class="grid grid-cols-[165px_minmax(0,1fr)] items-start gap-[18px] border-t border-line py-[14px] max-[520px]:grid-cols-1 max-[520px]:gap-2">
							<h3 class="mt-[7px] mb-0 text-[.7rem] font-semibold tracking-[.06em] text-[#aaaaaa] uppercase">Tools and systems</h3>
							<ul class="m-0 flex list-none flex-wrap gap-[7px] p-0">
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">Git</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">GitHub Actions</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">Active Directory</li>
								<li class="rounded-md border border-line px-2 py-[6px] text-[.66rem] leading-[1.4] text-paper">Azure Identity / PIM</li>
							</ul>
						</div>
					</div>
				</div>
				<div class="flex flex-col rounded-[14px] border border-line bg-panel p-6 max-[520px]:p-[19px]">
					<p class="mb-[18px] text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-accent uppercase">Education</p>
					<article class="border-t border-line py-4">
						<p class="mb-2 text-[.68rem] text-[#aaaaaa]">University of South Florida</p>
						<h3 class="mb-[10px] text-base leading-[1.4] font-medium tracking-[-.01em] text-paper">BSAS, Computer Systems Technology</h3>
						<time class="text-[.64rem] font-semibold text-accent" datetime="2022-08">Aug 2022</time>
					</article>
					<article class="border-t border-line py-4">
						<p class="mb-2 text-[.68rem] text-[#aaaaaa]">Hillsborough Community College</p>
						<h3 class="mb-[10px] text-base leading-[1.4] font-medium tracking-[-.01em] text-paper">AS, Network Administration</h3>
						<time class="text-[.64rem] font-semibold text-accent" datetime="2020-05">May 2020</time>
					</article>
				</div>
			</div>
		</section>
		{@render sectionSeparator()}

		<section class="py-[10px]" id="learning" aria-labelledby="learning-title">
			<div class="mb-[25px]">
				<p class="m-0 flex items-center gap-2 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-muted uppercase">
					<span class="relative size-[7px] flex-none rounded-full bg-accent after:absolute after:-inset-1 after:rounded-full after:border after:border-accent/55 after:content-[''] after:animate-learning-pulse motion-reduce:after:animate-none" aria-hidden="true"></span>
					Learning <span class="border border-accent/30 px-[6px] py-[3px] text-[.52rem] tracking-[.12em] text-accent">In progress</span>
				</p>
				<h2 id="learning-title" class="mt-[9px] mb-0 text-[clamp(2rem,3vw,2.8rem)] font-medium tracking-[-.03em]">Embedded systems<span class="text-accent">.</span></h2>
			</div>
			<article class="mb-[18px] grid grid-cols-1 gap-[14px] rounded-[14px] border border-line bg-panel p-[22px]">
				<div>
					<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-accent uppercase">Building FPV drones · 1 year</p>
					<h3 class="mt-2 mb-0 text-[1.25rem] font-medium tracking-[-.015em]">FPV drone racing</h3>
				</div>
				<div>
					<p class="mb-[14px] text-[.82rem] leading-[1.7] text-[#c5c5c5]">
						Built and soldered custom drones, debugged UART connections, flashed firmware, configured radio
						equipment, and tracked down hardware and system issues.
					</p>
					<ul class="mb-0 flex list-none flex-wrap gap-[7px] p-0" aria-label="Drone racing experience">
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">Custom drone soldering</li>
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">UART debugging</li>
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">TX/RX configuration</li>
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">Firmware flashing</li>
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">System configuration</li>
						<li class="rounded-md border border-line px-2 py-[6px] text-[.61rem] text-[#c5c5c5]">Hardware troubleshooting</li>
					</ul>
					<div class="mt-[22px] grid grid-cols-4 gap-[10px] max-[520px]:grid-cols-2" role="group" aria-label="Photos of Jacob's FPV drones">
						{#each dronePhotos as photo, index (photo.src)}
							<figure class="aspect-[4/3] min-w-0 overflow-hidden rounded-[9px] border border-line bg-panel-soft">
								<button
									class="block size-full cursor-zoom-in border-0 bg-transparent p-0"
									aria-label={`Open drone photo ${index + 1}`}
									onclick={(event) => openPhotoGallery(dronePhotos, 'FPV drone photos', index, event.currentTarget)}
								>
									<img class="block size-full object-cover" src={photo.src} alt={photo.alt} loading="lazy" />
								</button>
							</figure>
						{/each}
					</div>
				</div>
			</article>
			<div class="relative overflow-hidden rounded-[14px] border border-line bg-[radial-gradient(ellipse_at_50%_0%,rgb(237_117_90_/_12%),transparent_48%),#1e1e1e] p-6 max-[520px]:p-[14px]" role="group" aria-label="Embedded systems study topics">
				<svg class="pointer-events-none absolute top-[116px] left-0 z-0 h-[60px] w-full max-[520px]:hidden" viewBox="0 0 1000 60" preserveAspectRatio="none" aria-hidden="true">
					<path class="fill-none stroke-accent/45 [stroke-width:1.5px] [vector-effect:non-scaling-stroke]" d="M500 0 V22 H166 V60" />
					<path class="fill-none stroke-accent/45 [stroke-width:1.5px] [vector-effect:non-scaling-stroke]" d="M500 0 V60" />
					<path class="fill-none stroke-accent/45 [stroke-width:1.5px] [vector-effect:non-scaling-stroke]" d="M500 0 V22 H834 V60" />
				</svg>
				<article class="relative z-10 mx-auto flex min-h-[92px] max-w-[390px] flex-col items-center justify-center rounded-xl border border-accent/70 bg-[#292929] px-5 py-4 text-center shadow-[0_12px_32px_rgb(0_0_0_/_24%)]">
					<span class="inline-flex items-center gap-[7px] text-[.57rem] font-bold tracking-[.13em] text-accent uppercase"><span class="size-[6px] rounded-full bg-accent" aria-hidden="true"></span>Study plan</span>
					<h3 class="mt-[7px] mb-0 text-base leading-[1.35] font-medium tracking-[-.01em]">Embedded systems engineering</h3>
				</article>
				<div class="relative z-10 mt-12 grid grid-cols-3 gap-[14px] max-[520px]:mt-[14px] max-[520px]:grid-cols-1 max-[520px]:gap-[10px]">
					<article class="min-h-[116px] rounded-xl border border-line bg-[#292929]/95 px-5 py-[18px] shadow-[0_8px_24px_rgb(0_0_0_/_15%)] transition-[border-color,transform] duration-[180ms] hover:-translate-y-0.5 hover:border-accent max-[520px]:min-h-0">
						<span class="mb-[13px] inline-flex size-7 items-center justify-center rounded-full bg-accent/10 text-[.59rem] font-bold text-accent">01</span>
						<p class="m-0 text-[.57rem] font-bold tracking-[.13em] text-muted uppercase">Language</p>
						<h3 class="mt-[5px] mb-0 text-[.96rem] leading-[1.35] font-medium">C/C++</h3>
					</article>
					<article class="min-h-[116px] rounded-xl border border-line bg-[#292929]/95 px-5 py-[18px] shadow-[0_8px_24px_rgb(0_0_0_/_15%)] transition-[border-color,transform] duration-[180ms] hover:-translate-y-0.5 hover:border-accent max-[520px]:min-h-0">
						<span class="mb-[13px] inline-flex size-7 items-center justify-center rounded-full bg-accent/10 text-[.59rem] font-bold text-accent">02</span>
						<p class="m-0 text-[.57rem] font-bold tracking-[.13em] text-muted uppercase">Hardware</p>
						<h3 class="mt-[5px] mb-0 text-[.96rem] leading-[1.35] font-medium">Electronics</h3>
					</article>
					<article class="min-h-[116px] rounded-xl border border-line bg-[#292929]/95 px-5 py-[18px] shadow-[0_8px_24px_rgb(0_0_0_/_15%)] transition-[border-color,transform] duration-[180ms] hover:-translate-y-0.5 hover:border-accent max-[520px]:min-h-0">
						<span class="mb-[13px] inline-flex size-7 items-center justify-center rounded-full bg-accent/10 text-[.59rem] font-bold text-accent">03</span>
						<p class="m-0 text-[.57rem] font-bold tracking-[.13em] text-muted uppercase">Systems</p>
						<h3 class="mt-[5px] mb-0 text-[.96rem] leading-[1.35] font-medium">RTOS</h3>
					</article>
				</div>
			</div>
		</section>

		{@render sectionSeparator()}
		<section class="py-[10px]" id="contact" aria-labelledby="contact-title">
			<div class="flex items-center justify-between gap-5 rounded-[14px] border border-line bg-[radial-gradient(ellipse_at_88%_50%,rgb(237_117_90_/_9%),transparent_38%),#242424] px-6 py-5 max-[520px]:flex-col max-[520px]:items-start max-[520px]:gap-3 max-[520px]:px-5 max-[520px]:py-4">
				<p class="m-0 text-[.68rem] leading-[1.5] font-bold tracking-[.15em] text-[#aaaaaa] uppercase" id="contact-title">Contact</p>
				<a class="inline-flex items-center gap-2 rounded-lg border border-line bg-carbon-black/40 px-4 py-3 text-[.82rem] font-semibold text-paper transition-colors duration-150 hover:border-accent hover:text-accent max-[520px]:text-[.72rem]" href="mailto:jacobkeil13@gmail.com">
					jacobkeil13@gmail.com <span class="text-accent" aria-hidden="true">↗</span>
				</a>
			</div>
		</section>
	</main>
</div>

<footer class="min-h-[92px] bg-[#101010] text-paper max-[800px]:min-h-[90px]">
	<div class="relative mx-auto flex min-h-[inherit] w-[calc(100%-80px)] max-w-[1240px] items-center justify-between max-[800px]:w-[calc(100%-40px)] max-[800px]:max-w-[600px] max-[800px]:gap-[18px] max-[520px]:grid max-[520px]:grid-cols-2 max-[520px]:grid-rows-[auto_auto] max-[520px]:gap-y-[14px] max-[520px]:py-[19px]">
		<a class="text-[1.4rem] font-extrabold tracking-[-.09em]" href="#top" aria-label="Back to top">JK<span class="text-accent">.</span></a>
		<p class="absolute left-1/2 m-0 -translate-x-1/2 text-[.82rem] text-[#aaaaaa] max-[520px]:static max-[520px]:col-span-2 max-[520px]:row-start-2 max-[520px]:justify-self-center max-[520px]:translate-x-0">Jacob Keil</p>
		<div class="flex items-center gap-9 text-[.79rem] font-semibold max-[520px]:col-start-2 max-[520px]:row-start-1 max-[520px]:justify-self-end max-[520px]:gap-[19px]">
			<a class="transition-colors duration-150 hover:text-accent" href="https://github.com/JacobKeil" target="_blank" rel="noreferrer">GitHub ↗</a>
			<a class="transition-colors duration-150 hover:text-accent" href="https://www.linkedin.com/in/jacobkeil/" target="_blank" rel="noreferrer">LinkedIn ↗</a>
		</div>
	</div>
</footer>

<svelte:window onkeydown={handlePhotoGalleryKeydown} />

{#if activeGallery}
	<div class="fixed inset-0 z-[100] grid place-items-center p-6 max-[520px]:p-3">
		<button
			class="absolute inset-0 size-full cursor-default border-0 bg-black/50 p-0 backdrop-blur-[3px]"
			type="button"
			tabindex="-1"
			aria-label="Close photo viewer"
			onclick={() => void closePhotoGallery()}
		></button>
		<section
			class="relative z-[1] grid max-h-[calc(100vh-48px)] w-[min(100%,1200px)] grid-cols-[52px_minmax(0,1fr)_52px] items-center gap-3 overflow-auto p-[58px_18px_18px] max-[520px]:max-h-[calc(100vh-24px)] max-[520px]:grid-cols-[38px_minmax(0,1fr)_38px] max-[520px]:gap-[6px] max-[520px]:p-[52px_8px_12px]"
			role="dialog"
			aria-modal="true"
			aria-label={activeGallery.title}
			bind:this={photoCarouselElement}
		>
			<button
				class="absolute top-3 right-3 grid size-10 cursor-pointer place-items-center border-0 bg-transparent p-0 text-paper transition-colors hover:text-accent"
				type="button"
				aria-label="Close photo viewer"
				bind:this={closePhotoButton}
				onclick={() => void closePhotoGallery()}
			>
				<span class="text-[2rem] leading-none font-light" aria-hidden="true">×</span>
			</button>
			<button
				class="grid size-12 cursor-pointer place-items-center border-0 bg-transparent p-0 text-paper transition-colors duration-150 hover:text-accent focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-accent max-[520px]:size-9"
				type="button"
				aria-label="Previous photo"
				onclick={() => stepPhoto(-1)}
			>
				<svg class="size-6" viewBox="0 0 24 24" fill="none" aria-hidden="true">
					<path d="m14 5-7 7 7 7" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" />
				</svg>
			</button>
			<figure class="m-0 grid min-w-0 justify-items-center gap-[11px]">
				<img
					class="block h-[min(70vh,760px)] w-full rounded-xl object-contain max-[520px]:h-[min(62vh,620px)]"
					src={activeGallery.photos[activePhotoIndex].src}
					alt={activeGallery.photos[activePhotoIndex].alt}
				/>
				<figcaption class="relative w-full px-12 text-center text-[.78rem] text-[#c5c5c5] max-[520px]:px-10 max-[520px]:text-[.68rem]">
					<span>{activeGallery.photos[activePhotoIndex].caption}</span>
					<span class="absolute top-1/2 right-0 -translate-y-1/2">{activePhotoIndex + 1} / {activeGallery.photos.length}</span>
				</figcaption>
			</figure>
			<button
				class="grid size-12 cursor-pointer place-items-center border-0 bg-transparent p-0 text-paper transition-colors duration-150 hover:text-accent focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-accent max-[520px]:size-9"
				type="button"
				aria-label="Next photo"
				onclick={() => stepPhoto(1)}
			>
				<svg class="size-6" viewBox="0 0 24 24" fill="none" aria-hidden="true">
					<path d="m10 5 7 7-7 7" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" />
				</svg>
			</button>
		</section>
	</div>
{/if}

{#if resumeOpen}
	<div class="fixed inset-0 z-[100] grid place-items-center p-6 max-[520px]:p-3">
		<button
			class="absolute inset-0 size-full cursor-default border-0 bg-black/50 p-0 backdrop-blur-[3px]"
			type="button"
			tabindex="-1"
			aria-label="Close resume viewer"
			onclick={() => void closeResume()}
		></button>
		<section
			class="relative z-[1] flex h-[min(92vh,1100px)] max-h-[calc(100vh-48px)] w-[min(100%,1080px)] flex-col overflow-hidden rounded-[14px] border border-line bg-[#171717] p-5 shadow-[0_24px_90px_rgb(0_0_0_/_45%)] max-[520px]:h-[94vh] max-[520px]:max-h-[calc(100vh-24px)] max-[520px]:w-full max-[520px]:px-[10px] max-[520px]:pt-[58px] max-[520px]:pb-[10px]"
			role="dialog"
			aria-modal="true"
			aria-labelledby="resume-modal-title"
			bind:this={resumeModalElement}
		>
			<button
				class="absolute top-3 right-3 grid size-10 cursor-pointer place-items-center border-0 bg-transparent p-0 text-paper transition-colors hover:text-accent"
				type="button"
				aria-label="Close resume viewer"
				bind:this={resumeCloseButton}
				onclick={() => void closeResume()}
			>
				<span class="text-[2rem] leading-none font-light" aria-hidden="true">×</span>
			</button>
			<header class="flex flex-none items-center justify-between gap-[18px] pr-[52px] pt-1 pb-4 pl-0 max-[520px]:flex-col max-[520px]:items-start max-[520px]:gap-3 max-[520px]:pr-[42px] max-[520px]:pt-0 max-[520px]:pb-3">
				<h2 id="resume-modal-title" class="m-0 text-[1.35rem] font-semibold">Resume</h2>
				<div class="flex items-center gap-[18px] max-[520px]:flex-wrap max-[520px]:gap-3">
					<a class="inline-flex min-h-10 items-center justify-center gap-3 rounded-[9px] bg-paper px-[13px] text-[.76rem] font-bold text-carbon-black transition duration-150 hover:-translate-y-0.5 hover:bg-accent max-[520px]:min-h-[38px] max-[520px]:text-[.68rem]" href="/Resume_Final.pdf" download="Jacob-Keil-Resume.pdf">
						Download PDF <span aria-hidden="true">↓</span>
					</a>
					<a class="text-[.78rem] font-semibold text-paper max-[520px]:text-[.7rem]" href="/Resume_Final.pdf" target="_blank" rel="noreferrer">
						Open PDF <span class="ml-1 text-accent" aria-hidden="true">↗</span>
					</a>
				</div>
			</header>
			<iframe class="min-h-0 w-full flex-1 rounded-md border-0 bg-white" src="/Resume_Final.pdf" title="Jacob Keil resume"></iframe>
		</section>
	</div>
{/if}
