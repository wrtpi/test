<script setup lang='ts'>
import { computed, ref, watch } from 'vue'
import { NCard, NModal } from 'naive-ui'
import pkg from '../../../../package.json'
import { fetchChatConfig } from '@/api'

interface Props {
  visible: boolean
}

interface Emit {
  (e: 'update:visible', visible: boolean): void
}

interface ConfigState {
  timeoutMs?: number
  reverseProxy?: string
  apiModel?: string
  socksProxy?: string
}

const props = defineProps<Props>()

const emit = defineEmits<Emit>()

const show = computed({
  get() {
    return props.visible
  },
  set(visible: boolean) {
    emit('update:visible', visible)
  },
})

const config = ref<ConfigState>()

async function fetchConfig() {
  try {
    const { data } = await fetchChatConfig<ConfigState>()
    config.value = data
  }
  catch (error) {
    // ...
  }
}

watch(
  () => props.visible,
  (val) => {
    if (val)
      fetchConfig()
  },
)
</script>

<template>
  <NModal v-model:show="show" style="width: 80%; max-width: 460px;">
    <NCard>
      <div class="space-y-4">
        <h2 class="text-xl font-bold text-center">
          Version - {{ pkg.version }}
        </h2>
        <hr>
        <p>
	ChatGPT是一种大型语言模型由OpenAI训练的人工智能。它可以帮助你生成自然语言表达，以及更好地理解交流。
        </p>
        <hr>
        <p>API方式：{{ config?.apiModel ?? '-' }}</p>
        <p>反向代理：{{ config?.reverseProxy ?? '-' }}</p>
        <p>超时时间：{{ config?.timeoutMs ?? '-' }}</p>
        <p>Socks代理：{{ config?.socksProxy ?? '-' }}</p>
      </div>
    </NCard>
  </NModal>
</template>
