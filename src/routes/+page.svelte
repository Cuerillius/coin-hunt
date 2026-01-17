<script lang="ts">
	import Coin from '../components/coin.svelte';
	import Contact from '../components/contact.svelte';
	import ContextMenu from '../components/contextMenu.svelte';
	import Panel from '../components/panel.svelte';

	const CELL_SIZE = 32;
	const X_OFFSET = CELL_SIZE * 7;
	const Y_OFFSET = CELL_SIZE * -1;

	let menuX = $state(0);
	let menuY = $state(0);
	let showMenu = $state(false);

	let isBackgroundPressed = $state(false);
	let isPanelPressed = $state(false);
	let isRightClickPressed = $state(false);
	let isSliderDone = $state(false);

	function handleRightClick(e: MouseEvent) {
		e.preventDefault();
		menuX = e.clientX;
		menuY = e.clientY;
		showMenu = true;
	}
</script>

<svelte:window on:contextmenu={isPanelPressed ? handleRightClick : null} />
<ContextMenu x={menuX} y={menuY} bind:show={showMenu} bind:isToggled={isRightClickPressed} />

<div
	class="relative flex min-h-screen w-full items-center justify-center overflow-hidden bg-black font-mono"
>
	<div
		class="pointer-events-none absolute inset-0 z-0"
		style="
			background-image: linear-gradient(to right, #232323 1px, transparent 1px),
				linear-gradient(to bottom, #232323 1px, transparent 1px);
			background-size: 32px 32px;
			background-position: center center;
			-webkit-mask-image: radial-gradient(ellipse 450px 300px at 50% 50%, #000 20%, transparent 100%);
			mask-image: radial-gradient(ellipse 450px 300px at 50% 50%, #000 20%, transparent 100%);
		"
	></div>

	<div
		class="absolute z-10 flex flex-col items-center justify-center transition-all
		{isSliderDone ? 'scale-100 opacity-100 duration-0' : 'scale-95 opacity-0 duration-2500'}"
	>
		<div
			class="relative flex h-37 w-72 flex-col items-center justify-center rounded-3xl bg-neutral-950 p-1 shadow-[0_1px_0_rgba(255,255,255,0.05)] ring-1 ring-white/5"
		>
			<div
				class="relative flex h-full w-full items-center justify-center rounded-2xl border border-black bg-[#030303] shadow-[inset_0_6px_15px_rgba(0,0,0,1)]"
			>
				<div
					class="pointer-events-none absolute inset-0 rounded-2xl shadow-[inset_0_-1px_1px_rgba(255,255,255,0.08)]"
				></div>
				<div
					class="absolute inset-2 flex items-center justify-center rounded-xl border border-white/5 bg-gradient-to-b from-transparent to-neutral-900/30"
				>
					<Coin size="148px" />
				</div>
			</div>
		</div>
	</div>

	<button
		onclick={() => {
			isBackgroundPressed = !isBackgroundPressed;
		}}
		class="pointer-events-auto absolute z-30 cursor-pointer transition-all duration-100 ease-out
		{isBackgroundPressed
			? 'bg-white/10 shadow-[inset_3px_3px_5px_#000,inset_-1px_-1px_0_rgba(255,255,255,0.3)]'
			: ''}"
		style="
			width: {CELL_SIZE - 1}px;
			height: {CELL_SIZE - 1}px;
			left: 50%;
			top: 50%;
			transform: translate(-50%, -50%);
			margin-left: {X_OFFSET}px;
			margin-top: {Y_OFFSET}px;
		"
		aria-label="Secret Button"
	></button>

	<div
		class="cubic-bezier(0.7, 0, 0.3, 1) pointer-events-none absolute inset-0 z-20 flex items-center justify-center transition-all duration-1000"
		class:-translate-y-[22vh]={isSliderDone}
	>
		<div class="pointer-events-auto relative flex items-center justify-center">
			<div class="pointer-events-none absolute inset-0 z-10 flex justify-start pl-8">
				<Panel isUnlocked={isBackgroundPressed} bind:isToggled={isPanelPressed} />
			</div>
			<div class="relative z-20">
				<Contact isTerminalVisible={isRightClickPressed} bind:isSliderDone />
			</div>
		</div>
	</div>
</div>
