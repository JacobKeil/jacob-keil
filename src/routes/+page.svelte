<script lang="ts">
	import { tick } from 'svelte';

	let menuOpen = $state(false);
	let resumeOpen = $state(false);
	let resumeCloseButton: HTMLButtonElement | undefined;
	let resumeModalElement: HTMLElement | undefined;
	let resumeTrigger: HTMLButtonElement | undefined;
	let resumePreviousBodyOverflow = '';
	type GalleryPhoto = { src: string; alt: string; caption: string };
	type PhotoGallery = { title: string; photos: GalleryPhoto[] };
	let activeGallery = $state<PhotoGallery | null>(null);
	let activePhotoIndex = $state(0);
	let closePhotoButton: HTMLButtonElement | undefined;
	let photoCarouselElement: HTMLElement | undefined;
	let returnFocusElement: HTMLElement | null = null;
	let previousBodyOverflow = '';

	const projects = [
		{
			number: '01',
			name: 'Clover Trivia',
			kind: 'Product · Full stack',
			description:
				'A live trivia platform for hosts to build games, manage teams and scores, and share recaps with players.',
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
				'A watch-party experience for following multiple streams, with per-stream volume and flexible layouts.',
			stack: ['SvelteKit', 'Twitch OAuth', 'Docker'],
			links: [{ href: 'https://github.com/JacobKeil/castercurse', label: 'View on GitHub' }],
			visual: 'caster'
		},
		{
			number: '03',
			name: 'Folding LED Whoop Gates',
			kind: 'FPV racing · Hardware',
			description:
				'Designed illuminated folding gates for FPV whoop racing. A custom MR60 holder mounts at the top of the PVC, and a slightly undersized 3D-printed folding connector friction-fits to grip when deployed. Internal LEDs run from a WLED controller; soldered three-way splitters power multiple plug-in gates from one supply, so each gate needs no separate battery.',
			stack: ['3D printing', 'WLED', 'LEDs', 'Soldering', 'MR60', 'Power distribution'],
			links: [],
			visual: 'gates'
		}
	];

	const gatePhotos: GalleryPhoto[] = [
		{
			src: '/whoop_gates/PXL_20260301_030743515.MP.webp',
			alt: 'Several illuminated whoop gates set up in a parking garage',
			caption: 'Multi-gate setup'
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
			alt: 'LED-lit folding gates during an indoor test',
			caption: 'LED test'
		},
		{
			src: '/whoop_gates/PXL_20260307_071254745.MP.webp',
			alt: 'WLED controller with soldered three-way power splitters',
			caption: 'WLED power splitters'
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
			const buttons = photoCarouselElement?.querySelectorAll<HTMLButtonElement>('.photo-carousel button');
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
	<title>Jacob Keil | Software Engineer</title>
	<meta
		name="description"
		content="Jacob Keil is a full-stack web developer working toward embedded systems through hands-on FPV drone and hardware projects."
	/>
	<meta property="og:title" content="Jacob Keil | Software Engineer" />
	<meta property="og:url" content="https://jacobkeil.com/" />
	<link rel="canonical" href="https://jacobkeil.com/" />
	<meta
		property="og:description"
		content="Full-stack web development, IT systems, and hands-on embedded projects including FPV drones and LED whoop gates."
	/>
</svelte:head>

<div class="site-shell">
	<header class="site-header">
			<a class="wordmark" href="#top" aria-label="Jacob Keil, home">JK<span>.</span></a>
		<button
			class="menu-toggle"
			type="button"
			aria-label={menuOpen ? 'Close navigation menu' : 'Open navigation menu'}
			aria-expanded={menuOpen}
			aria-controls="main-navigation"
			onclick={() => (menuOpen = !menuOpen)}
		>
			<span></span><span></span><span></span>
		</button>
		<nav id="main-navigation" class:open={menuOpen} aria-label="Main navigation">
			<a href="#work" onclick={() => (menuOpen = false)}>Work</a>
			<a href="#projects" onclick={() => (menuOpen = false)}>Projects</a>
			<a href="#skills" onclick={() => (menuOpen = false)}>Skills</a>
			<a href="#about" onclick={() => (menuOpen = false)}>About</a>
			<a href="#learning" onclick={() => (menuOpen = false)}>Learning</a>
			<a href="#contact" onclick={() => (menuOpen = false)}>Contact</a>
			<a class="header-link" href="https://github.com/JacobKeil" target="_blank" rel="noreferrer">
				GitHub <span aria-hidden="true">↗</span>
			</a>
		</nav>
	</header>

	<main id="top">
		<div class="profile-strip" aria-label="Jacob Keil’s contact and profile links">
			<p class="profile-name">Jacob Keil<span class="accent">.</span></p>
			<div class="profile-links">
				<a class="profile-email" href="mailto:jacobkeil13@gmail.com">
					<span>Email</span> jacobkeil13@gmail.com
				</a>
				<a href="https://www.linkedin.com/in/jacobkeil/" target="_blank" rel="noreferrer">
					LinkedIn <span aria-hidden="true">↗</span>
				</a>
				<a href="https://github.com/JacobKeil" target="_blank" rel="noreferrer">
					GitHub <span aria-hidden="true">↗</span>
				</a>
			</div>
		</div>

		<section class="hero" aria-labelledby="hero-title">
			<div class="hero-copy">
				<p class="eyebrow"><span class="status-dot"></span> Software engineer</p>
				<h1 id="hero-title">Full-stack<br /><em>software engineer.</em></h1>
				<p class="hero-summary">
					I build full-stack web applications with TypeScript, SvelteKit, PostgreSQL, and Docker. I’m now
					working toward embedded systems through hands-on projects like custom FPV drones and LED-lit whoop gates.
				</p>
				<div class="hero-actions">
					<a class="button button-light" href="#projects">View projects <span aria-hidden="true">↓</span></a>
					<a class="text-link" href="mailto:jacobkeil13@gmail.com">Email me <span aria-hidden="true">↗</span></a>
					<button class="text-link resume-trigger" type="button" onclick={(event) => openResume(event.currentTarget)}>
						View résumé <span aria-hidden="true">↗</span>
					</button>
				</div>
			</div>
			<div class="hero-art" aria-hidden="true">
				<div class="hero-photo-stack">
					<img
						class="hero-stack-image hero-stack-back"
						src="/whoop_gates/PXL_20260211_232045248.MP.webp"
						alt=""
						loading="eager"
					/>
					<img
						class="hero-stack-image hero-stack-middle"
						src="/whoop_gates/PXL_20260301_030743515.MP.webp"
						alt=""
						loading="eager"
					/>
					<img
						class="hero-stack-image hero-stack-front"
						src="/drones/PXL_20260628_013756172.webp"
						alt=""
						loading="eager"
					/>
					<span class="hero-stack-label">FPV · builds &amp; projects</span>
				</div>
			</div>
			<div class="hero-index"><span>01 / 04</span><span>Portfolio · 2026</span></div>
			<div class="hero-spark" aria-hidden="true">✳</div>
		</section>

		<section class="work-section section-block" id="work" aria-labelledby="work-title">
			<div class="section-heading work-section-heading">
				<div>
					<p class="eyebrow">University of South Florida</p>
					<h2 id="work-title">Work projects<span class="accent">.</span></h2>
				</div>
				<div class="work-meta">
					<p>Technology &amp; Systems Analyst</p>
					<span>Mar 2023 to Aug 2026</span>
				</div>
			</div>

			<article class="public-work-card">
				<div class="public-work-copy">
					<p class="eyebrow">November 2023</p>
					<h3>Personal Budget Calculator</h3>
					<p class="public-work-description">
						Developed a semester cost calculator that gives students dollar-level estimates and lets them
						save and revisit their calculations.
					</p>
					<ul class="tag-list" aria-label="Technologies">
						<li>SvelteKit</li>
						<li>TypeScript</li>
						<li>Prisma</li>
						<li>PostgreSQL</li>
					</ul>
				</div>
				<a
					class="public-work-link"
					href="https://tup-ofa.forest.usf.edu/personal_budget_calculator"
					target="_blank"
					rel="noreferrer"
				>
					View calculator <span aria-hidden="true">↗</span>
				</a>
			</article>

			<div class="internal-work">
				<h3>Other internal projects</h3>
				<div class="internal-work-grid">
					<article class="internal-work-card">
						<div class="internal-work-meta">
							<p class="eyebrow">Nov 2024 to Aug 2026</p>
							<span>Internal · Retired</span>
						</div>
						<h4>Lobby Queue System</h4>
						<p>
							Built a real-time remote check-in queue so students could keep their place in line without
							waiting in person. Added analytics to track queue volume and help plan staffing.
						</p>
					</article>
					<article class="internal-work-card">
						<div class="internal-work-meta">
							<p class="eyebrow">May 2023 to Oct 2023</p>
							<span>Internal</span>
						</div>
						<h4>Internal Operations Portal</h4>
						<p>
							Built an Azure SSO-enabled portal for tickets, referrals, appointments, training, and document
							tracking. It replaced paper tasks and reduced the number of separate tools used by the office.
						</p>
					</article>
				</div>
			</div>

			<div class="previous-experience">
				<h3>Earlier experience</h3>
				<article class="experience-entry">
					<div class="experience-role">
						<p class="eyebrow">CAE USA</p>
						<h4>IT / Networking Co-op</h4>
						<span>Jan 2019 to Nov 2022</span>
					</div>
					<ul>
						<li>Imaged and onboarded workstations and helped train on-site staff.</li>
						<li>Configured and maintained switches, routers, load balancers, wireless infrastructure, and Active Directory.</li>
						<li>Monitored systems with SolarWinds, responded to incidents, and resolved recurring issues.</li>
					</ul>
				</article>
			</div>
		</section>

		<section class="projects-section section-block" id="projects" aria-labelledby="projects-title">
			<div class="section-heading">
				<div>
					<p class="eyebrow">Selected projects</p>
					<h2 id="projects-title">Projects<span class="accent">.</span></h2>
				</div>
				<a class="text-link" href="https://github.com/JacobKeil" target="_blank" rel="noreferrer">
					More on GitHub <span aria-hidden="true">↗</span>
				</a>
			</div>

			<div class="project-grid">
				{#each projects as project (project.number)}
					<article class="project-card" class:project-card-featured={project.visual === 'gates'}>
						<div class="project-preview {project.visual}" aria-hidden={project.visual !== 'gates'}>
							{#if project.visual === 'clover'}
								<img class="project-preview-image" src="/clover-trivia.png" alt="" loading="lazy" />
							{:else if project.visual === 'caster'}
								<img class="project-preview-image" src="/castercurse.png" alt="" loading="lazy" />
							{:else}
								<div class="gate-photo-gallery" role="group" aria-label="Photos of the folding LED whoop gates">
									{#each gatePhotos as photo, index (photo.src)}
										<figure class="gate-photo">
											<button
												class="photo-trigger"
												aria-label={`Open photo ${index + 1}: ${photo.caption}`}
												onclick={(event) => openPhotoGallery(gatePhotos, 'Whoop gate photos', index, event.currentTarget)}
											>
												<img src={photo.src} alt={photo.alt} loading="lazy" />
											</button>
											<figcaption>{photo.caption}</figcaption>
										</figure>
									{/each}
								</div>
							{/if}
						</div>
						<div class="project-info">
							<div class="project-heading-line">
								<p class="project-kind">{project.kind}</p>
								<span class="project-number">{project.number}</span>
							</div>
							<h3>{project.name}</h3>
							<p class="project-description">{project.description}</p>
							<ul class="tag-list" aria-label="Technologies">
								{#each project.stack as technology (technology)}
									<li>{technology}</li>
								{/each}
							</ul>
							{#if project.links.length}
								<div class="project-links">
									{#each project.links as link (link.href)}
										<a class="project-link" href={link.href} target="_blank" rel="noreferrer">
											{link.label}<span aria-hidden="true">↗</span>
										</a>
									{/each}
								</div>
							{/if}
						</div>
					</article>
				{/each}
			</div>
		</section>

		<section class="about section-block" id="about" aria-labelledby="about-title">
			<div class="about-label">
				<p class="eyebrow">Background</p>
				<h2 id="about-title">Software development<br /><em>and IT systems.</em></h2>
			</div>
			<div class="about-copy">
				<p>
					My professional background is in full-stack web development and IT systems. I’ve built a live trivia
					platform, a multi-stream viewing tool, and internal systems for university operations.
				</p>
				<p>
					I’m working toward embedded systems through practical FPV projects, including custom drone builds and
					LED-lit folding whoop gates that combine 3D-printed parts, soldered wiring, WLED control, and shared power distribution.
				</p>
			</div>
		</section>

		<section class="skills-education section-block" id="skills" aria-labelledby="skills-title">
			<div class="section-heading">
				<div>
					<p class="eyebrow">Skills and education</p>
					<h2 id="skills-title">Technical skills<span class="accent">.</span></h2>
				</div>
			</div>
			<div class="skills-education-grid">
				<div class="profile-panel skills-panel">
					<p class="eyebrow">Technical skills</p>
					<div class="skill-groups">
						<div class="skill-group">
							<h3>Languages</h3>
							<ul class="skill-tags">
								<li>TypeScript / JavaScript</li>
								<li>C++ <span>Currently learning</span></li>
								<li>HTML</li>
								<li>CSS</li>
							</ul>
						</div>
						<div class="skill-group">
							<h3>Frameworks</h3>
							<ul class="skill-tags">
								<li>SvelteKit</li>
								<li>Prisma</li>
								<li>Express</li>
								<li>Tailwind</li>
								<li>.NET <span>Familiar</span></li>
							</ul>
						</div>
						<div class="skill-group">
							<h3>Data and infrastructure</h3>
							<ul class="skill-tags">
								<li>PostgreSQL</li>
								<li>MongoDB <span>Familiar</span></li>
								<li>Docker</li>
								<li>NGINX</li>
								<li>Linux</li>
								<li>Windows Server</li>
							</ul>
						</div>
						<div class="skill-group">
							<h3>Tools and systems</h3>
							<ul class="skill-tags">
								<li>Git</li>
								<li>GitHub Actions</li>
								<li>Active Directory</li>
								<li>Azure Identity / PIM</li>
							</ul>
						</div>
					</div>
				</div>
				<div class="profile-panel education-panel">
					<p class="eyebrow">Education</p>
					<article class="education-entry">
						<p>University of South Florida</p>
						<h3>BSAS, Computer Systems Technology</h3>
						<time datetime="2022-08">Aug 2022</time>
					</article>
					<article class="education-entry">
						<p>Hillsborough Community College</p>
						<h3>AS, Network Administration</h3>
						<time datetime="2020-05">May 2020</time>
					</article>
				</div>
			</div>
		</section>

		<section class="learning section-block" id="learning" aria-labelledby="learning-title">
			<div class="learning-heading">
				<p class="eyebrow learning-status">
					<span class="learning-live-dot" aria-hidden="true"></span>
					Currently learning <span class="learning-live-label">Live</span>
				</p>
				<h2 id="learning-title">Embedded systems<span class="accent">.</span></h2>
			</div>
			<article class="drone-experience">
				<div>
					<p class="eyebrow">Hands-on experience · 1 year</p>
					<h3>FPV drone racing</h3>
				</div>
				<div>
					<p>
						Built and soldered custom drones, debugged UART connections and other system issues, flashed
						firmware, configured TX/RX equipment, and troubleshot connected systems.
					</p>
					<ul class="tag-list" aria-label="Drone racing experience">
						<li>Custom drone soldering</li>
						<li>UART debugging</li>
						<li>TX/RX configuration</li>
						<li>Firmware flashing</li>
						<li>System configuration</li>
						<li>Hardware troubleshooting</li>
					</ul>
					<div class="drone-photo-gallery" role="group" aria-label="Photos of Jacob's FPV drones">
						{#each dronePhotos as photo, index (photo.src)}
							<figure>
								<button
									class="photo-trigger"
									aria-label={`Open drone photo ${index + 1}`}
									onclick={(event) => openPhotoGallery(dronePhotos, 'FPV drone photos', index, event.currentTarget)}
								>
									<img src={photo.src} alt={photo.alt} loading="lazy" />
								</button>
							</figure>
						{/each}
					</div>
				</div>
			</article>
			<div class="learning-chart" role="group" aria-label="Current learning topics and how they connect">
				<svg class="learning-chart-lines" viewBox="0 0 1000 600" preserveAspectRatio="none" aria-hidden="true">
					<path d="M500 300 C400 235 300 175 180 130" />
					<path d="M500 300 C600 235 700 175 820 130" />
					<path d="M500 300 C500 365 500 430 500 510" />
				</svg>
				<article class="learning-node learning-node-core">
					<span>Current focus</span>
					<h3>Embedded systems engineering</h3>
				</article>
				<article class="learning-node learning-node-language">
					<span>Language</span>
					<h3>C/C++</h3>
				</article>
				<article class="learning-node learning-node-rtos">
					<span>Systems</span>
					<h3>RTOS</h3>
				</article>
				<article class="learning-node learning-node-hardware">
					<span>Hardware</span>
					<h3>Electronics</h3>
				</article>
			</div>
		</section>

		<section class="contact" id="contact" aria-labelledby="contact-title">
			<p class="eyebrow" id="contact-title">Contact</p>
			<a class="contact-link" href="mailto:jacobkeil13@gmail.com">
				jacobkeil13@gmail.com <span aria-hidden="true">↗</span>
			</a>
		</section>
	</main>
</div>

<footer class="site-footer">
	<div class="site-footer-inner">
		<a class="wordmark" href="#top" aria-label="Back to top">JK<span>.</span></a>
		<p>Jacob Keil</p>
		<div class="footer-links">
			<a href="https://github.com/JacobKeil" target="_blank" rel="noreferrer">GitHub ↗</a>
			<a href="https://www.linkedin.com/in/jacobkeil/" target="_blank" rel="noreferrer">LinkedIn ↗</a>
		</div>
	</div>
</footer>

<svelte:window onkeydown={handlePhotoGalleryKeydown} />

{#if activeGallery}
	<div class="photo-lightbox">
		<button
			class="photo-lightbox-backdrop"
			type="button"
			tabindex="-1"
			aria-label="Close photo viewer"
			onclick={() => void closePhotoGallery()}
		></button>
		<section
			class="photo-carousel"
			role="dialog"
			aria-modal="true"
			aria-label={activeGallery.title}
			bind:this={photoCarouselElement}
		>
			<button
				class="photo-carousel-close"
				type="button"
				aria-label="Close photo viewer"
				bind:this={closePhotoButton}
				onclick={() => void closePhotoGallery()}
			>
				<span aria-hidden="true">×</span>
			</button>
			<button
				class="photo-carousel-arrow"
				type="button"
				aria-label="Previous photo"
				onclick={() => stepPhoto(-1)}
			>
				<span aria-hidden="true">←</span>
			</button>
			<figure class="photo-carousel-image">
				<img
					src={activeGallery.photos[activePhotoIndex].src}
					alt={activeGallery.photos[activePhotoIndex].alt}
				/>
				<figcaption>
					<span>{activeGallery.photos[activePhotoIndex].caption}</span>
					<span>{activePhotoIndex + 1} / {activeGallery.photos.length}</span>
				</figcaption>
			</figure>
			<button
				class="photo-carousel-arrow"
				type="button"
				aria-label="Next photo"
				onclick={() => stepPhoto(1)}
			>
				<span aria-hidden="true">→</span>
			</button>
		</section>
	</div>
{/if}

{#if resumeOpen}
	<div class="resume-lightbox">
		<button
			class="resume-lightbox-backdrop"
			type="button"
			tabindex="-1"
			aria-label="Close résumé viewer"
			onclick={() => void closeResume()}
		></button>
		<section
			class="resume-modal"
			role="dialog"
			aria-modal="true"
			aria-labelledby="resume-modal-title"
			bind:this={resumeModalElement}
		>
			<button
				class="photo-carousel-close"
				type="button"
				aria-label="Close résumé viewer"
				bind:this={resumeCloseButton}
				onclick={() => void closeResume()}
			>
				<span aria-hidden="true">×</span>
			</button>
			<header class="resume-modal-header">
				<h2 id="resume-modal-title">Résumé</h2>
				<div class="resume-modal-actions">
					<a class="button button-light" href="/Resume_Final.pdf" download="Jacob-Keil-Resume.pdf">
						Download PDF <span aria-hidden="true">↓</span>
					</a>
					<a class="resume-open-pdf" href="/Resume_Final.pdf" target="_blank" rel="noreferrer">
						Open PDF <span aria-hidden="true">↗</span>
					</a>
				</div>
			</header>
			<iframe src="/Resume_Final.pdf" title="Jacob Keil résumé"></iframe>
		</section>
	</div>
{/if}
