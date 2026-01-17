<script lang="ts">
	import { Motion, useMotionTemplate, useMotionValue } from 'svelte-motion';
	import Knob from './knob.svelte';
	import Slide from './slide.svelte';
	import ScrambleText from './scrambleText.svelte';

	let { isTerminalVisible = $bindable(false), isSliderDone = $bindable(false) } = $props();
	let isKnobUnlocked = $state(false);

	let isSliderUnlocked = $state(false);

	let terminalInput = $state('');
	let terminalRef: HTMLInputElement;

	let mouseX = useMotionValue(0);
	let mouseY = useMotionValue(0);
	let background = useMotionTemplate`radial-gradient(200px circle at ${mouseX}px ${mouseY}px, rgba(51, 51, 51, 0.2), transparent 60%)`;
	let MAIL = 'contact@cyri.li';

	const springProps = { stiffness: 200, damping: 25, mass: 0.5 };

	$effect(() => {
		if (isTerminalVisible && terminalRef) {
			setTimeout(() => terminalRef.focus(), 50);
		}
	});

	function handleTerminalClick() {
		if (isTerminalVisible && terminalRef) {
			terminalRef.focus();
		}
	}

	function handleKeydown(e: KeyboardEvent) {
		if (e.key === 'Enter') {
			processCommand();
		}
	}

	function processCommand() {
		const command = terminalInput.trim().toLowerCase();

		if (command.includes('unlock')) {
			isKnobUnlocked = true;
		}

		if (command.includes('lock') && !command.includes('unlock')) {
			isKnobUnlocked = false;
		}

		if (command === 'help') {
			console.log('Available commands: unlock, lock');
		}

		terminalInput = '';
	}
</script>

<div
	role="button"
	tabindex="0"
	aria-label="Contact Card"
	onmousemove={(e) => {
		const { left, top } = e.currentTarget.getBoundingClientRect();
		mouseX.set(e.clientX - left);
		mouseY.set(e.clientY - top);
	}}
	onclick={handleTerminalClick}
	onkeydown={(e) => {
		if (e.key === ' ' && !isTerminalVisible) {
			handleTerminalClick();
		}
	}}
	class="group relative w-80 overflow-hidden rounded-2xl bg-neutral-950 transition-all duration-500 outline-none focus:ring-2 focus:ring-white/20"
	style="
		--spotlight-color: rgba(255, 255, 255, 0.08);
		{isKnobUnlocked ? 'box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);' : ''}
	"
>
	<div
		class="absolute top-0 right-5 h-px w-80 bg-linear-to-l from-transparent via-white/20 to-transparent opacity-50"
	></div>

	<Motion style={{ background }} let:motion>
		<div
			use:motion
			class="pointer-events-none absolute -inset-px rounded-xl opacity-0 transition-opacity duration-300 group-hover:opacity-100"
		></div>
	</Motion>

	<div class="relative flex h-45 flex-col gap-3 rounded-2xl border border-white/10 px-5 py-6">
		<div class="space-y-2">
			<div class="relative flex h-8 flex-row items-center justify-between">
				<div
					class="flex items-center overflow-hidden font-mono text-xl font-bold tracking-tight text-neutral-100"
				>
					<ScrambleText
						textOriginal="$> "
						textEncrypted="Cyrill Weber"
						isTextEncrypted={!isTerminalVisible}
						class="whitespace-pre text-neutral-100"
					/>

					{#if isTerminalVisible}
						<div class="relative ml-2 flex items-center">
							<span class="whitespace-pre text-neutral-300">{terminalInput}</span>
							<span class="ml-0.5 inline-block h-5 w-0.5 animate-pulse bg-white align-middle"
							></span>
						</div>
					{/if}
				</div>

				{#if isTerminalVisible}
					<input
						bind:this={terminalRef}
						bind:value={terminalInput}
						onkeydown={handleKeydown}
						type="text"
						class="absolute inset-0 z-40 h-full w-10 cursor-text bg-transparent text-transparent caret-transparent opacity-0 focus:outline-none"
						spellcheck="false"
						autocomplete="off"
					/>
				{/if}

				<Knob canUnlock={isKnobUnlocked} bind:isUnlocked={isSliderUnlocked} />
			</div>

			<ScrambleText
				textOriginal="terminal@host:0"
				textEncrypted={MAIL}
				isTextEncrypted={!isTerminalVisible}
				class="h-6 pb-4 text-sm font-medium text-neutral-500"
			/>

			<div class="relative h-14 w-full overflow-hidden rounded-xl" style="perspective: 1200px;">
				<div class="absolute inset-0 bg-neutral-900/40">
					<Slide bind:isDone={isSliderDone} />
				</div>

				<Motion
					animate={{
						y: isSliderUnlocked ? -56 : 0,
						scale: isSliderUnlocked ? 0.9 : 1,
						rotateX: isSliderUnlocked ? 90 : 0
					}}
					transition={{
						type: 'spring',
						...springProps
					}}
					let:motion
				>
					<a
						use:motion
						href="mailto:{MAIL}"
						class="absolute inset-0 z-10 flex items-center justify-center rounded-xl bg-white font-bold text-black shadow-xl transition-colors hover:bg-neutral-200"
						style="transform-origin: top; backface-visibility: hidden;"
					>
						Contact
					</a>
				</Motion>
			</div>
		</div>
	</div>
</div>

<style>
	div {
		transform-style: preserve-3d;
	}
	input {
		pointer-events: auto;
	}
</style>
