<script>
	import { onMount } from 'svelte';

	let ready = $state(false);
	/**
	 * @type {any[]}
	 */
	let stack = $state([]);
	let userStack = $state([]);
	let stackList = $derived(stack.join(', '));
	let score = $derived(stack.length);
	let lastScore = $state(-1);
	let started = $state(false);
	let position = $state(0);
	let allowInput = $state(false);

	onMount(() => {
		if (!document.referrer.includes(window.location.host)) {
			window.location.href = '/';
		} else {
			ready = true;
			addToStack();
			showPattern();
		}
	});

	function addToStack() {
		const colours = ['red', 'blue', 'green', 'amber'];
		const randomColour = colours[Math.floor(Math.random() * colours.length)];
		stack = [...stack, randomColour];
		position = stack.length - 1;
	}
	/**
	 * @param {any} colour
	 */
	function checkStack(colour) {
		if (position < 0) return;
		if (!allowInput) {
			console.warn('Input not allowed at this time.');
			return;
		}
		if (position >= stack.length) {
			console.error('Position out of bounds');
			return;
		}
		if (position == stack.length - 1) {
			// user is at the end of the stack, so if they get the colour right, we add a new colour and reset position
			if (stack[position] === colour) {
				addToStack();
				userStack = [];
				position = 0;
				showPattern();
			} else {
				console.error(`Wrong! Expected ${stack[position]}, but got ${colour}.`);
				resetGame();
				allowInput = false;
			}
		} else {
			// user is not at the end of the stack, so we just check if the colour matches
			if (stack[position] === colour) {
				userStack = [...userStack, colour];
				position++;
			} else {
				console.error(`Wrong! Expected ${stack[position]}, but got ${colour}.`);
				resetGame();
				allowInput = false;
			}
		}
	}
	function showPattern() {
		allowInput = false;
		let i = 0;
		const interval = setInterval(() => {
			if (i >= stack.length) {
				allowInput = true;
				clearInterval(interval);
				return;
			}
			const colour = stack[i];
			const element = document.querySelector(`.bg-${colour}-500`);
			if (element) {
				element.classList.add('bg-white');
				var had_text = element.classList.contains('text-black');
				if (!had_text) {
					element.classList.add('text-black');
				}
				setTimeout(() => {
					if (!had_text) {
						element.classList.remove('text-black');
					}
					element.classList.remove('bg-white');
				}, 1000);
			} else {
				console.error(`Element for colour ${colour} not found.`);
			}
			i++;
		}, 1500);
	}
	function resetGame() {
		lastScore = score - 1;
		stack = [];
		userStack = [];
		position = 0;
		addToStack();
		showPattern();
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
				<div class="flex w-full flex-row items-center justify-between">
					<button
						class="ml-4 w-full cursor-pointer rounded bg-red-600 px-4 py-2 text-white"
						onclick={() => {
							resetGame();
						}}
					>
						Reset
					</button>
					<button
						class="ml-4 w-full cursor-pointer rounded bg-green-600 px-4 py-2 text-white"
						onclick={() => {
							showPattern();
						}}
					>
						Show Pattern
					</button>
				</div>
				<div class="mx-auto w-[10cm]">
					<div class="mt-4 grid grid-cols-2 gap-4">
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class={'flex select-none items-center justify-center rounded-lg border-2 bg-red-500 p-4 transition-all duration-150 ease-in-out' +
								(allowInput
									? ' cursor-pointer hover:scale-105 active:scale-95'
									: ' cursor-not-allowed opacity-50')}
							onclick={(e) => {
								if (!allowInput) return;
								checkStack('red');
								const button = e.currentTarget;
								button.classList.add('bg-red-300', 'scale-95');
								setTimeout(() => {
									button.classList.remove('bg-red-300', 'scale-95');
								}, 150);
							}}
						>
							<p class="font-semibold">Red</p>
						</div>
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class={'flex select-none items-center justify-center rounded-lg border-2 bg-blue-500 p-4 transition-all duration-150 ease-in-out' +
								(allowInput
									? ' cursor-pointer hover:scale-105 active:scale-95'
									: ' cursor-not-allowed opacity-50')}
							onclick={(e) => {
								if (!allowInput) return;
								checkStack('blue');
								const button = e.currentTarget;
								button.classList.add('bg-blue-300', 'scale-95');
								setTimeout(() => {
									button.classList.remove('bg-blue-300', 'scale-95');
								}, 150);
							}}
						>
							<p class="font-semibold">Blue</p>
						</div>
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class={'flex select-none items-center justify-center rounded-lg border-2 bg-green-500 p-4 transition-all duration-150 ease-in-out' +
								(allowInput
									? ' cursor-pointer hover:scale-105 active:scale-95'
									: ' cursor-not-allowed opacity-50')}
							onclick={(e) => {
								if (!allowInput) return;
								checkStack('green');
								const button = e.currentTarget;
								button.classList.add('bg-green-300', 'scale-95');
								setTimeout(() => {
									button.classList.remove('bg-green-300', 'scale-95');
								}, 150);
							}}
						>
							<p class="font-semibold text-black">Green</p>
						</div>
						<!-- svelte-ignore a11y_click_events_have_key_events -->
						<!-- svelte-ignore a11y_no_static_element_interactions -->
						<div
							class={'flex select-none items-center justify-center rounded-lg border-2 bg-amber-500 p-4 transition-all duration-150 ease-in-out' +
								(allowInput
									? ' cursor-pointer hover:scale-105 active:scale-95'
									: ' cursor-not-allowed opacity-50')}
							onclick={(e) => {
								if (!allowInput) return;
								checkStack('amber');
								const button = e.currentTarget;
								button.classList.add('bg-amber-300', 'scale-95');
								setTimeout(() => {
									button.classList.remove('bg-amber-300', 'scale-95');
								}, 150);
							}}
						>
							<p class="font-semibold text-black">Yellow</p>
						</div>
					</div>
				</div>
				<div class="mt-4 flex w-full flex-col items-center">
					<p class="font-semibold">Your score: <span class="font-mono">{score - 1}</span></p>
					{#if lastScore !== -1}
						<p class="font-semibold">Last score: <span class="font-mono">{lastScore}</span></p>
					{/if}
				</div>
			{/if}
		</div>
	</div>
{:else}
	<p>Loading...</p>
{/if}
