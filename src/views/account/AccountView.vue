<script setup lang="ts">
import { toTypedSchema } from '@vee-validate/zod'
import { ref, computed, onMounted, watchEffect, watch } from 'vue'
import { useMutation, useQuery } from '@tanstack/vue-query'
import { useField, useForm } from 'vee-validate'
import * as z from 'zod'

import { Card, CardContent, CardFooter, CardTitle } from '@/components/ui/card'
import { Label } from '@/components/ui/label'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Avatar, AvatarImage } from '@/components/ui/avatar'
import TeamSwitcher from '@/views/dashboard/components/TeamSwitcher.vue'

import { useToast } from '@/components/ui/toast'
import { useUserStore } from '@/stores/userStore'
import { getCurrentUser } from '@/api/user'
import { updateUser } from '@/api/auth'
import { storeToRefs } from 'pinia'
import type { UserInfo } from '@/types/user'
import LoadingSpinner from '@/components/LoadingSpinner.vue'
import {
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage
} from '@/components/ui/form'
import { CircleUser, User } from 'lucide-vue-next'
import DefaultTeam from './components/DefaultTeam.vue'
import AccountInfo from './components/AccountInfo.vue'

const formSchema = toTypedSchema(
  z.object({
    firstName: z.string().min(1, 'First name is required'),
    lastName: z.string().min(1, 'Last name is required')
  })
)

const {
  isLoading,
  isError,
  data: currentUser,
  error
} = useQuery({
  queryKey: ['user'],
  queryFn: getCurrentUser
})

const form = useForm({
  initialValues: {
    firstName: '',
    lastName: ''
  },
  validationSchema: formSchema
})

watch(currentUser, (newVal) => {
  if (newVal) {
    form.setValues({
      firstName: newVal.data.first_name,
      lastName: newVal.data.last_name
    })
  }
})

const onSubmit = form.handleSubmit((values) => {
  mutate({
    first_name: values.firstName,
    last_name: values.lastName
  })
})

const {
  isPending: isUpdatePending,
  isError: isUpdateError,
  error: updateError,
  isSuccess: isUpdateSuccess,
  mutate
} = useMutation({
  mutationFn: (user: { first_name: string; last_name: string }) => {
    console.log(user)
    return updateUser(user)
  }
})
</script>

<template>
  <div class="flex flex-col w-full min-h-screen">
    <main
      class="flex min-h-[calc(100vh_-_theme(spacing.16))] flex-1 flex-col gap-4 bg-muted/40 p-4 md:gap-8"
    >
      <div class="grid w-full max-w-6xl gap-2 mx-auto">
        <h1 class="text-3xl font-semibold">Settings</h1>
      </div>
      <div
        class="mx-auto grid w-full max-w-6xl items-start gap-6 md:grid-cols-[180px_1fr] lg:grid-cols-[250px_1fr]"
      >
        <nav class="grid gap-2 text-sm text-muted-foreground">
          <a href="#" class="px-2 py-3 font-semibold text-white rounded-md bg-primary"> General </a>
          <a href="#" class="px-2 py-3 duration-300 rounded-md hover:bg-gray-100"> Security </a>
          <a href="#" class="px-2 py-3 duration-300 rounded-md hover:bg-gray-100"> Integrations </a>
          <a href="#" class="px-2 py-3 duration-300 rounded-md hover:bg-gray-100"> Support </a>
          <a href="#" class="px-2 py-3 duration-300 rounded-md hover:bg-gray-100">
            Organizations
          </a>
          <a href="#" class="px-2 py-3 duration-300 rounded-md hover:bg-gray-100"> Advanced </a>
        </nav>
        <div class="grid gap-6">
          <AccountInfo />
          <DefaultTeam />
        </div>
      </div>
    </main>
  </div>
</template>
