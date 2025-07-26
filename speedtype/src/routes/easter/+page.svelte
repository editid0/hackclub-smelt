<script>
	import { setContext } from 'svelte';

	let ready = $state(false);
	/**
	 * @type {any[]}
	 */
	let stack = $state([]);
	let userStack = $state([]);
	let stackList = $derived(stack.join(', '));
	let started = $state(false);
	let position = $state(0);

	// Only runs in the browser
	if (typeof window !== 'undefined') {
		if (!document.referrer.includes(window.location.host)) {
			window.location.href = '/';
		} else {
			ready = true;
			addToStack();
		}
	}

	function addToStack() {
		// Pick a random colour to add to the stack
		const colours = ['red', 'blue', 'green', 'yellow'];
		const randomColour = colours[Math.floor(Math.random() * colours.length)];
		stack = [...stack, randomColour];
		position = stack.length - 1;
		console.log(`New stack: ${stackList}`);
	}
	/**
	 * @param {any} colour
	 */
	function checkStack(colour) {
		if (position < 0) return;
		if (position >= stack.length) {
			console.error('Position out of bounds');
			return;
		}
		if (position == stack.length - 1) {
			// user is at the end of the stack, so if they get the colour right, we add a new colour and reset position
			if (stack[position] === colour) {
				console.log(`Correct! ${colour} was the last colour in the stack.`);
				addToStack();
				userStack = [];
				position = 0;
			} else {
				console.error(`Wrong! Expected ${stack[position]}, but got ${colour}.`);
				userStack = [];
				position = 0;
			}
		} else {
			// user is not at the end of the stack, so we just check if the colour matches
			if (stack[position] === colour) {
				console.log(`Correct! ${colour} matches the stack at position ${position}.`);
				userStack = [...userStack, colour];
				position++;
			} else {
				console.error(`Wrong! Expected ${stack[position]}, but got ${colour}.`);
				userStack = [];
				position = 0;
			}
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
							class="red flex items-center justify-center rounded-lg border-2 p-4"
							onclick={() => checkStack('red')}
						>
							<p>Red</p>
						</div>
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class="blue flex items-center justify-center rounded-lg border-2 p-4"
							onclick={() => checkStack('blue')}
						>
							<p>Blue</p>
						</div>
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class="green flex items-center justify-center rounded-lg border-2 p-4"
							onclick={() => checkStack('green')}
						>
							<p>Green</p>
						</div>
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class="yellow flex items-center justify-center rounded-lg border-2 p-4"
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

<style>
	.red {
		background-color: red;
		color: white;
	}
	.blue {
		background-color: blue;
		color: white;
	}
	.green {
		background-color: green;
		color: white;
	}
	.yellow {
		background-color: yellow;
		color: black;
	}
</style>
