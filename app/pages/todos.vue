<script setup lang="ts">
definePageMeta({
  middleware: 'auth'
})
const newTodo = ref('')
const newTodoInput = useTemplateRef('new-todo')

const toast = useToast()
const queryCache = useQueryCache()

const { data: todos } = useQuery({
  key: ['todos'],
  // NOTE: the cast sometimes avoids an "Excessive depth check" TS error
  // using $fetch directly doesn't avoid the round trip to the server
  // when doing SSR
  // https://github.com/nuxt/nuxt/issues/24813
  query: () => useRequestFetch()('/api/todos') as Promise<Todo[]>
})

const { mutate: addTodo, isLoading: loading } = useMutation({
  mutation: (title: string) => {
    if (!title.trim()) throw new Error('Title is required')

    return $fetch('/api/todos', {
      method: 'POST',
      body: {
        title,
        completed: 0
      }
    })
  },

  async onSuccess(todo) {
    await queryCache.invalidateQueries({ key: ['todos'] })
    toast.add({ title: `Todo "${todo.title}" created.` })
  },

  onSettled() {
    newTodo.value = ''
    // the first nextTick allows loading to become false and re enable the input
    // the second nextTick allows the input to be rendered again so it can be focused
    // a better solution would be to use a custom `v-focus` directive or a more elaborated focus management solution
    nextTick()
      .then(() => nextTick())
      .then(() => {
        newTodoInput.value?.input?.focus()
      })
  },

  onError(err) {
    if (isNuxtZodError(err)) {
      const title = err.data?.data.issues
        .map((issue: { message: string }) => issue.message)
        .join('\n')
      if (title) {
        toast.add({ title, color: 'error' })
      }
    }
    else {
      console.error(err)
      toast.add({ title: 'Unexpected Error', color: 'error' })
    }
  }
})

const { mutate: toggleTodo } = useMutation({
  mutation: (todo: Todo) =>
    $fetch(`/api/todos/${todo.id}`, {
      method: 'PATCH',
      body: {
        completed: Number(!todo.completed)
      }
    }),

  async onSuccess() {
    await queryCache.invalidateQueries({ key: ['todos'] })
  }
})

const { mutate: deleteTodo } = useMutation({
  mutation: (todo: Todo) =>
    $fetch(`/api/todos/${todo.id}`, { method: 'DELETE' }),

  async onSuccess(_result, todo) {
    await queryCache.invalidateQueries({ key: ['todos'] })
    toast.add({ title: `Todo "${todo.title}" deleted.` })
  }
})
</script>

<template>
  <div class="max-w-2xl mx-auto">
    <form
      class="flex flex-col gap-6"
      @submit.prevent="addTodo(newTodo)"
    >
      <div class="flex items-center gap-3 p-3 bg-neutral-800/50 backdrop-blur-sm rounded-xl shadow-lg shadow-emerald-900/5">
        <UInput
          ref="new-todo"
          v-model="newTodo"
          name="todo"
          :disabled="loading"
          class="flex-1 bg-transparent border-0 focus:ring-0 placeholder-neutral-600"
          placeholder="write anything and hit enter to add"
          autocomplete="off"
          autofocus
        />

        <UButton
          type="submit"
          color="primary"
          variant="solid"
          :class="{ 'opacity-50': newTodo.trim().length === 0 }"
          :icon="loading ? 'i-lucide-loader-2' : 'i-lucide-plus'"
          :loading="loading"
          :disabled="newTodo.trim().length === 0"
        />
      </div>

      <ul class="flex flex-col gap-2">
        <li
          v-for="todo of todos"
          :key="todo.id"
          class="group flex items-center gap-4 p-3 bg-neutral-800/50 backdrop-blur-sm rounded-xl transition-all duration-200 hover:bg-neutral-700/50"
        >
          <USwitch
            :model-value="Boolean(todo.completed)"
            @update:model-value="toggleTodo(todo)"
            class="!bg-neutral-700 data-[checked]:!bg-emerald-500"
          />

          <span
            class="flex-1 font-medium transition-all duration-200"
            :class="[todo.completed ? 'line-through text-neutral-500' : 'text-neutral-200']"
          >{{ todo.title }}</span>

          <UButton
            color="error"
            variant="ghost"
            size="xs"
            icon="i-lucide-trash-2"
            class="opacity-0 group-hover:opacity-100 transition-opacity duration-200"
            @click="deleteTodo(todo)"
          />
        </li>
      </ul>
    </form>
  </div>
</template>

<style>
.todo-enter-active,
.todo-leave-active {
  transition: all 0.3s ease;
}
.todo-enter-from,
.todo-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>
