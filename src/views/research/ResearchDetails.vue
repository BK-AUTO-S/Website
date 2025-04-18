<template>
  <div>
    <Loading :is-loading="isLoading" />
    <PageHeader :subLinks="['Trang chủ']" mainLink="Nghiên cứu" :title="research?.title || ''" />
    <div class="page-content">
      <div class="row">
        <div class="col-12">
          <div v-if="research" class="research-details">
            <div class="research-date mb-3">{{ research.publishedAt }}</div>
            
            <div class="ql-container ql-snow">
              <div class="ql-editor" v-html="research.content"></div>
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
import ResearchService from '@/services/ResearchService';
import { useRoute } from 'vue-router';

useTitle('menu.dashboard');
const { t } = useI18n();
const route = useRoute();
const isLoading = ref(false);
const research = ref(null);

onMounted(async () => {
  try {
    isLoading.value = true;
    const response = await ResearchService.getResearchDetails(route.params.id);
    research.value = response;
  } catch (error) {
    console.error(error);
    alert('Có lỗi xảy ra khi tải nghiên cứu');
  } finally {
    isLoading.value = false;
  }
});
</script>

<style lang="scss" scoped>
.page-header {
  background: linear-gradient(0deg, rgba(0, 0, 0, 0.64), rgba(0, 0, 0, 0.64)),
    url('@/assets/img/background/bg-research.webp');
  background-size: cover;
}

.research-details {
  .research-date {
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
