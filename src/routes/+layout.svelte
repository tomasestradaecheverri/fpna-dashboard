<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>

<svelte:head>
	<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Mono:wght@400;500&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
</svelte:head>

{@render children()}
