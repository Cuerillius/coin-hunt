<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import { gsap } from 'gsap';
	import { Draggable } from 'gsap/dist/Draggable';

	let { size = '300px' } = $props();

	const totalFrames: number = 260;
	const fps: number = 60;
	const imgBaseUrl: string = 'https://cerpow.github.io/cerpow-img/coin/';

	let canvas: HTMLCanvasElement;
	let proxy: HTMLDivElement;
	let ctx: CanvasRenderingContext2D | null;

	let images: (HTMLImageElement | null)[] = new Array(totalFrames + 1).fill(null);

	let currentFrame: number = 0;
	let newFrame: number = 0;
	let startTime: number = Date.now();

	let animationFrameId: number;
	let autoPlayInterval: ReturnType<typeof setInterval>;
	let draggableInstance: Draggable[] = [];

	let isDragActive: boolean = false;
	let startDragFrame: number = 0;

	onMount(() => {
		if (typeof window !== 'undefined') {
			gsap.registerPlugin(Draggable);
		}

		if (!canvas) return;
		ctx = canvas.getContext('2d');

		const isMobile: boolean = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(
			navigator.userAgent
		);

		const loadFrame = (index: number) => {
			const img = new Image();
			img.crossOrigin = 'Anonymous';
			img.src = `${imgBaseUrl}${index.toString().padStart(4, '0')}.png`;
			img.onload = () => {
				images[index] = img;
				if (index === 0) {
					drawFrame(0);
					init(isMobile);
				}
			};
		};

		loadFrame(0);

		for (let i = 1; i < totalFrames + 1; i++) {
			loadFrame(i);
		}

		const init = (isMobile: boolean) => {
			renderLoop();
			setupDraggable(isMobile);
			startAutoPlay();
		};
	});

	onDestroy(() => {
		if (typeof window !== 'undefined') {
			cancelAnimationFrame(animationFrameId);
			clearInterval(autoPlayInterval);
			if (draggableInstance[0]) draggableInstance[0].kill();
		}
	});

	function drawFrame(index: number) {
		if (!ctx || !canvas) return;
		const img = images[index];

		if (img && img.complete && img.naturalWidth > 0) {
			ctx.clearRect(0, 0, canvas.width, canvas.height);

			const drawWidth = 400;
			const drawHeight = 400;
			const offsetX = (canvas.width - drawWidth) / 2;
			const offsetY = (canvas.height - drawHeight) / 2;

			ctx.drawImage(img, offsetX, offsetY, drawWidth, drawHeight);
		}
	}

	function renderLoop() {
		animationFrameId = requestAnimationFrame(renderLoop);
		const now = Date.now();
		const elapsed = now - startTime;

		if (elapsed > 1000 / fps) {
			startTime = now - (elapsed % 1000) / fps;

			if (currentFrame !== newFrame) {
				drawFrame(currentFrame);
				newFrame = currentFrame;
			}
		}
	}

	function resetWithinBounds(frame: number): number {
		const value = frame % totalFrames;
		return value < 0 ? Math.floor(totalFrames + value) : Math.floor(value);
	}

	function startAutoPlay() {
		isDragActive = false;
		startDragFrame = 0;

		if (draggableInstance[0]) {
			draggableInstance[0].kill();
			gsap.set(proxy, { x: 0 });
			setupDraggable(/Android|webOS|iPhone/i.test(navigator.userAgent));
		}

		clearInterval(autoPlayInterval);
		autoPlayInterval = setInterval(() => {
			currentFrame = resetWithinBounds(currentFrame + 1);
		}, 20);
	}

	function setupDraggable(isMobile: boolean) {
		if (!proxy) return;

		draggableInstance = Draggable.create(proxy, {
			trigger: canvas,
			type: 'x',
			inertia: true,
			allowContextMenu: true,
			minimumMovement: 0,
			cursor: 'grab',
			activeCursor: 'grabbing',
			throwResistance: isMobile ? 1800 : 2200,
			dragResistance: isMobile ? 0.42 : 0.6,
			onPress: function (this: Draggable) {
				if (isDragActive) return;
				isDragActive = true;
				clearInterval(autoPlayInterval);
				startDragFrame = currentFrame;
			},
			onDrag: function (this: Draggable) {
				currentFrame = resetWithinBounds(startDragFrame + this.x);
			},
			onThrowUpdate: function (this: Draggable) {
				currentFrame = resetWithinBounds(startDragFrame + this.x);
			},
			onThrowComplete: function (this: Draggable) {
				setTimeout(startAutoPlay, 50);
			},
			onRelease: function (this: Draggable) {
				if (this.isThrowing) return;
				startAutoPlay();
			}
		});
	}
</script>

<div class="coin-wrapper" style="max-width: {size};">
	<canvas bind:this={canvas} width="600" height="600"></canvas>
	<div bind:this={proxy} class="proxy"></div>
</div>

<style>
	.coin-wrapper {
		position: relative;
		width: 100%;
		height: auto;
		aspect-ratio: 1 / 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	canvas {
		width: 100%;
		height: auto;
		display: block;
		user-select: none;
		cursor: grab;
		filter: drop-shadow(0px 10px 15px rgba(0, 0, 0, 0.5));
		transform: none !important;
	}

	canvas:active {
		cursor: grabbing;
	}

	.proxy {
		position: absolute;
		top: 0;
		left: 0;
		width: 1px;
		height: 1px;
		visibility: hidden;
		pointer-events: none;
	}
</style>
