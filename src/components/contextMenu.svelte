<script lang="ts">
	let { x, y, show = $bindable(false), isToggled = $bindable(false) } = $props();

	function handleToggle() {
		isToggled = !isToggled;
		setTimeout(() => {
			show = false;
		}, 250);
	}
</script>

{#if show}
	<div
		class="backdrop"
		role="presentation"
		onclick={() => (show = false)}
		oncontextmenu={(e) => {
			e.preventDefault();
			show = false;
		}}
	></div>

	<div class="menu" style="top: {y}px; left: {x}px;">
		<button class="menu-item" onclick={handleToggle}>
			<span class="label">Encrypted Text</span>

			<div class="checkbox" class:is-checked={!isToggled}>
				<svg viewBox="0 0 14 14" class="check-icon">
					<path
						d="M3 7 L6 10 L11 4"
						stroke="currentColor"
						stroke-width="2.5"
						fill="none"
						stroke-linecap="round"
						stroke-linejoin="round"
					/>
				</svg>
			</div>
		</button>
	</div>
{/if}

<style>
	.backdrop {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 999;
		cursor: default;
	}

	.menu {
		position: fixed;
		z-index: 1000;
		background: #171717;
		border: 1px solid #262626;
		border-radius: 12px;
		padding: 6px;
		box-shadow:
			0 10px 15px -3px rgba(0, 0, 0, 0.5),
			0 4px 6px -2px rgba(0, 0, 0, 0.3);
		min-width: 180px;
		display: flex;
		flex-direction: column;
		overflow: hidden;
	}

	.menu-item {
		display: flex;
		align-items: center;
		justify-content: space-between;
		width: 100%;
		background: transparent;
		border: none;
		padding: 10px 12px;
		cursor: pointer;
		border-radius: 8px;
		transition: background 0.2s ease;
		gap: 16px;
	}

	.menu-item:hover {
		background: #262626;
	}

	.label {
		color: #d4d4d4;
		font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, monospace;
		font-size: 13px;
		font-weight: 500;
	}

	.checkbox {
		width: 20px;
		height: 20px;
		border-radius: 6px;
		border: 1.5px solid #404040;
		background: #262626;
		display: flex;
		align-items: center;
		justify-content: center;
		transition: all 0.2s cubic-bezier(0.23, 1, 0.32, 1);
		color: transparent;
	}
	.checkbox.is-checked {
		background: #ffffff;
		border-color: #ffffff;
		color: #000000;
		transform: scale(1.05);
	}

	.check-icon {
		width: 12px;
		height: 12px;
		opacity: 0;
		transform: scale(0.5) rotate(-10deg);
		transition: all 0.2s cubic-bezier(0.34, 1.56, 0.64, 1);
	}

	.checkbox.is-checked .check-icon {
		opacity: 1;
		transform: scale(1) rotate(0deg);
	}
</style>
