<script>
	import { fly } from 'svelte/transition';
	import { alert } from '$lib/stores/alert';

	function closeAlert() {
		alert.set({
			text: $alert.text,
			isActive: false
		});
	}

	alert.subscribe((value) => {
		if (value.isActive) {
			setTimeout(closeAlert, 2000);
		}
	});
</script>

{#if $alert.isActive}
	<aside class="bg-emerald-400 fixed bottom-3 left-0 right-0 p-4 z-50" transition:fly={{ y: 100 }}>
		<p class="text-slate-900 font-bungee text-center m-0 max-w-full">{$alert.text}</p>
	</aside>
{/if}
