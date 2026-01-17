<script lang="ts">
	// Accept the isActive prop (defaults to false)
	// We also capture 'class' as 'className' to apply Tailwind classes from the parent
	let { isActive = false, class: className = '', ...rest } = $props();
</script>

<svg
	xmlns="http://www.w3.org/2000/svg"
	viewBox="0 0 98 96"
	fill="currentColor"
	overflow="visible"
	class="{className} {isActive ? 'is-active' : ''}"
	{...rest}
>
	<path
		fill-rule="evenodd"
		clip-rule="evenodd"
		d="M48.854 0C21.839 0 0 22 0 49.217c0 21.756 13.993 40.172 33.405 46.69 2.427.49 3.316-1.059 3.316-2.362 0-1.141-.08-5.052-.08-9.127-13.59 2.934-16.42-5.867-16.42-5.867-2.184-5.704-5.42-7.17-5.42-7.17-4.448-3.015.324-3.015.324-3.015 4.934.326 7.523 5.052 7.523 5.052 4.367 7.496 11.404 5.378 14.235 4.074.404-3.178 1.699-5.378 3.074-6.6-10.839-1.141-22.243-5.378-22.243-24.283 0-5.378 1.94-9.778 5.014-13.2-.485-1.222-2.184-6.275.486-13.038 0 0 4.125-1.304 13.426 5.052a46.97 46.97 0 0 1 12.214-1.63c4.125 0 8.33.571 12.213 1.63 9.302-6.356 13.427-5.052 13.427-5.052 2.67 6.763.97 11.816.485 13.038 3.155 3.422 5.015 7.822 5.015 13.2 0 18.905-11.404 23.06-22.324 24.283 1.78 1.548 3.316 4.481 3.316 9.126 0 6.6-.08 11.897-.08 13.526 0 1.304.89 2.853 3.316 2.364 19.412-6.52 33.405-24.935 33.405-46.691C97.707 22 75.788 0 48.854 0z"
	/>
	<g transform="translate(-169.9, -193.867)">
		<!-- Added transform-box: fill-box logic via CSS class to ensure scaling works from center -->
		<g class="sparks" style="transform-origin: 219px 241px;">
			<rect
				x="209.721"
				y="174.411"
				width="14.925"
				height="21.814"
				style="transform-origin: 217.566px 184.935px;"
			/>
			<rect
				x="267.332"
				y="228.712"
				width="14.925"
				height="21.814"
				style="transform-origin: 275.177px 239.236px;"
				transform="matrix(0, 1, -1, 0, 2.952377, 2.725205)"
			/>
			<rect
				x="267.332"
				y="228.712"
				width="14.925"
				height="21.814"
				style="transform-origin: 275.177px 239.236px;"
				transform="matrix(0, 1, -1, 0, -113.780869, -0.597458)"
			/>
			<rect
				x="267.332"
				y="228.712"
				width="14.925"
				height="21.814"
				style="transform-origin: 275.177px 239.236px;"
				transform="matrix(-0.707298, 0.706916, -0.706916, -0.707298, -98.564133, -36.253881)"
			/>
			<rect
				x="267.332"
				y="228.712"
				width="14.925"
				height="21.814"
				style="transform-origin: 275.177px 239.236px;"
				transform="matrix(-0.707298, 0.706916, -0.706916, -0.707298, -11.96415, 41.12246)"
			/>
			<rect
				x="267.332"
				y="233.765"
				width="14.925"
				height="22.296"
				style="transform-origin: 275.177px 244.52px;"
				transform="matrix(0.720766, 0.693179, -0.693179, 0.720766, -16.330049, -45.063961)"
			/>
			<rect
				x="267.332"
				y="233.765"
				width="14.925"
				height="22.296"
				style="transform-origin: 275.177px 244.52px;"
				transform="matrix(0.720766, 0.693179, -0.693179, 0.720766, -97.036758, 40.956902)"
			/>
			<rect
				x="211.01"
				y="291.849"
				width="14.925"
				height="21.814"
				style="transform-origin: 218.855px 302.373px;"
			/>
		</g>
	</g>
</svg>

<style>
	.sparks {
		opacity: 0;
		transform: scale(0);
	}

	.is-active .sparks {
		animation: sparks-explode 0.8s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
	}

	@keyframes sparks-explode {
		0% {
			opacity: 0;
			transform: scale(0);
		}
		20% {
			opacity: 1;
		}
		100% {
			opacity: 1;
			transform: scale(1);
		}
	}
</style>
