<script>
	import { setContext } from 'svelte';

	let ready = $state(false);
	let stack = $state([]);
	let started = $state(false);
	let position = $state(0);

	// Only runs in the browser
	if (typeof window !== 'undefined') {
		if (!document.referrer.includes(window.location.host)) {
			window.location.href = '/';
		} else {
			ready = true;
		}
	}

	function addToStack() {
		// Pick a random colour to add to the stack
		const colours = ['red', 'blue', 'green', 'yellow'];
		const randomColour = colours[Math.floor(Math.random() * colours.length)];
		stack = [...stack, randomColour];
		position = stack.length - 1;
	}
	/**
	 * @param {any} colour
	 */
	function checkStack(colour) {
		if (stack[position] === colour) {
			position--;
			if (position < 0) {
				addToStack();
			}
		} else {
			// Reset the stack if the wrong colour is clicked
			stack = [];
			position = 0;
		}
	}
</script>

{#if ready}
	<div class="mx-auto flex w-full max-w-3xl flex-col items-center gap-4 p-2">
		<div class="w-full">
			<h1 class="text-center text-5xl font-semibold">You found the real Easter Egg!</h1>
			<p class="text-center text-lg">If you looked at the source code, that's cheating.</p>
			<p class="text-center text-lg">Enjoy simon says!</p>
		</div>
		<div class="flex w-full flex-col justify-center rounded-xl border-2 p-4">
			{#if !started}
				<button
					class="rounded bg-blue-500 px-4 py-2 text-white"
					onclick={() => {
						stack.push('simon');
						started = true;
					}}
				>
					Start Simon Says
				</button>
			{/if}
			{#if started}
				<button
					class="ml-4 rounded bg-red-500 px-4 py-2 text-white"
					onclick={() => {
						stack = [];
						started = false;
					}}
				>
					Reset
				</button>
				<div class="mx-auto w-[10cm]">
					<div class="mt-4 grid grid-cols-2 gap-4">
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class="flex items-center justify-center rounded-lg border-2 p-4"
							onclick={() => checkStack('red')}
						>
							<p>Red</p>
						</div>
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class="flex items-center justify-center rounded-lg border-2 p-4"
							onclick={() => checkStack('blue')}
						>
							<p>Blue</p>
						</div>
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class="flex items-center justify-center rounded-lg border-2 p-4"
							onclick={() => checkStack('green')}
						>
							<p>Green</p>
						</div>
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class="flex items-center justify-center rounded-lg border-2 p-4"
							onclick={() => checkStack('yellow')}
						>
							<p>Yellow</p>
						</div>
					</div>
				</div>
			{/if}
		</div>
	</div>
{:else}
	<p>Loading...</p>
{/if}
