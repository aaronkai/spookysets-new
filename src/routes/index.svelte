<script>
	import ChooseWorkout from '$lib/components/ChooseWorkout.svelte';
	import { user } from '$lib/stores/sessionStore';
	import { supabase } from '$lib/supabaseClient';
	let ctaVisible = true;
	//instantiate user store
	user.set(supabase.auth.user());

	//when there is a change to auth status, update the user store
	supabase.auth.onAuthStateChange((_, session) => {
		user.set(session.user);
	});

	function handleClick() {
		ctaVisible = !ctaVisible;
	}
</script>

<main class="grid gap-8 ">
	{#if !$user}
		<img class="h-20 w-20 m-auto" src="/ghost.svg" alt="ghost" />
		<header class="grid gap-4 justify-center text-center">
			<h1
				class="font-bungee text-5xl align-middle text-fuchsia-300 [text-shadow:0_0_10px_#d946ef,0_0_25px_#a21caf]"
			>
				Spooky Sets
			</h1>
			<h2 class="text-3xl text-gray-50 font-bungee ">
				Create a <a href="/pages/workout" class="text-fuchsia-300">Workout</a>
			</h2>
			<h2 class="text-3xl text-gray-50 font-bungee ">
				List Your <a href="pages/workout" class="text-fuchsia-300">Exercises</a>
			</h2>
			<h2 class="text-3xl text-gray-50 font-bungee ">
				Track Your <a href="/pages/workout" class="text-fuchsia-300">Sets</a>
			</h2>
		</header>
		<div class="grid grid-rows-2 gap-8 md:grid-cols-2">
			<a
				class="text-3xl text-gray-50 font-bungee bg-emerald-300 rounded py-2 px-4 text-slate-900 text-center"
				href="/pages/workout">Workout Now!</a
			>
			<a
				class="text-3xl text-gray-50 font-bungee bg-emerald-300 rounded py-2 px-4 text-slate-900 text-center"
				href="/pages/signUp">Log In</a
			>
		</div>
	{:else}
		<ChooseWorkout />
	{/if}
</main>
