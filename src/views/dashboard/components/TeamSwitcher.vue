<script setup lang="ts">
import { ref } from 'vue'
import { cn } from '@/lib/utils'

import { onMounted } from 'vue'

import { storeToRefs } from 'pinia'
import { getCurrentUser } from '@/api/user'
import { useUserStore } from '@/stores/userStore'
import { useToast } from '@/components/ui/toast'

const { setUserInfo } = useUserStore()
const { toast } = useToast()
const { userInfo } = storeToRefs(useUserStore())

import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar'
import { Button } from '@/components/ui/button'
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger
} from '@/components/ui/dialog'
import {
  Command,
  CommandEmpty,
  CommandGroup,
  CommandInput,
  CommandItem,
  CommandList,
  CommandSeparator
} from '@/components/ui/command'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { Popover, PopoverContent, PopoverTrigger } from '@/components/ui/popover'
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue
} from '@/components/ui/select'
import { CheckIcon, ChevronsUpDown, CirclePlus } from 'lucide-vue-next'

const groups = [
  {
    label: 'Personal Account',
    teams: [
      {
        label: 'Alicia Koch',
        value: 'personal'
      }
    ]
  },
  {
    label: 'Teams',
    teams: [
      {
        label: 'Acme Inc.',
        value: 'acme-inc'
      },
      {
        label: 'Monsters Inc.',
        value: 'monsters'
      }
    ]
  }
]

type Team = (typeof groups)[number]['teams'][number]

const open = ref(false)
const showNewTeamDialog = ref(false)
const selectedTeam = ref<Team>(groups[0].teams[0])

// onMounted(async () => {
//   try {
//     const response = await getCurrentUser()
//     const data = response.data

//     const userInfo = {
//       id: data.id,
//       first_name: data.first_name,
//       last_name: data.last_name,
//       email: data.email,
//       role: data.role,
//       created_at: new Date(data.created_at), // Chuyển đổi thành đối tượng Date
//       updated_at: data.updated_at ? new Date(data.updated_at) : null // Xử lý trường hợp có thể là null
//     }
//     setUserInfo(userInfo)
//   } catch (error) {
//     console.error('Failed to fetch user info', error)
//     toast({
//       title: 'Failed to fetch user info',
//       // description: error.message,
//       variant: 'destructive'
//     })
//   }
// })
</script>

<template>
  <Dialog v-model:open="showNewTeamDialog">
    <Popover v-model:open="open">
      <PopoverTrigger as-child>
        <Button
          variant="outline"
          role="combobox"
          aria-expanded="open"
          aria-label="Select a team"
          :class="cn('w-[200px] justify-between', $attrs.class ?? '')"
        >
          <Avatar class="w-5 h-5 mr-2">
            <AvatarImage
              :src="`https://avatar.vercel.sh/${selectedTeam.value}.png`"
              :alt="selectedTeam.label"
            />
            <AvatarFallback>SC</AvatarFallback>
          </Avatar>
          {{ selectedTeam.label }}
          <ChevronsUpDown class="w-3.5 h-3.5 ml-auto opacity-50 shrink-0" />
        </Button>
      </PopoverTrigger>
      <PopoverContent class="w-[200px] p-0">
        <Command v-model="open">
          <CommandList>
            <CommandInput placeholder="Search team..." />
            <CommandEmpty>No team found.</CommandEmpty>
            <CommandGroup v-for="group in groups" :key="group.label" :heading="group.label">
              <CommandItem
                v-for="team in group.teams"
                :key="team.value"
                :value="team"
                class="text-sm"
                @select="
                  () => {
                    selectedTeam = team
                    open = false
                  }
                "
              >
                <Avatar class="w-5 h-5 mr-2">
                  <AvatarImage
                    :src="`https://avatar.vercel.sh/${team.value}.png`"
                    :alt="team.label"
                    class="grayscale"
                  />
                  <AvatarFallback>SC</AvatarFallback>
                </Avatar>
                {{ team.label }}

                <CheckIcon
                  :class="
                    cn(
                      'ml-auto h-4 w-4',
                      selectedTeam.value === team.value ? 'opacity-100' : 'opacity-0'
                    )
                  "
                />
              </CommandItem>
            </CommandGroup>
          </CommandList>
          <CommandSeparator />
          <CommandList>
            <CommandGroup>
              <DialogTrigger as-child>
                <CommandItem
                  value="create-team"
                  @select="
                    () => {
                      open = false
                      showNewTeamDialog = true
                    }
                  "
                >
                  <CirclePlus class="w-4 h-4 mr-2 text-muted-foreground" />
                  Create Team
                </CommandItem>
              </DialogTrigger>
            </CommandGroup>
          </CommandList>
        </Command>
      </PopoverContent>
    </Popover>
    <DialogContent>
      <DialogHeader>
        <DialogTitle>Create team</DialogTitle>
        <DialogDescription> Add a new team to manage products and customers. </DialogDescription>
      </DialogHeader>
      <div>
        <div class="py-2 pb-4 space-y-4">
          <div class="space-y-2">
            <Label for="name">Team name</Label>
            <Input id="name" placeholder="Acme Inc." />
          </div>
          <div class="space-y-2">
            <Label for="plan">Subscription plan</Label>
            <Select>
              <SelectTrigger>
                <SelectValue placeholder="Select a plan" />
              </SelectTrigger>
              <SelectContent>
                <SelectItem value="free">
                  <span class="font-medium">Free</span> -
                  <span class="text-muted-foreground"> Trial for two weeks </span>
                </SelectItem>
                <SelectItem value="pro">
                  <span class="font-medium">Pro</span> -
                  <span class="text-muted-foreground"> $9/month per user </span>
                </SelectItem>
              </SelectContent>
            </Select>
          </div>
        </div>
      </div>
      <DialogFooter>
        <Button variant="outline" @click="showNewTeamDialog = false"> Cancel </Button>
        <Button type="submit"> Continue </Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>
