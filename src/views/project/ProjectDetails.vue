<template>
  <div>
    <Loading :is-loading="isLoading" />
    <PageHeader :subLinks="['Trang chủ']" mainLink="Dự án" :title="project?.title || ''" />
    <div class="page-content">
      <div class="row">
        <div class="col-12">
          <div v-if="project" class="project-details">
            <div class="project-date mb-3">{{ project.publishedAt }}</div>
            
            <QuillEditor
              v-model:content="projectContent"
              contentType="html"
              theme="snow"
              :toolbar="false"
              :readOnly="true"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { QuillEditor } from '@vueup/vue-quill';
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
const projectContent = ref('');

onMounted(async () => {
  try {
    isLoading.value = true;
    const response = await ProjectService.getProjectDetails(route.params.id);
    project.value = response;
    projectContent.value = response.content;
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

:deep(.ql-editor) {
  padding: 0;
}
:deep(.ql-container) {
  border: none !important;
}
</style>
