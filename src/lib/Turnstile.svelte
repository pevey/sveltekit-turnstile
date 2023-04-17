<script>
   import { browser } from '$app/environment'
   import { turnstileLoaded } from '$lib/stores'

   function turnstileCallback() {
		turnstileLoaded.set(true)
	}
</script>

<svelte:head>
   {#if browser && ($turnstileLoaded == false)}
      <script src="https://challenges.cloudflare.com/turnstile/v0/api.js?render=explicit" on:load={turnstileCallback} async></script>
   {/if}
</svelte:head>
   