<script lang="ts">
	import { currentUser, pb } from '$lib/pocketbase';

	async function login() {
		try {
			const authData = await pb.collection('users').authWithOAuth2({ provider: 'google' });
			const meta = authData.meta;
            console.log(meta)

			if (meta?.isNew || !currentUser.avatar) {
				const formData = new FormData();

				const response = await fetch(meta?.avatarUrl);

				if (response.ok) {
					const file = await response.blob();
					formData.append('avatar', file);
				}

				formData.append('name', meta?.name);

				await pb.collection('users').update(authData.record.id, formData);
			}
		} catch (err) {
			console.error(err);
		}
	}

	function signOut() {
		pb.authStore.clear();
	}
</script>

{#if $currentUser}
	<h1>Signed in as {$currentUser.name}</h1>
    <img src={pb.files.getUrl($currentUser, $currentUser.avatar)} alt={$currentUser.name}  class="mx-auto rounded-xl shadow-lg" />

	<button on:click={signOut} class="btn variant-filled-warning">Log out</button>
{:else}
	<h1>Please Login</h1>
	<button class="btn variant-outline-primary" on:click={login}> Login using Google </button>
{/if}
