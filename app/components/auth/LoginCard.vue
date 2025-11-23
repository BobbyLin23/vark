<script lang="ts" setup>
import type { HTMLAttributes } from 'vue'
import { toTypedSchema } from '@vee-validate/zod'
import { useForm } from 'vee-validate'
import { toast } from 'vue-sonner'
import { z } from 'zod'
import { authClient } from '~/lib/auth-client'
import { cn } from '~/lib/utils'

const props = defineProps<{
  class?: HTMLAttributes['class']
}>()

const emits = defineEmits<{
  (e: 'toggleState', value: 'login' | 'register'): void
}>()

const isLoading = ref(false)

const formSchema = toTypedSchema(z.object({
  email: z.email('Invalid email address'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
}))

const form = useForm({
  validationSchema: formSchema,
})

const onSubmit = form.handleSubmit(async (values) => {
  isLoading.value = true

  try {
    await authClient.signIn.email({
      email: values.email,
      password: values.password,
      callbackURL: '/dashboard',
    }, {
      onSuccess: () => {
        navigateTo('/dashboard')
      },
      onError: (ctx) => {
        toast.error(ctx.error.message)
      },
    })
  }
  catch (err) {
    console.error(err)
  }
  finally {
    isLoading.value = false
  }
})
</script>

<template>
  <div :class="cn('flex flex-col gap-6', props.class)">
    <Card class="max-w-md w-full">
      <CardHeader>
        <CardTitle>Login to continue</CardTitle>
        <CardDescription>
          Use your email or another service to continue.
        </CardDescription>
      </CardHeader>
      <CardContent>
        <form class="space-y-4" @submit="onSubmit">
          <FormField v-slot="{ componentField }" name="email">
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input type="email" placeholder="m@example.com" v-bind="componentField" :disabled="isLoading" />
              </FormControl>
              <FormMessage />
            </FormItem>
          </FormField>
          <FormField v-slot="{ componentField }" name="password">
            <FormItem>
              <FormLabel>Password</FormLabel>
              <FormControl>
                <Input type="password" placeholder="********" v-bind="componentField" :disabled="isLoading" />
              </FormControl>
              <FormMessage />
            </FormItem>
          </FormField>
          <Button type="submit" class="w-full" :disabled="isLoading">
            <Spinner v-if="isLoading" />
            <span v-if="isLoading">Signing in...</span>
            <span v-else>Login</span>
          </Button>
          <div class="text-center text-sm text-muted-foreground">
            Don't have an account?
            <Button variant="link" class="p-0 cursor-pointer" :disabled="isLoading" @click="emits('toggleState', 'register')">
              Sign up
            </Button>
          </div>
          <Separator />
          <Button variant="outline" class="w-full" :disabled="isLoading">
            <Icon name="ri:google-fill" class="size-4" />
            Continue with Google
          </Button>
          <Button variant="outline" class="w-full" :disabled="isLoading">
            <Icon name="ri:github-fill" class="size-4" />
            Continue with GitHub
          </Button>
        </form>
      </CardContent>
    </Card>
  </div>
</template>
