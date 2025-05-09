<script setup lang="ts">
import { onMounted } from 'vue';
import { useRoute } from 'vue-router';
import { useCredentialsStore } from '@/stores/credentials.store';

const credentialsStore = useCredentialsStore();
const route = useRoute();

onMounted(async () => {
	try {
		const credentialId = route.query.credentialId as string;

		if (!credentialId) throw new Error('Missing credentialId in query');

		await credentialsStore.fetchAllCredentials();
		const cred = credentialsStore.getCredentialById(credentialId);

		if (!cred) throw new Error('Credential not found');

		const authUrl = await credentialsStore.oAuth2Authorize(cred);
		console.log('[OAuth URL]', authUrl);

		// await fetch('http://localhost:3002/your-custom-endpoint', {
		// 	method: 'POST',
		// 	headers: { 'Content-Type': 'application/json' },
		// 	body: JSON.stringify({ authUrl }),
		// });
	} catch (error) {
		console.error('OAuth Handler Error:', error);
	}
});
</script>

<template>
	<!-- Hidden view -->
	<div style="display: none">Authorizing...</div>
</template>
