<script setup lang="ts">
import { authClient } from '~/lib/auth-client'

const session = authClient.useSession()

const router = useRouter()

async function handleSignOut() {
  await authClient.signOut({
    fetchOptions: {
      onSuccess: () => {
        router.push('/auth')
      },
    },
  })
}
</script>

<template>
  <div>
    <div v-if="session.isPending">
      <Spinner />
    </div>
    <div v-else>
      <pre>{{ session.data }}</pre>
      <Button
        v-if="session.data"
        @click="handleSignOut()"
      >
        Sign out
      </Button>
    </div>
  </div>
</template>
