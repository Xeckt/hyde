<script lang="ts">
	import { me } from '$lib/main';
	import { onMount } from 'svelte';
	import { createEventDispatcher } from 'svelte';
	import BranchButton from './BranchButton.svelte';
	import PullRequest from './PullRequest.svelte';

	let username: string = $state('');
	let profilePictureUrl = $state('');
	me.subscribe((me) => {
		profilePictureUrl = me.avatar_url;
	});

	const dispatch = createEventDispatcher();

	function settingsClickHandler() {
		// The settings menu display listens for this event to show
		dispatch('settingsopen');
	}

	onMount(() => {
		// Look for the username cookie and display it as the username
		const cookieJar = document.cookie.split('; ');
		for (const cookie of cookieJar) {
			const [key, value] = cookie.split('=');
			if (key === 'username') {
				username = value;
			}
		}
	});
</script>

<div class="top-bar">
	<BranchButton />
	<div class="right-items">
		<PullRequest />
		<div
			onclick={settingsClickHandler}
			onkeydown={settingsClickHandler}
			role="button"
			tabindex="0"
			class="settings"
			title="Settings"
		>
			<p>{username}</p>
			<!-- <svg xmlns="http://www.w3.org/2000/svg" height="2rem" viewBox="0 -960 960 960" width="2rem"
				><path
					d="m370-80-16-128q-13-5-24.5-12T307-235l-119 50L78-375l103-78q-1-7-1-13.5v-27q0-6.5 1-13.5L78-585l110-190 119 50q11-8 23-15t24-12l16-128h220l16 128q13 5 24.5 12t22.5 15l119-50 110 190-103 78q1 7 1 13.5v27q0 6.5-2 13.5l103 78-110 190-118-50q-11 8-23 15t-24 12L590-80H370Zm70-80h79l14-106q31-8 57.5-23.5T639-327l99 41 39-68-86-65q5-14 7-29.5t2-31.5q0-16-2-31.5t-7-29.5l86-65-39-68-99 42q-22-23-48.5-38.5T533-694l-13-106h-79l-14 106q-31 8-57.5 23.5T321-633l-99-41-39 68 86 64q-5 15-7 30t-2 32q0 16 2 31t7 30l-86 65 39 68 99-42q22 23 48.5 38.5T427-266l13 106Zm42-180q58 0 99-41t41-99q0-58-41-99t-99-41q-59 0-99.5 41T342-480q0 58 40.5 99t99.5 41Zm-2-140Z"
				/></svg
			> -->
			<img src={profilePictureUrl} alt="Profile" width="35rem" height="35rem" />
		</div>
	</div>
</div>

<style>
	.top-bar {
		display: flex;
		background-color: var(--background-2);
		height: 4rem;
		justify-content: space-between;
		align-items: center;
		width: 100%;
	}

	.top-bar * {
		margin-right: 0.6rem;
	}

	.right-items {
		display: flex;
		justify-content: flex-end;
		align-items: center;
		margin-left: auto; /* Pushes the right items to the right */
	}

	.settings {
		cursor: pointer;
		display: flex;
		flex-direction: row;
		align-items: center;
	}

	.settings p {
		color: var(--foreground-2);
		font-family: var(--font-family);
		font-size: larger;
		margin-right: 0.4rem;
		font-weight: 350;
	}

	.settings img {
		margin-left: 0.2rem;
		border-radius: 50%;
	}
</style>
