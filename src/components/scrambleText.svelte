<script lang="ts">
	let {
		textOriginal,
		textEncrypted,
		isTextEncrypted = false,
		class: className = ''
	} = $props<{
		textOriginal: string;
		textEncrypted: string;
		isTextEncrypted?: boolean;
		class?: string;
	}>();

	const CHARS: string = '!<>-_\\/[]{}—=+*^?#________';

	interface QueueItem {
		from: string;
		to: string;
		start: number;
		end: number;
		char: string;
	}

	let currentRawText: string = isTextEncrypted ? textEncrypted : textOriginal;

	let renderedHtml = $state<string>(currentRawText);

	$effect(() => {
		const targetText: string = isTextEncrypted ? textEncrypted : textOriginal;

		if (currentRawText === targetText) return;

		const oldText: string = currentRawText;
		const length: number = Math.max(oldText.length, targetText.length);
		const queue: QueueItem[] = [];

		for (let i = 0; i < length; i++) {
			const from: string = oldText[i] || '';
			const to: string = targetText[i] || '';
			const start: number = Math.floor(Math.random() * 40);
			const end: number = start + Math.floor(Math.random() * 40);
			queue.push({ from, to, start, end, char: '' });
		}

		let frame: number = 0;
		let frameRequest: number | undefined;

		const update = (): void => {
			let output: string = '';
			let complete: number = 0;

			for (let i = 0, n = queue.length; i < n; i++) {
				let { from, to, start, end, char } = queue[i];

				if (frame >= end) {
					complete++;
					output += to;
				} else if (frame >= start) {
					if (!char || Math.random() < 0.28) {
						char = CHARS[Math.floor(Math.random() * CHARS.length)];
						queue[i].char = char;
					}
					output += `<span class="dud">${char}</span>`;
				} else {
					output += from;
				}
			}

			renderedHtml = output;

			if (complete === queue.length) {
				currentRawText = targetText;
			} else {
				frameRequest = requestAnimationFrame(update);
				frame++;
			}
		};

		update();

		return () => {
			if (frameRequest) cancelAnimationFrame(frameRequest);
		};
	});
</script>

<div class={className}>
	{@html renderedHtml}
</div>

<style>
	:global(.dud) {
		color: #757575;
		opacity: 0.7;
	}
</style>
