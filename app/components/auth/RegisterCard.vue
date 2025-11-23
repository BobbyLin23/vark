<script lang="ts" setup>
import type { HTMLAttributes } from 'vue'
import { toTypedSchema } from '@vee-validate/zod'
import { useForm } from 'vee-validate'
import { z } from 'zod'
import { cn } from '~/lib/utils'

const props = defineProps<{
  class?: HTMLAttributes['class']
}>()

const emits = defineEmits<{
  (e: 'toggleState', value: 'login' | 'register'): void
}>()

const formSchema = toTypedSchema(z.object({
  email: z.email(),
  password: z.string().min(8),
  confirmPassword: z.string().min(8),
}).refine(data => data.password === data.confirmPassword, {
  path: ['confirmPassword'],
  message: 'Passwords do not match',
}))

const form = useForm({
  validationSchema: formSchema,
})

const onSubmit = form.handleSubmit((values) => {
  console.log(values)
})
</script>

<template>
  <div :class="cn('flex flex-col gap-6', props.class)">
    <Card class="max-w-md w-full">
      <CardHeader>
        <CardTitle>Sign up to continue</CardTitle>
        <CardDescription>
          Use your email to create an account.
        </CardDescription>
      </CardHeader>
      <CardContent>
        <form class="space-y-4" @submit="onSubmit">
          <FormField v-slot="{ componentField }" name="email">
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input type="email" placeholder="m@example.com" v-bind="componentField" />
              </FormControl>
              <FormMessage />
            </FormItem>
          </FormField>
          <FormField v-slot="{ componentField }" name="password">
            <FormItem>
              <FormLabel>Password</FormLabel>
              <FormControl>
                <Input type="password" placeholder="********" v-bind="componentField" />
              </FormControl>
              <FormMessage />
            </FormItem>
          </FormField>
          <FormField v-slot="{ componentField }" name="confirmPassword">
            <FormItem>
              <FormLabel>Confirm Password</FormLabel>
              <FormControl>
                <Input type="password" placeholder="********" v-bind="componentField" />
              </FormControl>
              <FormMessage />
            </FormItem>
          </FormField>
          <Button type="submit" class="w-full">
            Sign up
          </Button>
          <div class="text-center text-sm text-muted-foreground">
            Already have an account?
            <Button variant="link" class="p-0 cursor-pointer" @click="emits('toggleState', 'login')">
              Login
            </Button>
          </div>
          <Separator />
          <Button variant="outline" class="w-full">
            <Icon name="ri:google-fill" class="size-4" />
            Continue with Google
          </Button>
          <Button variant="outline" class="w-full">
            <Icon name="ri:github-fill" class="size-4" />
            Continue with GitHub
          </Button>
        </form>
      </CardContent>
    </Card>
  </div>
</template>
