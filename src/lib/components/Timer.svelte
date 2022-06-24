<script type="ts">
	type Timer = ReturnType<typeof setInterval>;

	let seconds: number = 0;
	let timerRunning: boolean = false;
	let timer: Timer;

	export function boopTimer(resetTimer: boolean = false): Timer {
		if (!timerRunning) {
			seconds = 0;
			timerRunning = true;
			timer = setInterval(() => {
				seconds = seconds + 1;
			}, 1000);
		} else {
			// if timer function call comes from a rep button, reset timer, don't stop it.
			if (resetTimer) {
				seconds = 0;
				clearInterval(timer);
				timer = setInterval(() => {
					seconds = seconds + 1;
				}, 1000);
			} else {
				timerRunning = false;
				seconds = 0;
				clearInterval(timer);
			}
		}
		return timer;
	}
</script>

<button
	title="Start/Stop Timer"
	on:click={() => boopTimer(false)}
	class={timerRunning
		? 'bg-red-400 mx-auto w-full flex justify-center items-center flex-col hover:opacity-75'
		: 'stopped bg-emerald-400 mx-auto w-full flex justify-center items-center flex-col hover:opacity-75'}
>
	<h1 class="text-2xl text-slate-900 font-bungeetext-2xl text-slate-900 font-bungee ">Timer</h1>
	<p class="text-5xl text-slate-900 font-bungee">{seconds}</p>
</button>
