<script lang="ts">
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';

	let { isDone = $bindable(false) } = $props();

	let containerWidth = $state(0);
	let handleWidth = 48;
	let padding = 8;

	let startX = 0;
	let startHandlePos = 0;
	let isDragging = $state(false);

	const handlePos = tweened(0, { duration: 200, easing: cubicOut });

	let maxMove = $derived(Math.max(0, containerWidth - handleWidth - padding));

	$effect(() => {
		if (!isDragging) {
			handlePos.set(isDone ? maxMove : 0);
		}
	});

	function handleStart(e: MouseEvent | TouchEvent) {
		isDragging = true;
		startX =
			e.type === 'touchstart' ? (e as TouchEvent).touches[0].clientX : (e as MouseEvent).clientX;

		startHandlePos = $handlePos;
	}

	function handleMove(e: MouseEvent | TouchEvent) {
		if (!isDragging) return;

		const currentX =
			e.type === 'touchmove' ? (e as TouchEvent).touches[0].clientX : (e as MouseEvent).clientX;

		const delta = currentX - startX;
		const targetPos = startHandlePos + delta;
		const newPos = Math.max(0, Math.min(targetPos, maxMove));
		handlePos.set(newPos, { duration: 0 });
	}

	function handleEnd() {
		if (!isDragging) return;
		isDragging = false;

		const threshold = maxMove * 0.5;

		if (!isDone) {
			if ($handlePos > threshold) {
				handlePos.set(maxMove);
				isDone = true;
			} else {
				handlePos.set(0);
				isDone = false;
			}
		} else {
			if ($handlePos < threshold) {
				handlePos.set(0);
				isDone = false;
			} else {
				handlePos.set(maxMove);
				isDone = true;
			}
		}
	}
</script>

<svelte:window
	onmousemove={handleMove}
	onmouseup={handleEnd}
	ontouchmove={handleMove}
	ontouchend={handleEnd}
/>

<div
	bind:clientWidth={containerWidth}
	class="relative h-full w-full overflow-hidden rounded-lg border border-white/10 bg-white/5 p-1 select-none"
>
	<div
		role="button"
		tabindex="0"
		onmousedown={handleStart}
		ontouchstart={handleStart}
		style="transform: translateX({$handlePos}px)"
		class="relative z-10 flex h-full w-12 cursor-grab items-center justify-center rounded-md bg-white shadow-xl transition-colors duration-200 active:cursor-grabbing
"
	>
		<svg
			class="h-5 w-5 text-black transition-all duration-300"
			fill="none"
			viewBox="0 0 24 24"
			stroke="currentColor"
		>
			{#if isDone}
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					stroke-width="3"
					d="M11 17l-5-5m0 0l5-5m-5 5h12"
				/>
			{:else}
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					stroke-width="3"
					d="M13 7l5 5m0 0l-5 5m5-5H6"
				/>
			{/if}
		</svg>
	</div>
</div>
