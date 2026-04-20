<template>
  <div class="bg-bg flex min-h-screen flex-col font-sans">
    <!-- Navigation -->
    <nav
      class="bg-surface/80 sticky top-0 z-50 border-b border-gray-100 shadow-sm backdrop-blur-md transition-all duration-300 dark:border-gray-800"
    >
      <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
        <div class="flex h-16 items-center justify-between">
          <div class="flex items-center gap-3">
            <router-link to="/" class="flex items-center gap-2">
              <img :src="logoDesktop" alt="CivicConnect" class="hidden h-10 w-auto md:block" />
              <img :src="logoMobile" alt="CivicConnect" class="block h-10 w-auto md:hidden" />
            </router-link>
          </div>

          <div class="hidden items-center gap-6 md:flex">
            <!-- Public Links -->
            <template v-if="!authStore.isAuthenticated">
              <router-link
                to="/"
                active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                >Home</router-link
              >
            </template>

            <!-- Authenticated Links -->
            <template v-if="authStore.isAuthenticated">
              <!-- Admin Links -->
              <template v-if="authStore.isAdmin">
                <router-link
                  to="/admin/dashboard"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Dashboard</router-link
                >
                <router-link
                  to="/admin/analytics"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Analytics</router-link
                >
                <router-link
                  to="/admin/issues"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Issues</router-link
                >
                <router-link
                  to="/issues-map"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Map</router-link
                >
              </template>

              <template v-else-if="authStore.isCitizen">
                <router-link
                  :to="dashboardRoute"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Dashboard</router-link
                >
                <router-link
                  to="/issues"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Issues</router-link
                >
                <router-link
                  to="/issues-map"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Map</router-link
                >
              </template>

              <!-- Staff Links -->
              <template v-if="authStore.isStaff">
                <router-link
                  to="/staff/dashboard"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Staff Dashboard</router-link
                >
                <router-link
                  to="/staff/issues"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Manage Issues</router-link
                >
                <router-link
                  to="/staff/reports"
                  active-class="bg-primary/10 text-primary font-semibold rounded-lg"
                  class="text-text-light hover:text-primary px-3 py-2 font-medium transition-colors"
                  >Reports</router-link
                >
              </template>

              <!-- Notification Bell -->
              <NotificationBell />

              <!-- Profile Dropdown (Simplified for now) -->
              <div class="group relative">
                <button
                  class="text-text hover:text-primary flex items-center gap-2 font-medium transition-colors"
                >
                  <span
                    class="bg-primary-light text-primary rounded-full px-3 py-1 text-sm font-semibold"
                  >
                    {{ userInitials }}
                  </span>
                </button>
                <div
                  class="invisible absolute right-0 z-50 mt-2 w-48 origin-top-right transform rounded-xl border border-gray-100 bg-white py-1 opacity-0 shadow-lg transition-all duration-200 group-hover:visible group-hover:opacity-100"
                >
                  <router-link
                    to="/profile"
                    class="text-text hover:text-primary block px-4 py-2 text-sm hover:bg-gray-50"
                    >Profile</router-link
                  >
                  <!-- Citizen Specific Links -->
                  <template v-if="authStore.isCitizen">
                    <router-link
                      to="/my-issues"
                      class="text-text hover:text-primary block px-4 py-2 text-sm hover:bg-gray-50"
                      >My Issues</router-link
                    >
                  </template>
                  <button
                    @click="handleLogout"
                    class="text-danger hover:bg-danger-light/30 block w-full px-4 py-2 text-left text-sm"
                  >
                    Sign out
                  </button>
                </div>
              </div>
            </template>

            <!-- Auth Buttons -->
            <template v-else>
              <div class="flex items-center gap-3">
                <router-link
                  to="/login"
                  class="text-primary hover:text-primary-hover px-4 py-2 font-semibold transition-colors"
                  >Sign In</router-link
                >
                <router-link
                  to="/register"
                  class="btn-primary hover-lift shadow-primary/30 rounded-lg px-5 py-2 font-semibold shadow-sm"
                  >Get Started</router-link
                >
              </div>
            </template>
          </div>

          <!-- Mobile menu button (Simplified) -->
          <div class="flex items-center gap-4 md:hidden">
            <NotificationBell v-if="authStore.isAuthenticated" />
            <button
              @click="mobileMenuOpen = !mobileMenuOpen"
              class="text-text rounded-lg p-2 transition-colors hover:bg-gray-100"
            >
              <Bars3Icon class="h-6 w-6" />
            </button>
          </div>
        </div>
      </div>

      <!-- Mobile Menu -->
      <div
        v-if="mobileMenuOpen"
        class="absolute left-0 z-40 w-full border-t border-gray-100 bg-white shadow-lg md:hidden"
      >
        <div class="space-y-1 px-4 pt-2 pb-6">
          <template v-if="authStore.isAuthenticated">
            <router-link
              v-if="authStore.isCitizen"
              :to="dashboardRoute"
              active-class="bg-primary/10 text-primary font-semibold"
              class="text-text hover:text-primary block rounded-lg px-3 py-3 text-base font-medium hover:bg-gray-50"
              @click="mobileMenuOpen = false"
              >Dashboard</router-link
            >

            <!-- Citizen Links -->
            <template v-if="authStore.isCitizen">
              <router-link
                to="/issues"
                active-class="bg-primary/10 text-primary font-semibold"
                class="text-text hover:text-primary block rounded-lg px-3 py-3 text-base font-medium hover:bg-gray-50"
                @click="mobileMenuOpen = false"
                >Issues</router-link
              >
              <router-link
                to="/my-issues"
                active-class="bg-primary/10 text-primary font-semibold"
                class="text-text hover:text-primary block rounded-lg px-3 py-3 text-base font-medium hover:bg-gray-50"
                @click="mobileMenuOpen = false"
                >My Issues</router-link
              >
            </template>

            <!-- Admin Links -->
            <template v-if="authStore.isAdmin">
              <router-link
                to="/admin/dashboard"
                active-class="bg-primary/10 text-primary font-semibold"
                class="text-text hover:text-primary block rounded-lg px-3 py-3 text-base font-medium hover:bg-gray-50"
                @click="mobileMenuOpen = false"
                >Dashboard</router-link
              >
              <router-link
                to="/admin/analytics"
                active-class="bg-primary/10 text-primary font-semibold"
                class="text-text hover:text-primary block rounded-lg px-3 py-3 text-base font-medium hover:bg-gray-50"
                @click="mobileMenuOpen = false"
                >Analytics</router-link
              >
            </template>

            <!-- Staff Links -->
            <template v-if="authStore.isStaff">
              <router-link
                to="/staff/dashboard"
                active-class="bg-primary/10 text-primary font-semibold"
                class="text-text hover:text-primary block rounded-lg px-3 py-3 text-base font-medium hover:bg-gray-50"
                @click="mobileMenuOpen = false"
                >Staff Dashboard</router-link
              >
              <router-link
                to="/staff/issues"
                active-class="bg-primary/10 text-primary font-semibold"
                class="text-text hover:text-primary block rounded-lg px-3 py-3 text-base font-medium hover:bg-gray-50"
                @click="mobileMenuOpen = false"
                >Manage Issues</router-link
              >
              <router-link
                to="/staff/reports"
                active-class="bg-primary/10 text-primary font-semibold"
                class="text-text hover:text-primary block rounded-lg px-3 py-3 text-base font-medium hover:bg-gray-50"
                @click="mobileMenuOpen = false"
                >Reports</router-link
              >
            </template>

            <button
              @click="handleLogout"
              class="text-danger hover:bg-danger-light/20 block w-full rounded-lg px-3 py-3 text-left text-base font-medium"
            >
              Sign out
            </button>
          </template>
          <template v-else>
            <router-link
              to="/login"
              class="text-text hover:text-primary block rounded-lg px-3 py-3 text-base font-medium hover:bg-gray-50"
              @click="mobileMenuOpen = false"
              >Sign In</router-link
            >
            <router-link
              to="/register"
              class="text-primary bg-primary-light/30 block rounded-lg px-3 py-3 text-base font-medium"
              @click="mobileMenuOpen = false"
              >Get Started</router-link
            >
          </template>
        </div>
      </div>
    </nav>

    <!-- Main Content -->
    <main class="grow">
      <slot></slot>
    </main>

    <!-- Footer -->
    <footer class="mt-auto border-t border-gray-100 bg-white py-12">
      <div
        class="mx-auto flex max-w-7xl flex-col items-center justify-between gap-6 px-4 sm:px-6 md:flex-row lg:px-8"
      >
        <router-link
          to="/"
          class="flex items-center gap-2 opacity-80 transition-opacity hover:opacity-100"
        >
          <img :src="logoDesktop" alt="CivicConnect" class="h-8 w-auto" />
        </router-link>
        <div class="text-text-light text-sm">
          &copy; {{ new Date().getFullYear() }} CivicConnect. Empowering communities.
        </div>
        <div class="flex gap-6">
          <!-- Social placeholders -->
          <!-- Facebook -->
          <a
            href="https://web.facebook.com/yasser.c.ebad"
            class="text-text-lighter hover:text-primary transition-colors"
          >
            <span class="sr-only">Facebook</span>
            <svg class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24">
              <path
                d="M22 12c0-5.523-4.477-10-10-10S2 6.477 2 12c0 4.991 3.657 9.128 8.438 9.878v-6.987h-2.54V12h2.54V9.797c0-2.506 1.492-3.89 3.777-3.89 1.094 0 2.238.195 2.238.195v2.46h-1.26c-1.243 0-1.63.771-1.63 1.562V12h2.773l-.443 2.89h-2.33v6.988C18.343 21.128 22 16.991 22 12z"
              />
            </svg>
          </a>
          <!-- GitHub -->
          <a
            href="https://github.com/DevFusionOrg"
            class="text-text-lighter hover:text-primary transition-colors"
          >
            <span class="sr-only">GitHub</span>
            <svg class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24">
              <path
                fill-rule="evenodd"
                d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z"
                clip-rule="evenodd"
              />
            </svg>
          </a>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useAuthStore } from '../stores/authStore'
import { useRouter } from 'vue-router'
import NotificationBell from '../components/NotificationBell.vue'
import { Bars3Icon } from '@heroicons/vue/24/outline'
import logoDesktop from '@/assets/civic-connect-logo.png'
import logoMobile from '@/assets/civic-connect-logo-mobile.png'

const authStore = useAuthStore()
const router = useRouter()
const mobileMenuOpen = ref(false)

const dashboardRoute = computed(() => {
  if (authStore.isAdmin) return '/admin/dashboard'
  if (authStore.isStaff) return '/staff/dashboard'
  return '/dashboard'
})

const userInitials = computed(() => {
  if (authStore.user?.first_name && authStore.user?.last_name) {
    return (authStore.user.first_name.charAt(0) + authStore.user.last_name.charAt(0)).toUpperCase()
  }
  return 'U'
})

const handleLogout = () => {
  authStore.logout()
  router.push('/login')
  mobileMenuOpen.value = false
}
</script>
