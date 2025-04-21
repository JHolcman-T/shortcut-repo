<template>
  <div class="flex flex-col gap-8">
    <h1 class="text-4xl">Shortcut Repo</h1>
    <div class="flex flex-col gap-2">
      <Dialog
        v-model:open="formOpen"
        @update:open="(state:boolean) => (state === false ? resetForm() : null)"
      >
        <DialogTrigger asChild>
          <Button variant="default" class="hover:cursor-pointer">
            <span class="text-sm">Add Shortcut</span>
            <Icon icon="radix-icons:plus" />
          </Button>
        </DialogTrigger>
        <DialogContent class="select-none">
          <DialogHeader>
            <DialogTitle>Add new shortcut</DialogTitle>
            <DialogDescription>
              Create new shortcut by adding keys and a description.
            </DialogDescription>
          </DialogHeader>
          <Card>
            <CardContent>
              <div class="flex items-center gap-2">
                <template v-for="(key, keyIndex) in formData.keys" v-if="formData.keys.length > 0">
                  <template v-if="keyIndex > 0">+</template>
                  <KbdKey
                    :keyName="key"
                    @click="removeKey(keyIndex)"
                    class="hover:bg-red-400 hover:cursor-pointer"
                  />
                </template>
                <template v-else>
                  <span class="text-muted-foreground text-sm text-center w-full"
                    >No keys added yet.</span
                  >
                </template>
              </div>
            </CardContent>
          </Card>
          <form @submit.prevent="addKey" class="flex items-center gap-2">
            <Input
              v-model="newKeyValue"
              type="text"
              placeholder="Add key"
              class="border border-gray-300 rounded-md p-2"
              pattern="\S+"
              minlength="1"
            />
            <Button type="submit" variant="outline" class="hover:cursor-pointer">
              <span class="text-sm">Add</span>
            </Button>
          </form>
          <Input
            v-model="formData.description"
            type="text"
            placeholder="Description"
            class="border border-gray-300 rounded-md p-2 mt-2"
            minlength="1"
          />
          <DialogFooter>
            <Button type="submit" @click="saveShortcut" class="w-full hover:cursor-pointer">
              Save changes
            </Button>
          </DialogFooter>
        </DialogContent>
      </Dialog>
      <!-- Shortcut list -->
      <Card
        v-for="shortCut in shortCuts"
        :key="shortCut.keys.toString()"
        v-if="shortCuts.length > 0"
      >
        <CardContent>
          <div class="flex items-center gap-2">
            <template v-for="(key, keyIndex) in shortCut.keys">
              <template v-if="keyIndex > 0">+</template>
              <KbdKey :keyName="key" />
            </template>
            <CardDescription class="text-sm text-gray-500">
              {{ shortCut.description }}
            </CardDescription>
            <Button size="icon" variant="ghost" class="ml-auto hover:cursor-pointer">
              <Icon icon="radix-icons:pencil-1" />
            </Button>
          </div>
        </CardContent>
      </Card>
      <template v-else>
        <span class="text-muted-foreground text-sm text-center w-full mt-8">
          No shortcuts added yet.
        </span>
      </template>
    </div>
  </div>
</template>

<script setup lang="ts">
  import KbdKey from "./components/KbdKey.vue"
  import {reactive, ref} from "vue"
  import Card from "./components/ui/card/Card.vue"
  import CardContent from "./components/ui/card/CardContent.vue"
  import CardDescription from "./components/ui/card/CardDescription.vue"
  import Button from "./components/ui/button/Button.vue"
  import Input from "./components/ui/input/Input.vue"
  import {
    Dialog,
    DialogContent,
    DialogTrigger,
    DialogHeader,
    DialogDescription,
    DialogTitle,
    DialogFooter,
  } from "./components/ui/dialog"
  import {Icon} from "@iconify/vue"

  interface IShortcut {
    keys: string[]
    description: string
  }

  const shortCuts = ref<IShortcut[]>([])

  //
  // -------------- form handling --------------
  //
  const formOpen = ref(false)
  const newKeyValue = ref<string>("")

  const formData = reactive({
    keys: [],
    description: "",
  })

  function addKey() {
    if (!newKeyValue.value) return
    formData.keys.push(newKeyValue.value)
    newKeyValue.value = ""
  }

  function removeKey(index: number) {
    formData.keys.splice(index, 1)
  }

  function saveShortcut() {
    if (!formData.description || formData.keys.length < 2) return
    if (shortCuts.value.some(shortcut => shortcut.keys.toString() === formData.keys.toString())) {
      return
    }
    shortCuts.value.push({
      keys: formData.keys,
      description: formData.description,
    })
    resetForm()
    toggleForm()
  }

  function toggleForm() {
    formOpen.value = !formOpen.value
  }

  function resetForm() {
    newKeyValue.value = ""
    formData.keys = []
    formData.description = ""
  }
</script>
