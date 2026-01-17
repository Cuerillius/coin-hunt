<script lang="ts">
	import { spring } from 'svelte/motion';
	import GithubVaultLogo from './../lib/assets/github-vault.svelte';

	let { canUnlock = false, isUnlocked = $bindable(false) } = $props();

	let dialElement: HTMLDivElement;
	let isDragging = $state(false);

	let currentRotationValue = $state(isUnlocked ? 360 : 0);
	let previousAngle = 0;

	let rotationSpring = spring(currentRotationValue, { stiffness: 0.05, damping: 0.3 });

	$effect(() => {
		if (isDragging) return;

		if (isUnlocked) {
			currentRotationValue = 360;
			rotationSpring.set(360);
		} else {
			currentRotationValue = 0;
			rotationSpring.set(0);
		}
	});

	function getAngle(clientX: number, clientY: number) {
		if (!dialElement) return 0;
		const rect = dialElement.getBoundingClientRect();
		const centerX = rect.left + rect.width / 2;
		const centerY = rect.top + rect.height / 2;

		const radians = Math.atan2(clientY - centerY, clientX - centerX);
		let degrees = radians * (180 / Math.PI);
		return degrees + 90;
	}

	function startDrag(e: MouseEvent) {
		if (!canUnlock) return;

		isDragging = true;
		e.preventDefault();

		previousAngle = getAngle(e.clientX, e.clientY);

		window.addEventListener('mousemove', onDrag);
		window.addEventListener('mouseup', stopDrag);
	}

	function onDrag(e: MouseEvent) {
		if (!isDragging) return;

		const currentAngle = getAngle(e.clientX, e.clientY);
		let delta = currentAngle - previousAngle;

		if (delta > 180) delta -= 360;
		if (delta < -180) delta += 360;

		currentRotationValue += delta;

		if (currentRotationValue < 0) currentRotationValue = 0;
		if (currentRotationValue > 360) currentRotationValue = 360;

		rotationSpring.set(currentRotationValue);
		previousAngle = currentAngle;
	}

	function stopDrag() {
		isDragging = false;
		window.removeEventListener('mousemove', onDrag);
		window.removeEventListener('mouseup', stopDrag);

		if (currentRotationValue > 180) {
			currentRotationValue = 360;
			rotationSpring.set(360);
			isUnlocked = true;
		} else {
			currentRotationValue = 0;
			rotationSpring.set(0);
			isUnlocked = false;
		}
	}
</script>

<div class="relative flex items-center justify-center select-none">
	{#if !canUnlock}
		<a
			href="https://github.com/Cuerillius"
			target="_blank"
			class="flex h-6 w-6 items-center justify-center text-neutral-400 transition-colors hover:text-white"
			draggable="false"
		>
			<GithubVaultLogo class="h-6 w-6" />
		</a>
	{:else}
		<div
			class="flex cursor-grab items-center justify-center outline-none active:cursor-grabbing"
			onmousedown={startDrag}
			role="slider"
			tabindex="0"
			aria-valuenow={currentRotationValue}
			aria-valuemin="0"
			aria-valuemax="360"
		>
			<div
				bind:this={dialElement}
				class="text-neutral-400 transition-colors"
				class:text-white={isUnlocked}
				style="transform: rotate({$rotationSpring}deg);"
			>
				<GithubVaultLogo class="is-active h-6 w-6" />
			</div>
		</div>
	{/if}
</div>
