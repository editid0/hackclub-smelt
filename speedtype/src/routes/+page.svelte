<script lang="ts">
	import { onMount } from 'svelte';

	let sentences = [
		'The quick brown fox jumps over the lazy dog.',
		'Smelt is a YSWS.',
		'Typescript is worse than Javascript.',
		'Python is the best programming language.',
		'She sells seashells by the seashore.',
		'The juxtaposition of modern architecture and ancient ruins was strikingly beautiful.',
		'"Don\'t", Dan dared, "dash down dusty, dimly-lit docks during December\'s dismal drizzle - disaster\'s definitely due."',
		'The five boxing wizards jump quickly.',
		"Bizarrely, the judge's jumbled justification jeopardised Jerry's jobial journey, jolting justifications into jittery, jigsaw-like jargon.",
		"Jack's jittery justification - jarringly juxtaposed with Jill's judicious jargon - juggled jeapardy, jest, and jaded judgement, just as Jeremy jotted jealous journal entries joylessly."
	];

	let sampleText = $state('Loading...');
	let userInput = $state('');
	let startTime: number | null = null;
	let wpm = $state(0);
	let accuracy = $state(100);
	let inputEnabled = $state(false);
	let shiftPressed = $state(false);
	let allowBackspace = $state(true);
	let allowBackspaceChange = $state(true);

	const calculateWPM = () => {
		const words = userInput.trim().split(/\s+/).length;
		const minutes = (Date.now() - (startTime ?? 0)) / 60000;
		return Math.round(words / minutes);
	};

	const calculateAccuracy = () => {
		const correctChars = [...userInput].filter((char, i) => char === sampleText[i]).length;
		return Math.round((correctChars / sampleText.length) * 100);
	};

	function handleInput(e: Event) {
		if (!startTime) startTime = Date.now();
		if (!inputEnabled) return;

		allowBackspaceChange = false;

		userInput = (e.target as HTMLTextAreaElement).value;

		wpm = calculateWPM();
		accuracy = calculateAccuracy();
		if (userInput.length >= sampleText.length) {
			if (accuracy < 0) accuracy = 0;
			inputEnabled = false;
			if (wpm > 600) {
				resetGame();
			}
		}
	}

	function preventBackspace(event: { keyCode: number; preventDefault: () => void }) {
		if (event.keyCode === 8) {
			if (!allowBackspace) {
				event.preventDefault();
				return;
			}
		}
	}

	function resetGame() {
		userInput = '';
		startTime = null;
		wpm = 0;
		accuracy = 100;
		inputEnabled = true;
		allowBackspaceChange = true;
	}

	onMount(() => {
		document.addEventListener('keydown', (e) => {
			if (e.key === 'Shift') {
				shiftPressed = true;
			}
		});
		document.addEventListener('keyup', (e) => {
			if (e.key === 'Shift') {
				shiftPressed = false;
			}
		});
		sampleText = sentences[Math.floor(Math.random() * sentences.length)];
		inputEnabled = true;
		document.getElementById('userInput')?.focus();
	});
</script>

<div class="mx-auto flex w-full max-w-3xl flex-col items-center gap-4 p-2">
	<h1 class="text-center text-5xl font-semibold">SpeedType</h1>
	<p class="text-center text-lg">
		Type the text below as quickly and accurately as you can, time starts when you first press a
		key.
	</p>
	<p class="flex flex-row items-center gap-2">
		Allow backspace? <input
			type="checkbox"
			bind:checked={allowBackspace}
			disabled={!allowBackspaceChange}
			class="size-7"
		/>
	</p>
	<p
		class="mx-auto w-fit rounded-xl border-2 p-2 text-center font-mono text-xl"
		style="white-space: pre-wrap; user-select: none;"
	>
		{#each sampleText.split('') as char, i}
			<span class={userInput[i] === char ? 'correct' : userInput[i] ? 'incorrect' : 'unknown'}>
				{char}
			</span>
		{/each}
	</p>
	<textarea
		rows="5"
		bind:value={userInput}
		oninput={handleInput}
		onkeydown={preventBackspace}
		placeholder="Type here..."
		class="max-w-full rounded-lg border-2 p-2 font-mono text-lg outline-0"
		style="resize: none;"
		readonly={!inputEnabled}
		id="userInput"
	></textarea>
	<div class="w-full max-w-md rounded-lg border-2 p-4 text-center">
		<h2 class="text-3xl font-semibold">Your stats</h2>
		{#if wpm === 0}
			<p class="text-lg">Start typing to see your stats!</p>
		{:else}
			<p class="text-lg">Your stats stats:</p>
			<p>WPM: <span class="font-mono">{wpm}</span></p>
			<p>Accuracy: <span class="font-mono">{accuracy}%</span></p>
		{/if}
	</div>
	{#if userInput.length >= sampleText.length}
		<div>
			<button
				onclick={resetGame}
				class="mt-4 rounded-lg bg-blue-500 px-4 py-2 text-white hover:bg-blue-600">Retry</button
			>
		</div>
	{/if}
</div>

<!-- Easter Egg Trigger -->
{#if userInput.includes('editid')}
	<div class="fullscreen bg-white dark:bg-black">
		<h2>Hey!</h2>
		<p>You've found the easter egg. Congratulations!</p>
		<a
			href="/secret"
			class="text-blue-500 hover:underline"
			onclick={(e) => {
				e.preventDefault();
				if (!shiftPressed) {
					window.location.href = 'https://youtube.com/watch?v=dQw4w9WgXcQ';
				} else {
					window.location.href = '/easter';
				}
			}}>View Easter Egg</a
		>
	</div>
{/if}

<style>
	textarea {
		width: 100%;
		font-family: monospace;
		padding: 1rem;
		font-size: 1.2rem;
	}
	.correct {
		color: black;
		background-color: rgb(3, 190, 3);
	}
	.incorrect {
		color: white;
		background-color: rgb(255, 0, 0);
	}
	.correct,
	.incorrect,
	.unknown {
		padding: 0 1px;
	}
	.fullscreen {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 1000;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
</style>
