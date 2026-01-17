<script lang="ts">
	import { Motion, useMotionTemplate, useMotionValue } from 'svelte-motion';

	let { isUnlocked, isToggled = $bindable(false) } = $props();

	let mouseX = useMotionValue(0);
	let mouseY = useMotionValue(0);
	let background = useMotionTemplate`radial-gradient(200px circle at ${mouseX}px ${mouseY}px, rgba(51, 51, 51, 0.2), transparent 60%)`;
</script>

<div
	role="region"
	aria-label="Panel"
	onmousemove={(e) => {
		const { left, top } = e.currentTarget.getBoundingClientRect();
		mouseX.set(e.clientX - left);
		mouseY.set(e.clientY - top);
	}}
	class="
        group pointer-events-auto relative w-48 overflow-hidden rounded-2xl
        bg-neutral-950 transition-all duration-700
        ease-[cubic-bezier(0.34,1.56,0.64,1)]
    "
	style="
		--spotlight-color: rgba(255, 255, 255, 0.08);
		{isUnlocked ? 'box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);' : ''}
        transform: translateY({isUnlocked ? '-25%' : '0'});
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

	<div class="relative flex flex-col gap-4 rounded-2xl border border-white/10 px-5 py-4">
		<div class="flex items-center justify-between">
			<div class="text-xs font-medium text-neutral-400">Security Unlocked</div>
			<button
				onclick={() => (isToggled = !isToggled)}
				aria-label="Toggle Security Unlock"
				class="relative h-5 w-9 cursor-pointer rounded-full transition-all duration-300 ease-[cubic-bezier(0.23,1,0.32,1)]
				{isToggled
					? 'bg-white shadow-[0_0_10px_rgba(255,255,255,0.3)]'
					: 'bg-neutral-800 ring-1 ring-neutral-700 ring-inset'}"
				aria-pressed={isToggled}
			>
				<span
					class="absolute top-0.5 left-0.5 h-4 w-4 rounded-full shadow-md transition-transform duration-300 ease-[cubic-bezier(0.23,1,0.32,1)]
					{isToggled ? 'translate-x-4 bg-neutral-950' : 'translate-x-0 bg-neutral-400'}"
				></span>
			</button>
		</div>
		<div class="py-2"></div>
	</div>
</div>

<style>
	div {
		transform-style: preserve-3d;
	}
</style>
