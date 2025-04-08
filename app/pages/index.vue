<template>
  <div class="py-24 sm:py-32">
    <!-- ヒーローセクション -->
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="text-center">
        <h1 class="text-4xl sm:text-6xl font-bold bg-gradient-to-r from-emerald-600 to-emerald-400 bg-clip-text text-transparent">
          シンプルで効率的な<br>タスク管理を実現
        </h1>
        <p class="mt-6 text-xl text-neutral-600 dark:text-neutral-400 max-w-3xl mx-auto">
          サーバーレスアーキテクチャとGitHub認証を採用した、<br>
          モダンで安全なタスク管理アプリケーション
        </p>
        <div class="mt-10 flex justify-center gap-4">
          <UButton
            v-if="!loggedIn"
            to="/api/auth/callback/github"
            icon="i-simple-icons-github"
            label="GitHubで始める"
            size="xl"
            color="primary"
            external
          />
        </div>
      </div>
    </div>

    <!-- 機能セクション -->
    <div class="mt-24 max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="grid md:grid-cols-3 gap-8">
        <div class="group p-8 rounded-2xl bg-gradient-to-b from-white to-emerald-50/50 dark:from-neutral-800/50 dark:to-neutral-900/50 shadow-xl shadow-emerald-900/5 dark:shadow-emerald-100/5 backdrop-blur-sm hover:shadow-2xl hover:-translate-y-1 transition-all duration-300">
          <div class="mb-6">
            <div class="i-lucide-key-round text-4xl text-emerald-500 dark:text-emerald-400 group-hover:scale-110 transition-transform duration-300" />
          </div>
          <h3 class="text-lg font-semibold mb-2">安全な認証</h3>
          <p class="text-neutral-600 dark:text-neutral-400">
            GitHubアカウントを使用した安全で簡単なログイン。個人情報は最小限に。
          </p>
        </div>
        
        <div class="group p-8 rounded-2xl bg-gradient-to-b from-white to-emerald-50/50 dark:from-neutral-800/50 dark:to-neutral-900/50 shadow-xl shadow-emerald-900/5 dark:shadow-emerald-100/5 backdrop-blur-sm hover:shadow-2xl hover:-translate-y-1 transition-all duration-300">
          <div class="mb-6">
            <div class="i-lucide-zap text-4xl text-emerald-500 dark:text-emerald-400 group-hover:scale-110 transition-transform duration-300" />
          </div>
          <h3 class="text-lg font-semibold mb-2">高速なレスポンス</h3>
          <p class="text-neutral-600 dark:text-neutral-400">
            エッジコンピューティングによる高速なレスポンスで、ストレスのない操作を実現。
          </p>
        </div>

        <div class="group p-8 rounded-2xl bg-gradient-to-b from-white to-emerald-50/50 dark:from-neutral-800/50 dark:to-neutral-900/50 shadow-xl shadow-emerald-900/5 dark:shadow-emerald-100/5 backdrop-blur-sm hover:shadow-2xl hover:-translate-y-1 transition-all duration-300">
          <div class="mb-6">
            <div class="i-lucide-check-circle text-4xl text-emerald-500 dark:text-emerald-400 group-hover:scale-110 transition-transform duration-300" />
          </div>
          <h3 class="text-lg font-semibold mb-2">最適化された UI</h3>
          <p class="text-neutral-600 dark:text-neutral-400">
            モダンで使いやすいインターフェースと、オプティミスティックUIによる快適な操作感。
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const { loggedIn } = useUserSession()
</script>
