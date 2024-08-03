<template>
  <span v-if="isPending">
    <LoadingSpinner />
  </span>
  <span v-else-if="isError">Error: {{ error!.message }}</span>
  <!-- We can assume by this point that `isSuccess === true` -->
  <ul v-else-if="currentUser">
    <pre>
      <!-- <LoadingSpinner /> -->
      {{ currentUser.data.first_name }} {{ currentUser.data.last_name }}
    </pre>
  </ul>
</template>

<script setup lang="ts">
import { useQuery } from '@tanstack/vue-query'
import type { UserInfo } from '@/types/user'
import { getCurrentUser } from '@/api/user'
import LoadingSpinner from '@/components/LoadingSpinner.vue'

const {
  isPending,
  isError,
  data: currentUser,
  error
} = useQuery({
  queryKey: ['user'],
  queryFn: getCurrentUser
})
</script>
