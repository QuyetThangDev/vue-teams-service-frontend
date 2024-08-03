<template>
  <div>
    <Dialog>
      <DialogTrigger as-child>
        <Button variant="outline" class="flex flex-row items-center gap-2">
          <KeyRound class="w-3.5 h-3.5" />
          Change password
        </Button>
      </DialogTrigger>
      <DialogContent class="sm:max-w-[500px]">
        <DialogHeader>
          <DialogTitle class="flex flex-row items-center gap-1">
            <div class="flex items-center justify-center w-10 h-10 p-1 bg-red-100 rounded-full">
              <KeyRound class="w-4 h-4 text-red-600" />
            </div>
            Create new password
          </DialogTitle>
          <DialogDescription>
            Your new password must be different from previously used passwords.
          </DialogDescription>
        </DialogHeader>
        <Card class="max-w-sm md:min-w-[400px] md:min-h-[400px] mx-auto border-none">
          <CardHeader>
            <CardTitle class="text-2xl"> Login </CardTitle>
            <CardDescription> Enter your email below to login to your account </CardDescription>
          </CardHeader>
          <CardContent>
            <form @submit.prevent="onSubmit">
              <FormField
                v-slot="{ componentField }"
                name="oldPassword"
                class="flex flex-row items-center w-full"
              >
                <FormItem class="flex flex-row items-center w-full">
                  <FormLabel class="w-24 text-sm font-bold text-gray-500">New password</FormLabel>
                  <FormControl>
                    <Input type="text" placeholder="Your first name" v-bind="componentField" />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              </FormField>
              <FormField
                v-slot="{ componentField }"
                name="newPassword"
                class="flex flex-row items-center w-full"
              >
                <FormItem class="flex flex-row items-center w-full">
                  <FormLabel class="w-24 text-sm font-bold text-gray-500">New password</FormLabel>
                  <FormControl>
                    <Input type="text" placeholder="Your first name" v-bind="componentField" />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              </FormField>
              <FormField
                v-slot="{ componentField }"
                name="confirmPassword"
                class="flex flex-row items-center w-full"
              >
                <FormItem class="flex flex-row items-center w-full">
                  <FormLabel class="w-24 text-sm font-bold text-gray-500">First Name</FormLabel>
                  <FormControl>
                    <Input
                      rules="confirmed:@password"
                      type="text"
                      placeholder="Your first name"
                      v-bind="componentField"
                    />
                    <span v-if="errors.confirmPassword" class="text-red-500"
                      >Passwords do not match</span
                    >
                  </FormControl>
                  <FormMessage />
                </FormItem>
              </FormField>
              <CardFooter class="flex justify-end w-full px-0 py-4 border-t">
                <Button class="w-24" type="submit">
                  <!-- <span v-if="isUpdatePending">
                    <Loader2 class="w-4 h-4 animate-spin" />
                  </span> -->
                  <span>Submit</span>
                </Button>
              </CardFooter>
            </form>

            <div class="mt-4 text-sm text-center">
              Don't have an account?
              <RouterLink to="/auth/register" class="underline"> Sign up </RouterLink>
            </div>
          </CardContent>
        </Card>
        <!-- <DialogFooter>
          <Button type="submit" class="bg-primary" :disabled="isPending">Change password</Button>
        </DialogFooter> -->
      </DialogContent>
    </Dialog>
  </div>
</template>

<script setup lang="ts">
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger
} from '@/components/ui/dialog'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { Button } from '@/components/ui/button'
import { KeyRound } from 'lucide-vue-next'
import { toTypedSchema } from '@vee-validate/zod'
import { ref } from 'vue'
import { defineRule, useField, useForm } from 'vee-validate'
import * as z from 'zod'
import { changePassword } from '@/api/user'
import { useMutation } from '@tanstack/vue-query'
import { useToast } from '@/components/ui/toast'
import { useRouter } from 'vue-router'
import { login } from '@/api/auth'
import { useUserStore } from '@/stores/userStore'

import {
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage
} from '@/components/ui/form'

const { toast } = useToast()
const router = useRouter()

const { isPending, isError, error, isSuccess, mutate } = useMutation({
  mutationFn: (user: { old_password: string; new_password: string }) => {
    return changePassword(user)
  },
  onSuccess: (data: any) => {
    // useUserStore().setAccessToken(data.data.access_token)
    toast({
      title: 'Login successful',
      description: 'You have successfully logged in.'
    })
  },
  onError: () => {
    toast({
      title: 'Login failed',
      description: 'Invalid email or password.',
      variant: 'destructive'
    })
  }
})

const passwordRegex = /^(?=.*[\d])(?=.*[!@#$%^&*])[\w!@#$%^&*]{8,20}$/

const validationSchema = toTypedSchema(
  z.object({
    oldPassword: z.string(),
    newPassword: z.string(),
    confirmPassword: z.string()
  })
)

defineRule('confirmed', (value: string, [target]: string[]) => {
  if (value === target) {
    return true
  }
  return 'Passwords must match'
})

const { handleSubmit, errors } = useForm({
  initialValues: {
    oldPassword: '',
    newPassword: '',
    confirmPassword: ''
  },
  validationSchema: validationSchema
})

const onSubmit = handleSubmit((values) => {
  if (values.newPassword !== values.confirmPassword) {
    console.log(values)
    errors.value.confirmPassword = 'Passwords must match'
  }

  //   mutate({
  //     old_password: values.old_password,
  //     new_password: values.password
  //   })
})
</script>
