<script>
	import { supabase } from '$lib/supabaseClient';
	import ErrorMessage from '$lib/components/ErrorMessage.svelte';
	import Toast from './Toast.svelte';
	import { alert } from '$lib/stores/alert';
	import { user } from '$lib/stores/sessionStore';
	import { goto } from '$app/navigation';

	let signIn = false;
	let email;
	let password;
	let errorMessage;
	let loading = false;
	let isMember = localStorage.getItem('spookysetsMember') ? (signIn = true) : false;

	//when there is a change to auth status, update the user store
	supabase.auth.onAuthStateChange((_, session) => {
		user.set(session.user);
	});

	function toggle() {
		signIn = !signIn;
	}

	async function handleSubmit() {
		if (signIn) {
			try {
				loading = true;
				const { user, session, error } = await supabase.auth.signIn({
					email,
					password
				});
				if (error) {
					throw error;
				} else {
					$alert = { text: 'Success: You have logged in!', isActive: true };
					localStorage.setItem('spookysetsMember', 'true');
					goto('/');
				}
			} catch (error) {
				errorMessage = error.error_description || error.message;
			} finally {
				loading = false;
			}
		} else {
			try {
				loading = true;
				const { user, session, error } = await supabase.auth.signUp({
					email,
					password
				});

				if (error) {
					throw error;
				} else {
					$alert = { text: 'Check your email for a verification link.!', isActive: true };
					signIn = true;
				}
			} catch (error) {
				errorMessage = error.error_description || error.message;
			} finally {
				loading = false;
			}
		}
	}
</script>

<Toast />
<main class=" grid gap-6 justify-stretch">
	{#if !$user}
		{#if signIn}
			<header>
				<h1 class="text-5xl font-bungee text-indigo-300">Sign In</h1>
			</header>
		{:else}
			<header>
				<h1 class="font-bungee text-5xl text-indigo-300">Sign Up Now</h1>
			</header>
		{/if}
		<form class="grid gap-4 py-4" id="form" on:submit|preventDefault={handleSubmit}>
			<div>
				<label class="font-bungee text-xl text-indigo-300" for="email">Email: </label>
				<input
					id="email"
					type="email"
					placeholder="Your email"
					autocomplete="email"
					bind:value={email}
					class="text-xl mt-2 font-bungee text-indigo-300 grid items-baseline mb-2 border-none p-2 w-full rounded"
				/>
			</div>
			<div>
				<label class="font-bungee text-xl text-indigo-300" for="password">Password: </label>
				<input
					type="password"
					placeholder="Your password"
					autocomplete="current-password"
					bind:value={password}
					class="text-xl mt-2 font-bungee text-indigo-300 grid items-baseline mb-2 border-none p-2 w-full rounded"
				/>
			</div>
			{#if errorMessage}
				<ErrorMessage error={errorMessage} />
			{/if}

			{#if signIn}
				<input
					class="mt-4 py-4 block bg-emerald-300 p-4 text-slate-900 w-full text-center rounded font-bungee no-underline text-2xl hover:bg-indigo-300 border-none p-2 w-full rounded"
					type="submit"
					value={loading ? 'Loading' : 'Sign In'}
					disabled={loading}
				/>
			{:else}
				<input
					class="mt-4 py-4 block bg-emerald-300 text-slate-900 w-full text-center rounded font-bungee no-underline text-2xl hover:bg-indigo-300 border-none p-2 w-full rounded"
					type="submit"
					value={loading ? 'Loading' : 'Sign Up'}
					disabled={loading}
				/>
			{/if}
		</form>
		{#if signIn}
			<button class="text-indigo-300 bg-none border-none text-2xl font-bungee" on:click={toggle}
				>Do you need to sign up?</button
			>
		{:else}
			<button class="text-indigo-300 bg-none border-none text-2xl font-bungee" on:click={toggle}
				>Already have an account?</button
			>
		{/if}
	{:else}
		<div class="grid gap-4">
			<h1 class="text-indigo-300">You are signed in!</h1>
			<a class="text-indigo-300" href="/">Go pick a workout</a>
		</div>
	{/if}
</main>
