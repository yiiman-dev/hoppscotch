<template>
  <iframe :src="pdfsrc" class="flex flex-1">
    {{ $t("error.something_went_wrong") }}
  </iframe>
</template>

<script setup lang="ts">
import { ref } from "@nuxtjs/composition-api"
import { HoppRESTResponse } from "~/helpers/types/HoppRESTResponse"

const props = defineProps<{
  response: HoppRESTResponse
}>()

const pdfsrc = ref(null)
const blob = new Blob([props.response.body], {
  type: "application/pdf",
})
const objectUrl = URL.createObjectURL(blob)
pdfsrc.value = objectUrl
</script>
