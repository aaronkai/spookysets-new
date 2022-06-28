<script type="ts">
	import Exercise from '$lib/components/Exercise.svelte';
	import { exercises, title, id } from '$lib/stores/exerciseStore';
	import Toast from '$lib/components/Toast.svelte';
	import { alert } from '$lib/stores/alert';
	import { crossfade, fly } from 'svelte/transition';
	import { flip } from 'svelte/animate';

	import Timer from '$lib/components/Timer.svelte';
	import { user } from '$lib/stores/sessionStore';
	import { supabase } from '$lib/supabaseClient';

	let loading: boolean = false;
	let boopTimer;

	function addExercise(): void {
		let newId: number = $exercises.at(-1).id + 1 || 0;
		$exercises.push({ id: newId, name: 'placeholder', sets: [false, false, false] });
		$exercises = $exercises;
		$alert = { text: 'Exercise Added to List', isActive: true };
	}

	async function saveWorkout(): Promise<void> {
		try {
			loading = true;
			const user = supabase.auth.user();

			const upserts = {
				id: $id,
				user_id: user.id,
				name: $title,
				exercises: $exercises
			};
			const { data, error } = await supabase.from('workouts').insert(upserts);
			if (error) {
				throw error;
			} else {
				$alert = { text: 'Workout Saved', isActive: true };
			}
		} catch (error) {
			console.log(error);
			$alert = { text: 'Something went wrong!', isActive: true };
		} finally {
			loading = false;
		}
	}

	function exerciseDone(exerciseIndex: number): boolean {
		if (
			[...new Set($exercises[exerciseIndex].sets)][0] === true &&
			[...new Set($exercises[exerciseIndex].sets)].length === 1
		) {
			return true;
		} else {
			return false;
		}
	}

	const [send, receive] = crossfade({
		fallback: fly
	});
</script>

<svelte:head>
	<title>Spooky Sets: Workout</title>
</svelte:head>

<main class="grid px-4 gap-8">
	<Toast />
	<!-- Workout Title -->
	<h1
		class="text-indigo-300 bg-slate-900 font-bungee text-4xl border-0 w-full text-center mt-8"
		contenteditable="true"
		bind:innerHTML={$title}
		type="text"
	/>
	<!-- Control Panel -->
	<section class="grid gap-4">
		<div class="grid grid-cols-3 justify-center grid gap-1 mb-4">
			<button
				class="bg-emerald-300  flex justify-center items-center flex-col text-xl text-slate-900 cursor-pointer  font-bungee hover:opacity-75 disabled:bg-emerald-200"
				on:click={addExercise}
				title="Add Exercise"
				>Add Exercise
				<span class="text-5xl">&plus;</span>
			</button>
			<Timer bind:boopTimer />
			{#if $user}
				<button
					on:click={saveWorkout}
					title="Save"
					class="bg-emerald-400  flex justify-center items-center flex-col text-xl text-slate-900 cursor-pointer hover:opacity-75 font-bungee disabled:bg-emerald-200"
					disabled={loading}
				>
					Save
					<img
						class="w-1/2 h-1/2 text-slate-900 cursor-pointer font-bungee"
						src="/save.svg"
						alt="save icon"
					/>
				</button>
			{:else}
				<a
					class="mx-auto h-28 w-full text-slate-900 bg-emerald-300 font-bungee text-center flex justify-center items-center flex-col p-8"
					href="/pages/signUp">Log In to Save</a
				>
			{/if}
		</div>
		<!-- Exercise List -->
		{#each $exercises.filter((_, index) => !exerciseDone(index)) as exercise (exercise.id)}
			<div
				class="exercise"
				animate:flip
				in:receive={{ key: exercise.id }}
				out:send={{ key: exercise.id, duration: 2 }}
			>
				<Exercise {exercise} {boopTimer} />
			</div>
		{/each}
	</section>
	<!-- Finished Exercise List -->
	<section class="grid gap-4">
		{#each $exercises.filter((exercise, index) => exerciseDone(index)) as exercise (exercise.id)}
			<div
				animate:flip
				in:receive={{ key: exercise.id }}
				out:send={{ key: exercise.id }}
				class="opacity-50"
			>
				<Exercise {exercise} {boopTimer} />
			</div>
		{/each}
	</section>
</main>
