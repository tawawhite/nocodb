<script setup lang="ts">
import { IsLockedInj, IsPublicInj, useKanbanViewStoreOrThrow, useMenuCloseOnEsc } from '#imports'

const { isUIAllowed } = useUIPermission()

const { groupingFieldColumn } = useKanbanViewStoreOrThrow()

const isLocked = inject(IsLockedInj, ref(false))

const IsPublic = inject(IsPublicInj, ref(false))

const open = ref(false)

useMenuCloseOnEsc(open)

const handleSubmit = async () => {
  open.value = false
}

provide(IsKanbanInj, ref(true))
</script>

<template>
  <a-dropdown
    v-if="!IsPublic && isUIAllowed('edit-column')"
    v-model:visible="open"
    :trigger="['click']"
    overlay-class-name="nc-dropdown-kanban-add-edit-stack-menu"
  >
    <div class="nc-kanban-btn">
      <a-button
        v-e="['c:kanban:edit-or-add-stack']"
        class="nc-kanban-add-edit-stack-menu-btn nc-toolbar-btn"
        :disabled="isLocked"
      >
        <div class="flex items-center gap-1">
          <MdiPlusCircleOutline />
          <span class="text-capitalize !text-sm font-weight-normal">
            {{ $t('activity.kanban.addOrEditStack') }}
          </span>
          <MdiMenuDown class="text-grey" />
        </div>
      </a-button>
    </div>
    <template #overlay>
      <LazySmartsheetColumnEditOrAddProvider
        v-if="open"
        :column="groupingFieldColumn"
        @submit="handleSubmit"
        @cancel="open = false"
        @click.stop
        @keydown.stop
      />
    </template>
  </a-dropdown>
</template>
