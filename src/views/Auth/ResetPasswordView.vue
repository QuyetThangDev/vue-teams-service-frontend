<script setup lang="ts">
import { CircleCheckBig, LoaderCircle } from 'lucide-vue-next'
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { useToast } from '@/components/ui/toast'

import { useField, useForm } from 'vee-validate'
import { toTypedSchema } from '@vee-validate/zod'
import * as z from 'zod'
import { useMutation } from '@tanstack/vue-query'
import { ref } from 'vue'
import { useRouteQuery } from '@vueuse/router'

import { resetPassword } from '@/api/auth'
import { useRouter } from 'vue-router'

const { toast } = useToast()
const router = useRouter()
const passwordReset = ref(false)
const token = useRouteQuery<string | null>('token', null)
if (token.value === null) {
  router.replace({ name: 'Login' })
}

const passwordRegex = /^(?=.*[\d])(?=.*[!@#$%^&*])[\w!@#$%^&*]{8,20}$/

const { isPending, isError, error, isSuccess, mutate } = useMutation({
  mutationFn: resetPassword,
  onSuccess: () => {
    toast({
      title: 'Reset password successful'
    })
    passwordReset.value = true
  },
  onError: () => {
    console.log('Error')
  }
})

const validationSchema = toTypedSchema(
  z.object({
    password: z
      .string()
      .regex(
        passwordRegex,
        'Password must be 8-20 characters, include a number and a special character'
      )
  })
)

const { handleSubmit, errors } = useForm({
  validationSchema: validationSchema
})

const { value: password } = useField<string>('password')

const onSubmit = handleSubmit((values) => {
  mutate({
    new_password: values.password,
    token: token.value!
  })
})
</script>

<template>
  <div class="flex justify-center w-full min-w-[600px] min-h-screen">
    <img
      src="@/assets/login-background.png"
      alt="login image"
      class="absolute inset-0 object-cover w-full h-full -z-10"
    />
    <div class="flex items-center justify-center">
      <form @submit.prevent="onSubmit">
        <Card v-if="!passwordReset" class="max-w-sm md:min-w-[400px] mx-auto border-none">
          <CardHeader>
            <CardTitle class="text-2xl"> Create new password </CardTitle>
            <CardDescription>
              Your new password must be different from previously used passwords.
            </CardDescription>
          </CardHeader>
          <CardContent>
            <div class="grid gap-4">
              <div class="grid gap-2">
                <Label for="password">Password</Label>
                <Input
                  v-model="password"
                  id="password"
                  type="password"
                  placeholder="********"
                  required
                />
                <span v-if="errors.password" class="text-sm text-red-600">{{
                  errors.password
                }}</span>
              </div>
              <div class="grid gap-2">
                <Label for="password">Confirm password</Label>
                <Input
                  v-model="password"
                  id="confirm_password"
                  type="password"
                  placeholder="********"
                  required
                />
                <span v-if="errors.password" class="text-sm text-red-600">{{
                  errors.password
                }}</span>
              </div>
              <Button class="w-full" type="submit" :disabled="isPending">
                <LoaderCircle v-if="isPending" class="w-4 h-4 mr-2 animate-spin" />
                Reset Password
              </Button>
            </div>
          </CardContent>
        </Card>
        <Card v-else class="max-w-sm md:min-w-[500px] mx-auto border-none">
          <CardHeader class="items-center text-center">
            <CircleCheckBig class="w-8 h-8 text-green-600" />
            <CardTitle class="text-2xl text-center"> Email sent </CardTitle>
            <CardDescription>
              Check your email and open the link we sent to continue
            </CardDescription>
          </CardHeader>
          <CardContent>
            <div class="grid items-center justify-center gap-4">
              <Button class="max-w-[120px]" type="submit" :disabled="isPending">
                <LoaderCircle v-if="isPending" class="w-4 h-4 mr-2 animate-spin" />
                <RouterLink to="/auth/login"> Back to login</RouterLink>
              </Button>
            </div>
          </CardContent>
        </Card>
      </form>
    </div>
  </div>
</template>
