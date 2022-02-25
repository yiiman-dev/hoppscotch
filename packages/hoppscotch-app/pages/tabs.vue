<template>
  <Splitpanes class="smart-splitter" :dbl-click-splitter="false" vertical>
    <Pane class="flex flex-col hide-scrollbar !overflow-auto">
      <div
        class="relative sticky top-0 inline-flex flex-1 w-full divide-divider divide-x bg-primaryLight"
      >
        <draggable
          v-bind="dragOptions"
          :list="tabs"
          :style="tabsWidth"
          class="flex overflow-x-auto transition hide-scrollbar"
        >
          <transition-group
            class="flex divide-primaryDark divide-x"
            name="fade"
            appear
          >
            <button
              v-for="tab in tabs"
              :key="`tab-${tab.id}`"
              :class="[{ active: active(tab.id) }, 'tab']"
              @click="changeTab(tab.id)"
            >
              <span class="truncate">{{ tab.name }}</span>
              <ButtonSecondary
                svg="x"
                :class="[{ active: active(tab.id) }, 'close']"
                class="rounded my-0.5 mr-0.5 ml-4 !p-1"
                @click.native.stop="closeTab(tab.id)"
              />
            </button>
          </transition-group>
        </draggable>
        <span class="flex items-center justify-center p-1 bg-primaryLight">
          <ButtonSecondary
            svg="plus"
            class="sticky right-0 rounded"
            @click.native="addTab"
          />
        </span>
      </div>
      <Splitpanes class="smart-splitter" :dbl-click-splitter="false" horizontal>
        <Pane class="hide-scrollbar !overflow-auto">
          <div v-if="tabs.length">
            <div
              v-for="tab in tabs"
              :key="`request-${tab.id}`"
              :class="[{ active: active(tab.id) }, 'tab-content']"
            >
              {{ tab.id }}
            </div>
          </div>
          <div v-else>
            <div class="empty-tab-content">empty</div>
          </div>
        </Pane>
        <Pane class="hide-scrollbar !overflow-auto">
          <div v-if="tabs.length">
            <div
              v-for="tab in tabs"
              :key="`response-${tab.id}`"
              :class="[{ active: active(tab.id) }, 'tab-content']"
            >
              {{ tab.id }}
            </div>
          </div>
          <div v-else>
            <div class="empty-tab-content">empty</div>
          </div>
        </Pane>
      </Splitpanes>
    </Pane>
    <Pane
      max-size="35"
      size="25"
      min-size="20"
      class="hide-scrollbar !overflow-auto"
    >
      <aside>Sidebar</aside>
    </Pane>
  </Splitpanes>
</template>

<script setup lang="ts">
import draggable from "vuedraggable"
import { Splitpanes, Pane } from "splitpanes"
import "splitpanes/dist/splitpanes.css"
import { computed, ref } from "@nuxtjs/composition-api"

const currentTabId = ref(0)
const nextTabId = ref(1)
const tabs = ref([
  {
    id: 0,
    name: "Untitled request",
  },
])

const dragOptions = computed(() => ({
  group: "tabs",
  animation: 250,
  handle: ".tab",
  draggable: ".tab",
  ghostClass: "cursor-move",
}))

const tabsWidth = computed(() => ({
  maxWidth: `${tabs.value.length * 184}px`,
  width: "100%",
  minWidth: "0px",
  transition: "max-width 0.2s",
}))

const active = (id: number) => id === currentTabId.value

const changeTab = (id: number) => {
  currentTabId.value = id
}

const addTab = () => {
  tabs.value.push({
    id: nextTabId.value,
    name: "Untitled request",
  })
  currentTabId.value = nextTabId.value
  nextTabId.value++
}

const closeTab = (id: number) => {
  const index = tabs.value.findIndex((tab) => tab.id === id)
  tabs.value.splice(index, 1)
  if (currentTabId.value === id) {
    if (tabs.value[index]?.id) currentTabId.value = tabs.value[index]?.id
    else currentTabId.value = tabs.value[tabs.value.length - 1]?.id
  }
}
</script>

<style lang="scss" scoped>
.tab {
  @apply relative;
  @apply flex;
  @apply pl-4;
  @apply pr-1;
  @apply py-1;
  @apply font-semibold;
  @apply w-46;
  @apply transition;
  @apply flex-1;
  @apply items-center;
  @apply justify-between;
  @apply text-secondaryLight;
  @apply hover:bg-primaryDark;
  @apply hover:text-secondary;
  @apply focus-visible:text-secondaryDark;

  &::after {
    @apply absolute;
    @apply left-0;
    @apply right-0;
    @apply top-0;
    @apply bg-transparent;
    @apply z-2;
    @apply h-0.5;

    content: "";
  }

  &:focus::after {
    @apply bg-divider;
  }

  &.active {
    @apply text-secondaryDark;
    @apply bg-primary;

    &::after {
      @apply bg-accent;
    }
  }
}

.tab-content {
  @apply p-4;
  @apply hidden;

  &.active {
    @apply flex;
  }
}

.close {
  @apply opacity-50;

  &.active {
    @apply opacity-100;
  }
}
</style>
