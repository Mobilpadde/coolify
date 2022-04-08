<script context="module" lang="ts">
	import type { Load } from '@sveltejs/kit';
	function checkConfiguration(application): string {
		let configurationPhase = null;
		if (!application.gitSourceId) {
			configurationPhase = 'source';
		} else if (!application.repository && !application.branch) {
			configurationPhase = 'repository';
		} else if (!application.destinationDockerId) {
			configurationPhase = 'destination';
		} else if (!application.buildPack) {
			configurationPhase = 'buildpack';
		}
		return configurationPhase;
	}
	export const load: Load = async ({ fetch, url, params }) => {
		const endpoint = `/applications/${params.id}.json`;
		const res = await fetch(endpoint);
		if (res.ok) {
			let { application, isRunning, appId, githubToken, gitlabToken } = await res.json();
			if (!application || Object.entries(application).length === 0) {
				return {
					status: 302,
					redirect: '/applications'
				};
			}
			if (application.gitSource?.githubAppId && !githubToken) {
				const response = await fetch(`/applications/${params.id}/configuration/githubToken.json`);
				if (response.ok) {
					const { token } = await response.json();
					githubToken = token;
				}
			}
			const configurationPhase = checkConfiguration(application);
			if (
				configurationPhase &&
				url.pathname !== `/applications/${params.id}/configuration/${configurationPhase}`
			) {
				return {
					status: 302,
					redirect: `/applications/${params.id}/configuration/${configurationPhase}`
				};
			}

			return {
				stuff: {
					application
				}
			};
		}

		return {
			status: 302,
			redirect: '/applications'
		};
	};
</script>

<script lang="ts">
	import Loading from '$lib/components/Loading.svelte';

	let loading = false;
</script>

<nav class="nav-side">
	{#if loading}
		<Loading fullscreen cover />
	{/if}
</nav>
<slot />
