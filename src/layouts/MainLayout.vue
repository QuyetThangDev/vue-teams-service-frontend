<template>
  <div class="flex flex-col w-full min-h-screen bg-muted/40">
    <Sidebar />
    <div class="flex flex-col flex-1 sm:gap-2 sm:py-2 lg:pl-80">
      <header class="flex h-14 items-center justify-between gap-4 px-4 lg:h-[60px] lg:px-6">
        <!-- <SidebarMobile /> -->
        <a href="/" class="flex items-center justify-start gap-2 font-semibold lg:hidden">
          <Logo class="object-contain w-12 h-8" />
          <span class="text-lg font-bold">ProjectEnergyAdmin</span>
        </a>
        <div class="relative flex-1 ml-auto grow-0">
          <Button variant="outline" size="icon" class="w-8 h-8 ml-auto">
            <Bell class="w-4 h-4" />
            <span class="sr-only">Toggle notifications</span>
          </Button>
        </div>
        <DropdownMenu>
          <DropdownMenuTrigger as-child>
            <Button variant="secondary" size="icon" class="rounded-full">
              <CircleUser class="w-5 h-5" />
              <span class="sr-only">Toggle user menu</span>
            </Button>
          </DropdownMenuTrigger>
          <DropdownMenuContent align="end">
            <!-- <DropdownMenuLabel>{{ userInfo?.username }}</DropdownMenuLabel> -->
            <DropdownMenuLabel>Settings</DropdownMenuLabel>
            <DropdownMenuSeparator />
            <DropdownMenuItem @click="() => $router.push({ name: 'OrganizationSettings' })">
              Users
            </DropdownMenuItem>
            <!-- <DropdownMenuItem @click="toLogin()">Logout</DropdownMenuItem> -->
            <DropdownMenuItem class="duration-300 hover:bg-red-50 text-destructive"
              >Logout</DropdownMenuItem
            >
          </DropdownMenuContent>
        </DropdownMenu>
      </header>
      <main class="flex flex-col flex-1 gap-4 p-4 overflow-auto">
        <router-view />
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
// import Logo from '@/assets/logo.vue'
import { Bell, CircleUser } from 'lucide-vue-next'

import { Button } from '@/components/ui/button'
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger
} from '@/components/ui/dropdown-menu'

import Sidebar from '@/components/SidebarComponent.vue'
// import SidebarMobile from '@/components/SidbarMobile.vue'

import { onMounted } from 'vue'

import { storeToRefs } from 'pinia'
// import { toLogin } from '@/router'
// import { getCurrentUser } from '@/api/user'
// import { useUserStore } from '@/stores'

// const { setUserInfo } = useUserStore()
// const { userInfo } = storeToRefs(useUserStore())

// const initUserInfo = async () => {
//   try {
//     const { data } = await getCurrentUser()
//     setUserInfo({
//       id: data.id,
//       username: data.username,
//       role: data.role,
//       enabled: data.enabled,
//       createdAt: data.created_at
//     })
//   } catch (error) {
//     console.error('Failed to fetch user info', error)
//     // toast({
//     //   title: "Failed to fetch user info",
//     // });
//   }
// }

onMounted(() => {
  // initUserInfo()
})
</script>
