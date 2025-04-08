<script setup lang="ts">
import type { DropdownMenuItem } from '#ui/types'

const { loggedIn, user, clear } = useUserSession()
const colorMode = useColorMode()

watch(loggedIn, () => {
  if (!loggedIn.value) {
    navigateTo('/')
  }
})

const isDarkMode = computed({
  get: () => colorMode.preference === 'dark',
  set: () =>
    (colorMode.preference = colorMode.value === 'dark' ? 'light' : 'dark')
})

useHead({
  htmlAttrs: { lang: 'en' },
  link: [{ rel: 'icon', href: '/icon.png' }]
})

useSeoMeta({
  viewport: 'width=device-width, initial-scale=1, maximum-scale=1',
  title: 'Atidone',
  description:
    'A Nuxt demo hosted with edge-side rendering, authentication and queyring a Cloudflare D1 database',
  ogImage: '/social-image.png',
  twitterImage: '/social-image.png',
  twitterCard: 'summary_large_image'
})

const items = [
  [
    {
      label: 'Logout',
      icon: 'i-lucide-log-out',
      onSelect: clear
    }
  ]
] satisfies DropdownMenuItem[][]
</script>

<template>
  <UApp>
    <div class="min-h-screen flex flex-col">
      <!-- ナビゲーションバー -->
      <nav class="bg-white/60 dark:bg-black/60 backdrop-blur-xl shadow-[0_8px_30px_rgb(0,0,0,0.12)] dark:shadow-[0_8px_30px_rgba(0,0,0,0.3)] fixed w-full z-50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="flex justify-between items-center h-20">
            <div class="flex items-center">
              <NuxtLink to="/" class="text-2xl font-bold bg-gradient-to-r from-emerald-600 to-emerald-400 bg-clip-text text-transparent hover:from-emerald-500 hover:to-emerald-300 transition-all duration-300">
                Nuxt Todo
              </NuxtLink>
            </div>
            
            <div class="flex items-center gap-4">
              <UButton
                square
                variant="ghost"
                color="primary"
                :icon="$colorMode.preference === 'dark' ? 'i-lucide-moon' : 'i-lucide-sun'"
                class="text-emerald-600 dark:text-emerald-400"
                @click="isDarkMode = !isDarkMode"
              />
              
              <UButton
                v-if="!loggedIn"
                to="/api/auth/github"
                icon="i-simple-icons-github"
                label="Login with GitHub"
                color="primary"
                external
              />
              
              <template v-else>
                <UDropdownMenu
                  v-if="user"
                  :items="items"
                >
                  <UButton
                    color="primary"
                    variant="ghost"
                    trailing-icon="i-lucide-chevron-down"
                    class="text-emerald-950 dark:text-emerald-50"
                  >
                    <UAvatar
                      :src="`https://github.com/${user.login}.png`"
                      :alt="user.login"
                      size="3xs"
                    />
                    {{ user.login }}
                  </UButton>
                </UDropdownMenu>
              </template>
            </div>
          </div>
        </div>
      </nav>

      <!-- メインコンテンツ -->
      <main class="flex-1 pt-24">
        <NuxtPage />
      </main>

      <!-- フッター -->
      <footer class="bg-gradient-to-b from-transparent via-emerald-50/5 to-emerald-100/10 dark:via-emerald-900/5 dark:to-emerald-900/10 py-8 mt-auto">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="text-center text-sm text-emerald-600 dark:text-emerald-400">
            ©Akito Ichige
          </div>
        </div>
      </footer>
    </div>
  </UApp>
</template>

<style lang="postcss">
body {
  @apply font-sans text-emerald-950 bg-white dark:text-emerald-50 dark:bg-black;
}

.page-enter-active,
.page-leave-active {
  transition: all 0.3s ease;
}
.page-enter-from,
.page-leave-to {
  opacity: 0;
  transform: translateY(20px);
}
</style>
