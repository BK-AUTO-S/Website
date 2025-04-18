<template>
  <div>
    <Loading :is-loading="isLoading" />
    <PageHeader :subLinks="['Trang chủ']" mainLink="Dự án" :title="project?.title || ''" />
    <div class="page-content">
      <div class="row">
        <div class="col-12">
          <div v-if="project" class="project-details">
            <div class="project-date mb-3">{{ project.publishedAt }}</div>
            
            <div class="ql-container ql-snow">
              <div class="ql-editor" v-html="project.content"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import '@vueup/vue-quill/dist/vue-quill.snow.css';
import Loading from '@/components/Loading.vue';
import PageHeader from '@/components/PageHeader.vue';
import { useTitle } from '@/composables/common.js';
import { onMounted, ref } from 'vue';
import { useI18n } from 'vue-i18n';
import ProjectService from '@/services/ProjectService';
import { useRoute } from 'vue-router';

useTitle('menu.dashboard');
const { t } = useI18n();
const route = useRoute();
const isLoading = ref(false);
const project = ref(null);

onMounted(async () => {
  try {
    isLoading.value = true;
    const response = await ProjectService.getProjectDetails(route.params.id);
    project.value = response;
  } catch (error) {
    console.error(error);
    alert('Có lỗi xảy ra khi tải dự án');
  } finally {
    isLoading.value = false;
  }
});
</script>

<style lang="scss" scoped>
.page-header {
  background: linear-gradient(0deg, rgba(0, 0, 0, 0.64), rgba(0, 0, 0, 0.64)),
    url('@/assets/img/background/bg-project.webp');
  background-size: cover;
}

.project-details {
  .project-date {
    color: #888;
  }
}

:deep(.ql-container) {
  border: none !important;
  
  .ql-editor {
    padding: 0;
    min-height: auto !important;
    pointer-events: none;
  }
}
</style>
