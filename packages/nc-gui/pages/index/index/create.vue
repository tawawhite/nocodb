<script lang="ts" setup>
import type { Form } from 'ant-design-vue'
import type { VNodeRef } from '@vue/runtime-core'
import {
  extractSdkResponseErrorMsg,
  message,
  navigateTo,
  projectTitleValidator,
  reactive,
  ref,
  useApi,
  useNuxtApp,
  useSidebar,
} from '#imports'

const { $e } = useNuxtApp()

const { api, isLoading } = useApi({ useGlobalInstance: true })

useSidebar('nc-left-sidebar', { hasSidebar: false })

const nameValidationRules = [
  {
    required: true,
    message: 'Project name is required',
  },
  projectTitleValidator,
]

const form = ref<typeof Form>()

const formState = reactive({
  title: '',
})

const createProject = async () => {
  $e('a:project:create:xcdb')
  try {
    const result = await api.project.create({
      title: formState.title,
    })

    await navigateTo(`/nc/${result.id}`)
  } catch (e: any) {
    message.error(await extractSdkResponseErrorMsg(e))
  }
}

const focus: VNodeRef = (el) => (el as HTMLInputElement)?.focus()
</script>

<template>
  <div
    class="create relative flex flex-col justify-center gap-2 w-full p-8 md:(bg-white rounded-lg border-1 border-gray-200 shadow)"
  >
    <LazyGeneralNocoIcon class="color-transition hover:(ring ring-accent)" :animate="isLoading" />

    <div
      class="color-transition transform group absolute top-5 left-5 text-4xl rounded-full cursor-pointer"
      @click="navigateTo('/')"
    >
      <MdiChevronLeft class="text-black group-hover:(text-accent scale-110)" />
    </div>

    <h1 class="prose-2xl font-bold self-center my-4">{{ $t('activity.createProject') }}</h1>

    <a-form
      ref="form"
      :model="formState"
      name="basic"
      layout="vertical"
      class="lg:max-w-3/4 w-full !mx-auto"
      no-style
      autocomplete="off"
      @finish="createProject"
    >
      <a-form-item :label="$t('labels.projName')" name="title" :rules="nameValidationRules" class="m-10">
        <a-input :ref="focus" v-model:value="formState.title" name="title" class="nc-metadb-project-name" />
      </a-form-item>

      <div class="text-center">
        <button class="scaling-btn bg-opacity-100" type="submit">
          <span class="flex items-center gap-2">
            <MaterialSymbolsRocketLaunchOutline />
            {{ $t('general.create') }}
          </span>
        </button>
      </div>
    </a-form>
  </div>
</template>

<style lang="scss">
.create {
  .ant-input-affix-wrapper,
  .ant-input {
    @apply !appearance-none my-1 border-1 border-solid border-primary border-opacity-50 rounded;
  }
}
</style>
