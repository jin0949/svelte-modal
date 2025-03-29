<script lang="ts">
	import Modal from '$lib/components/Modal.svelte';
	import Toast from '$lib/components/Toast.svelte';
	import '../app.css';

	let { children } = $props();
</script>

<Modal />
<Toast />
{@render children()}
