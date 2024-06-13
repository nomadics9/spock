# Spock - Svelte + Pocketbase

My boilerplate for
<img src="./git_assets/spock.jpg" align="center" height="64" width="48" ></a>

    1.  Sveltekit + Pocketbase DB
    2.  Tailwindcss
    3.  SkeletonUI

# Auth Ready

### Snippet from login.svelete component - populating name and avatar from Google provider

```typescript
<script lang="ts">
	import { currentUser, pb } from '$lib/pocketbase';

	async function login() {
		try {
			const authData = await pb.collection('users').authWithOAuth2({ provider: 'google' });
			const meta = authData.meta;
            console.log(meta)

			if (meta?.isNew) {
				const formData = new FormData();

				const response = await fetch(meta.avatarUrl);

				if (response.ok) {
					const file = await response.blob();
					formData.append('avatar', file);
				}

				formData.append('name', meta.name);

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
```

### currentUser as a store to use example `$currentUser.avatar`

```ts
import { env } from '$env/dynamic/public';
import PocketBase from 'pocketbase';
import { writable } from 'svelte/store';

export const pb = new PocketBase(env.PUBLIC_DB_URL);

export const currentUser = writable(pb.authStore.model);

pb.authStore.onChange((auth) => {
	//console.log('auth changed ', auth)
	currentUser.set(pb.authStore.model);
});
```

### use .env for pocketbase URL

```sh
PUBLIC_DB_URL=http://localhost:8090
```

### Usage:

1. run DB in same dir

```bash
pocketbase serve
```

2. dev mode
1.

```bash
bun install
```

or

```bash
npm run install
```

2.

```bash
bun dev
```

or

```bash
npm run dev
```
