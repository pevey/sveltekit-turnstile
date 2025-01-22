<script lang="ts">
	import { browser } from '$app/environment'
	import { createEventDispatcher } from 'svelte'
	import { onMount } from 'svelte'
	import { turnstileLoaded } from './stores.js'

	export let siteKey = undefined
	export let fieldName = 'token'
	export let action = undefined
	export let cData = undefined
	export let retryInterval = 8000
	export let retry = 'auto'
	export let theme = 'auto'
	export let size = 'normal'
	export let forms = true
	export let tabIndex = 0

	const dispatch = createEventDispatcher()

	let mounted = false
	onMount(() => {
		mounted = true
		return () => {
			mounted = false
		}
	})

	function turnstileCallback() {
		turnstileLoaded.set(true)
	}

	function error() {
		dispatch('turnstile-error', {})
	}

	function expired() {
		dispatch('turnstile-expired', {})
	}

	function timeout() {
		dispatch('turnstile-timeout', {})
	}

	function callback(token: string) {
		dispatch('turnstile-callback', { token })
	}

	const turnstile = (node: HTMLElement) => {
		try {
			const id = window.turnstile.render(node, {
				sitekey: siteKey,
				'response-field-name': fieldName,
				'timeout-callback': timeout,
				'expired-callback': expired,
				'error-callback': error,
				callback,
				'retry-interval': retryInterval,
				'response-field': forms,
				tabindex: tabIndex,
				action,
				retry,
				theme,
				cData,
				size
			})
			return {
				destroy: () => {
					window.turnstile.remove(id)
				}
			}
		} catch (error) {
			console.error(error)
		}
	}
</script>

<svelte:head>
	{#if browser && $turnstileLoaded == false}
		<script src="https://challenges.cloudflare.com/turnstile/v0/api.js?render=explicit" on:load={turnstileCallback} async></script>
	{/if}
</svelte:head>

{#if mounted && $turnstileLoaded}
	{#key $$props}
		<div use:turnstile></div>
	{/key}
{/if}
