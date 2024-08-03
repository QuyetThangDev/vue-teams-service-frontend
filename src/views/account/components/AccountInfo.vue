<template>
  <div>
    <Card class="border border-gray-300 shadow-none">
      <CardHeader class="flex flex-row items-center gap-4 p-6 border-b">
        <!-- <Avatar class="w-16 h-16 p-2 bg-gray-200">
          <User class="text-gray-500 w-14 h-14" />
        </Avatar> -->
        <div class="flex flex-row items-center justify-between w-full gap-1 py-2">
          <CardTitle class="text-gray-500 shadow-none"> Account Information </CardTitle>
          <ChangePasswordDialog />
        </div>
      </CardHeader>
      <CardContent class="flex flex-col gap-6 mt-6">
        <span v-if="isLoading">
          <LoadingSpinner />
        </span>
        <span v-else-if="isError">Error: {{ error!.message }}</span>
        <form
          v-else-if="currentUser"
          @submit.prevent="onSubmit"
          class="flex flex-col items-center justify-center gap-4"
        >
          <FormField name="email" class="flex flex-row items-center w-full">
            <FormItem class="flex flex-row items-center w-full">
              <FormLabel class="w-24 text-sm font-bold text-gray-500">Email</FormLabel>
              <div class="flex flex-col w-full">
                <FormControl>
                  <Input
                    type="text"
                    disabled
                    placeholder="Your email"
                    v-model="currentUser!.data.email"
                  />
                </FormControl>
                <FormMessage />
              </div>
            </FormItem>
          </FormField>
          <FormField
            v-slot="{ componentField }"
            name="firstName"
            class="flex flex-row items-center w-full"
          >
            <FormItem class="flex flex-row items-center w-full">
              <FormLabel class="w-24 text-sm font-bold text-gray-500">First Name</FormLabel>
              <div class="flex flex-col w-full gap-1">
                <FormControl>
                  <Input type="text" placeholder="Your first name" v-bind="componentField" />
                </FormControl>
                <FormMessage />
              </div>
            </FormItem>
          </FormField>
          <FormField
            v-slot="{ componentField }"
            name="lastName"
            class="flex flex-row items-center w-full"
          >
            <FormItem class="flex flex-row items-center w-full">
              <FormLabel class="w-24 text-sm font-bold text-gray-500">Last Name</FormLabel>
              <div class="flex flex-col w-full gap-1">
                <FormControl>
                  <Input type="text" placeholder="Your last name" v-bind="componentField" />
                </FormControl>
                <FormMessage />
              </div>
            </FormItem>
          </FormField>
          <CardFooter class="flex justify-end w-full px-0 py-4 border-t">
            <Button class="w-24" type="submit">
              <span v-if="isUpdatePending">
                <Loader2 class="w-4 h-4 animate-spin" />
              </span>
              <span v-else>Submit</span>
            </Button>
          </CardFooter>
        </form>
      </CardContent>
    </Card>
  </div>
</template>

<script setup lang="ts">
import { toTypedSchema } from '@vee-validate/zod'
import { watch } from 'vue'
import { useMutation, useQuery } from '@tanstack/vue-query'
import { useForm } from 'vee-validate'
import * as z from 'zod'

import { Card, CardContent, CardFooter, CardTitle } from '@/components/ui/card'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'

import { useToast } from '@/components/ui/toast'
import { getCurrentUser } from '@/api/user'
import { updateUser } from '@/api/auth'
import LoadingSpinner from '@/components/LoadingSpinner.vue'
import { FormControl, FormField, FormItem, FormLabel, FormMessage } from '@/components/ui/form'
import { KeyRound } from 'lucide-vue-next'
import ChangePasswordDialog from './ChangePasswordDialog.vue'

const { toast } = useToast()

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
  },
  onSuccess: (data: any) => {
    toast({
      title: 'Update successful'
    })
  },
  onError: (error: any) => {
    toast({
      title: 'Update failed',
      description: error.message
    })
  }
})
</script>
