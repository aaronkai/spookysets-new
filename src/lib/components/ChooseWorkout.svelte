<script>
	import { supabase } from '$lib/supabaseClient';
	import { exercises, id, title } from '$lib/stores/exerciseStore';

	let loading = false;
	let allWorkouts = [];

	//create a new workout
	async function createNewWorkout() {
		try {
			loading = true;
			const user = supabase.auth.user();

			const inserts = {
				user_id: user.id,
				name: 'example 1',
				exercises: ['pushup', 'situp']
			};
			const { data, error } = await supabase.from('workouts').insert(inserts);
		} catch (error) {
			console.error(error);
		} finally {
			loading = false;
		}
	}
	//Read existing workout/workouts
	async function getAllWorkouts() {
		try {
			loading = true;
			const user = supabase.auth.user();
			const { data, error } = await supabase.from('workouts').select();
			allWorkouts = data;
		} catch (error) {
			console.error(error);
		} finally {
			loading = false;
		}
	}

	function setWorkout(workout) {
		$id = workout.id;
		$title = workout.name;
		$exercises = workout.exercises;
	}

	async function deleteWorkout(workout) {
		try {
			loading = true;
			const user = supabase.auth.user();
			const { data, error } = await supabase.from('workouts').delete().match({ id: workout.id });
			getAllWorkouts();
		} catch (error) {
			console.error(error);
		} finally {
			loading = false;
		}
	}
</script>

<section class="p-4 grid gap-4" use:getAllWorkouts>
	<header class="mb-4 grid grid-cols-[auto_1fr]">
		<img class="h-12 w-12 m-auto" src="/ghost.svg" alt="ghost" />
		<h1 class="text-center font-bungee text-5xl text-indigo-300">Your Workouts</h1>
	</header>
	<table class="w-full mt-4 bg-slate-900 border-2 border-indigo-300 rounded  border-separate">
		<caption class=" text-3xl font-bungee text-gray-50 pb-4 text-center">Choose a Workout</caption>
		<tr class="w-full">
			<th
				class="border-t-2 border-l-2 py-4 px-2 text-center border-indigo-300 text-indigo-300 text-xl border-t-0 font-bungee"
				scope="col">Workout Name</th
			>
			<th
				class="border-t-2 border-l-2 py-4 px-2 text-center border-indigo-300 text-indigo-300 text-xl border-t-0 font-bungee"
				scope="col">Delete?</th
			>
		</tr>
		<tr class="w-full">
			<td class="border-t-2 border-l-2 py-4 px-2 text-center border-indigo-300">
				<a class="text-gray-50 font-bungee" href="/pages/workout">Recommended Routine</a>
			</td>
			<td class="border-t-2 border-l-2 py-4 px-2 text-center border-indigo-300">
				<p class="text-gray-50 font-bungee">n/a</p>
			</td>
		</tr>
		{#each allWorkouts as workout}
			<tr class="w-full">
				<td class="border-t-2 border-l-2 py-4 px-2 text-center border-indigo-300">
					<a
						title="Go To Workout"
						class="text-gray-50 font-bungee"
						href="/pages/workout"
						on:click={setWorkout(workout)}>{workout.name}</a
					>
				</td>

				<td class="border-t-2 border-l-2 py-4 px-2 text-center border-indigo-300">
					<button
						class="bg-none w-full h-full border-none cursor-pointer flex justify-center"
						href="#"
					>
						<img
							class="w-6 h-6"
							src="/trash.svg"
							alt="trashcan icon"
							title="Delete Workout"
							on:click={deleteWorkout(workout)}
						/>
					</button>
				</td>
			</tr>
		{/each}
	</table>
</section>

<style>
	td:first-child,
	th:first-child {
		@apply border-l-0;
	}
</style>
