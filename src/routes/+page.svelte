<script>
	import { ArrowRight, Check, Files, RectangleVertical } from '@lucide/svelte';

	let strength = $state('weaker');

	let length = $state(15);

	let includeUpper = $state(false);
	let includeLower = $state(true);
	let includeNumbers = $state(false);
	let includeSymbols = $state(false);

	let password = $state('');

	$effect(() => {
		const slider = document.getElementById('lengthRange');
		if (slider) {
			const percent = (length / slider.max) * 100;
			slider.style.background = `linear-gradient(to right, var(--color-ma) 0%, var(--color-ma) ${percent}%, var(--color-mb) ${percent}%, var(--color-mb) 100%)`;
		}
	});

	function generatePassword(upper, lower, number, symbol, length) {
		let characters = [];

		if (upper) {
			for (let i = 65; i <= 90; i++) characters.push(String.fromCharCode(i));
		}
		if (lower) {
			for (let i = 97; i <= 122; i++) characters.push(String.fromCharCode(i));
		}
		if (number) {
			for (let i = 48; i <= 57; i++) characters.push(String.fromCharCode(i));
		}
		if (symbol) {
			for (let i = 33; i <= 47; i++) characters.push(String.fromCharCode(i));
			for (let i = 58; i <= 64; i++) characters.push(String.fromCharCode(i));
			for (let i = 91; i <= 96; i++) characters.push(String.fromCharCode(i));
			for (let i = 123; i <= 126; i++) characters.push(String.fromCharCode(i));
		}

		if (characters.length === 0) {
			for (let i = 97; i <= 122; i++) characters.push(String.fromCharCode(i));
			includeLower = true;
		}

		let password = '';

		for (let i = 0; i < length; i++) {
			password = password + characters[Math.floor(Math.random() * characters.length)];
		}

		strength = calculateStrength(upper, lower, number, symbol, length);

		return password;
	}

	function calculateStrength(upper, lower, number, symbol, length) {
		let score = 0;

		if (length <= 5) score += 1;
		else if (length <= 10) score += 2;
		else if (length <= 15) score += 3;
		else score += 4;

		let typesCount = 0;
		if (upper) typesCount++;
		if (lower) typesCount++;
		if (number) typesCount++;
		if (symbol) typesCount++;

		score += typesCount;

		if (score <= 3) return 'weaker';
		else if (score <= 5) return 'weak';
		else if (score <= 7) return 'medium';
		else return 'strong';
	}
</script>

<h2 class="text-st text-xl">Password Generator</h2>

<div class="bg-sb relative flex w-full max-w-lg items-center px-3 py-3 pr-8">
	<div class="scrollbar-none max-w-[93%] overflow-x-scroll">
		{#if !password}
			<p class="text-st grow text-2xl font-bold select-none">P4$5W0rD!</p>
		{:else}
			<p class="grow text-2xl font-bold">
				{password}
			</p>
		{/if}
		<button
			class="absolute top-1/2 right-4 -translate-y-1/2 hover:cursor-pointer"
			onclick={() => {
				navigator.clipboard.writeText(password).catch((error) => {
					console.error('Failed to copy text: ', error);
				});
			}}
		>
			<Files class="text-ma hover:text-mt" />
		</button>
	</div>
</div>

<div class="bg-sb flex w-full max-w-lg flex-col px-4 py-4">
	<div class="flex items-center justify-between capitalize">
		<p>character length</p>
		<span class="text-ma text-2xl">{length}</span>
	</div>

	<input
		type="range"
		id="lengthRange"
		min="0"
		max="30"
		bind:value={length}
		oninput={(e) => {
			const target = e.target;
			const percent = (target.value / target.max) * 100;
			target.style.background = `linear-gradient(to right, var(--color-ma) 0%, var(--color-ma) ${percent}%, var(--color-mb) ${percent}%, var(--color-mb) 100%)`;
		}}
	/>

	<fieldset class="mt-8 flex w-full flex-col gap-2 capitalize *:flex *:gap-4">
		<label for="includeUpper">
			<input class="hidden" type="checkbox" id="includeUpper" bind:checked={includeUpper} />
			{#if !includeUpper}
				<div
					class="hover:border-ma lborder-mt h-4 w-4 self-center rounded-sm border-2 hover:cursor-pointer"
				></div>
			{:else}
				<div class="border-ma bg-ma relative h-4 w-4 self-center rounded-sm border-2">
					<Check class="text-mb absolute top-[-1px] left-[-6px] h-4" />
				</div>
			{/if}
			<p>include uppercase letters</p>
		</label>

		<label for="includeLower">
			<input class="hidden" type="checkbox" id="includeLower" bind:checked={includeLower} />
			{#if !includeLower}
				<div
					class="hover:border-ma lborder-mt h-4 w-4 self-center rounded-sm border-2 hover:cursor-pointer"
				></div>
			{:else}
				<div class="border-ma bg-ma relative h-4 w-4 self-center rounded-sm border-2">
					<Check class="text-mb absolute top-[-1px] left-[-6px] h-4" />
				</div>
			{/if}
			<p>include lowercase letters</p>
		</label>

		<label for="includeNumbers">
			<input class="hidden" type="checkbox" id="includeNumbers" bind:checked={includeNumbers} />
			{#if !includeNumbers}
				<div
					class="hover:border-ma lborder-mt h-4 w-4 self-center rounded-sm border-2 hover:cursor-pointer"
				></div>
			{:else}
				<div
					class="border-ma bg-ma relative h-4 w-4 self-center rounded-sm border-2 hover:cursor-pointer"
				>
					<Check class="text-mb absolute top-[-1px] left-[-6px] h-4" />
				</div>
			{/if}
			<p>include numbers</p>
		</label>

		<label for="includeSymbols">
			<input class="hidden" type="checkbox" id="includeSymbols" bind:checked={includeSymbols} />
			{#if !includeSymbols}
				<div
					class="hover:border-ma lborder-mt h-4 w-4 self-center rounded-sm border-2 hover:cursor-pointer"
				></div>
			{:else}
				<div class="border-ma bg-ma relative h-4 w-4 self-center rounded-sm border-2">
					<Check class="text-mb absolute top-[-1px] left-[-6px] h-4" />
				</div>
			{/if}
			<p>include symbols</p>
		</label>
	</fieldset>

	<div class="bg-mb mt-6 flex w-full items-center justify-between px-4 py-4 uppercase">
		<p class="text-st">strength</p>
		<div class="flex items-center *:nth-[n+2]:m-[-4px]">
			<p class="mr-2">{strength}</p>

			{#if strength === 'weaker'}
				<RectangleVertical class="text-red-300" fill="var(--color-red-300)" />
				<RectangleVertical />
				<RectangleVertical />
				<RectangleVertical />
			{:else if strength === 'weak'}
				<RectangleVertical class="text-orange-300" fill="var(--color-orange-300)" />
				<RectangleVertical class="text-orange-300" fill="var(--color-orange-300)" />
				<RectangleVertical />
				<RectangleVertical />
			{:else if strength === 'medium'}
				<RectangleVertical class="text-yellow-300" fill="var(--color-yellow-300)" />
				<RectangleVertical class="text-yellow-300" fill="var(--color-yellow-300)" />
				<RectangleVertical class="text-yellow-300" fill="var(--color-yellow-300)" />
				<RectangleVertical />
			{:else if strength === 'strong'}
				<RectangleVertical class="text-ma" fill="var(--color-ma)" />
				<RectangleVertical class="text-ma" fill="var(--color-ma)" />
				<RectangleVertical class="text-ma" fill="var(--color-ma)" />
				<RectangleVertical class="text-ma" fill="var(--color-ma)" />
			{/if}
		</div>
	</div>

	<button
		class="hover:border-ma hover:text-ma bg-ma text-mb mt-4 flex w-full justify-center gap-2 border-2 border-transparent px-4 py-3 uppercase hover:cursor-pointer hover:bg-transparent"
		onclick={() =>
			(password = generatePassword(
				includeUpper,
				includeLower,
				includeNumbers,
				includeSymbols,
				length
			))}
	>
		<span>generate</span>
		<ArrowRight class="w-4" />
	</button>
</div>

<style lang="postcss">
	@import 'tailwindcss';

	input[type='range'] {
		@apply h-2 appearance-none bg-transparent;
		border-radius: 5px;
		background: linear-gradient(
			to right,
			var(--color-ma) 0%,
			var(--color-ma) 50%,
			var(--color-mb) 50%,
			var(--color-mb) 100%
		);
	}

	input[type='range']::-webkit-slider-thumb {
		@apply h-6 w-6 cursor-pointer appearance-none rounded-full bg-[var(--color-mt)];
		margin-top: -8px;
	}

	input[type='range']::-moz-range-thumb {
		@apply h-6 w-6 cursor-pointer rounded-full border-none bg-[var(--color-mt)];
	}

	input[type='range']::-ms-thumb {
		@apply h-6 w-6 cursor-pointer rounded-full border-none bg-[var(--color-mt)];
	}

	input[type='range']::-webkit-slider-runnable-track {
		@apply h-2 cursor-pointer appearance-none;
		border-radius: 5px;
	}

	input[type='range']::-moz-range-track {
		@apply h-2 cursor-pointer;
		border-radius: 5px;
	}
</style>
