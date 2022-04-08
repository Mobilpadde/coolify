<script lang="ts">
	export let applications: Array<Application>;
	import Application from './_Application.svelte';
</script>

<div class="flex space-x-1 p-6 font-bold">
	<div class="mr-4 text-2xl">Logs</div>
</div>
<div class="flex flex-wrap justify-center">
	{#if !applications || applications.length === 0}
		<div class="flex-col">
			<div class="text-center text-xl font-bold">No application or build logs found</div>
		</div>
	{:else}
		{#each applications as application}
			<Application {application} />
		{/each}
	{/if}
</div>
