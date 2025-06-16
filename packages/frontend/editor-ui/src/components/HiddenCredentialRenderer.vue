<script setup lang="ts">
import { onMounted } from 'vue';
import { useRoute } from 'vue-router';
import { useCredentialsStore } from '@/stores/credentials.store';

const credentialsStore = useCredentialsStore();
const route = useRoute();

onMounted(async () => {
	try {
		const credentialId = route.query.credentialId as string;
		const requestId = route.query.requestId as string;
		const callback = route.query.callback as string;

		if (!credentialId) throw new Error('Missing credentialId in query');

		await credentialsStore.fetchAllCredentials();
		const cred = credentialsStore.getCredentialById(credentialId);

		if (!cred) throw new Error('Credential not found');

		const authUrl = await credentialsStore.oAuth2Authorize(cred);

		await fetch(`${callback}/oauth_url_callback`, {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify({ oauth_url: authUrl, requestId: requestId }),
		});
	} catch (error) {
		console.error('OAuth Handler Error:', error);
	}
});
</script>

<template>
	<!-- Hidden view -->
	<div style="display: none">Authorizing...</div>
</template>
