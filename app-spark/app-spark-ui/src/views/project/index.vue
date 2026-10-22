<template>
  <bk-loading
    class="project-page-loading"
    :loading="entering"
    :opacity="0.96"
    :z-index="20"
    color="#f3f5f8"
  >
    <ProjectLayout>
      <bk-resize-layout
        class="project-split"
        placement="left"
        :min="320"
        :max="1000"
        initial-divide="400px"
        :border="false"
        collapsible
        immediate
      >
        <template #aside>
          <InteractionPanel />
        </template>
        <template #main>
          <WorkspacePanel />
        </template>
      </bk-resize-layout>
    </ProjectLayout>
  </bk-loading>
</template>

<script setup lang="ts">
import { onUnmounted, watch } from 'vue';
import { useRoute } from 'vue-router';
import { storeToRefs } from 'pinia';
import Message from '@/common/message';
import InteractionPanel from '@/components/project/interaction/index.vue';
import WorkspacePanel from '@/components/project/workspace/index.vue';
import ProjectLayout from '@/layouts/ProjectLayout.vue';
import { useProjectStore } from '@/store/project';

const route = useRoute();
const projectStore = useProjectStore();
const { entering } = storeToRefs(projectStore);

watch(() => route.params.projectId, async (projectId) => {
  const id = String(projectId || '');
  if (!id) return;
  try {
    await projectStore.enterProject(id);
  } catch (error) {
    Message({
      theme: 'error',
      message: error instanceof Error ? error.message : '进入项目失败',
    });
  }
}, { immediate: true });

onUnmounted(() => {
  projectStore.reset();
});
</script>

<style lang="postcss" scoped>
.project-page-loading {
  height: 100%;
}

.project-page-loading :deep(.bk-loading-mask) {
  z-index: 20;
}

.project-page-loading :deep(.bk-loading-indicator) {
  z-index: 21;
}

.project-split {
  width: 100%;
  height: 100%;
}

.project-split :deep(.bk-resize-layout-aside),
.project-split :deep(.bk-resize-layout-main),
.project-split :deep(.bk-resize-layout-aside-content) {
  height: 100%;
  min-width: 0;
  min-height: 0;
}
</style>
