<script type="ts">
	import { exercises } from '$lib/stores/exerciseStore';
	import { alert } from '$lib/stores/alert';
	export let exercise;
	export let boopTimer;
	let counter = 0;

	function removeSet(exerciseId): void {
		let index = $exercises.findIndex((exercise) => exercise.id === exerciseId);
		$exercises[index].sets.pop();
		$exercises = $exercises;
		$alert = { text: 'Set Removed', isActive: true };
	}
	function toggleSet(set, setIndex, exerciseId): void {
		if (set) {
			counter += 1;
		} else {
			counter = 0;
		}
		let index = $exercises.findIndex((exercise) => exercise.id === exerciseId);
		$exercises[index].sets[setIndex] = !$exercises[index].sets[setIndex];
		//boopTimer;
	}

	function addSet(exerciseId): void {
		let index = $exercises.findIndex((exercise) => exercise.id === exerciseId);
		$exercises[index].sets.push(false);
		$exercises = $exercises;
	}
	function removeExercise(singleExercise): void {
		$exercises = $exercises.filter((exercise) => exercise.id !== singleExercise.id);
		$alert = { text: 'Exercise Removed', isActive: true };
	}

	function handleTimer(set) {
		if (!set) {
			boopTimer(true);
		} else {
			if (counter === 0) {
				boopTimer();
			} else {
			}
		}
	}
</script>

<!-- Exercise Title -->
<div class="flex mb-4">
	<input
		class="text-indigo-300 font-bungee text-2xl border-none w-full pl-0 bg-slate-900"
		type="text"
		bind:value={exercise.name}
	/>
</div>
<!-- Exercise Controls: add set, markSetDone, remove set, remove exercise -->
<div class="exerciseControls flex w-full">
	<button
		class=" border-2 border-slate-900 font-bungee flex-1 h-8 bg-slate-200 text-slate-900"
		title="Remove Set"
		on:click={() => removeSet(exercise.id)}>&minus;</button
	>
	{#each exercise.sets as set, setIndex}
		<button
			title="Mark Set Done"
			on:click={() => handleTimer(set)}
			on:click={() => toggleSet(set, setIndex, exercise.id)}
			class={set
				? 'border-2 border-slate-900 font-bungee flex-1 h-8 bg-emerald-400 text-slate-900'
				: 'border-2 border-slate-900 font-bungee flex-1 h-8 bg-slate-200 text-slate-900'}
			>{setIndex + 1}</button
		>
	{/each}
	<button
		class=" border-2 border-slate-900 font-bungee flex-1 h-8 bg-slate-200 text-slate-900"
		title="Add Set"
		on:click={() => addSet(exercise.id)}>&plus;</button
	>

	<button
		class="border-2 border-slate-900 font-bungee flex-1 h-8 bg-slate-200 text-slate-900"
		title="Remove Exercise from List"
		on:click={() => removeExercise(exercise)}>Remove</button
	>
</div>
