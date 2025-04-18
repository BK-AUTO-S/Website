<template>
  <div>
    <Loading :is-loading="isLoading" />
    <PageHeader :subLinks="['Trang chủ']" mainLink="Tin tức" :title="news?.title || ''" />
    <div class="page-content">
      <div class="row">
        <div class="col-12">
          <div v-if="news" class="news-details">
            <div class="news-topic mb-3">{{ news.topic }}</div>
            <div class="news-date mb-3">{{ news.publishedAt }}</div>
            <div class="news-summary mb-4">{{ news.summary }}</div>
            <img v-if="news.thumbnail" :src="news.thumbnail" class="news-thumbnail mb-4" />
            
            <QuillEditor
              v-model:content="newsContent"
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
import NewsService from '@/services/NewsService';
import { useRoute } from 'vue-router';

useTitle('menu.dashboard');
const { t } = useI18n();
const route = useRoute();
const isLoading = ref(false);
const news = ref(null);
const newsContent = ref('');

onMounted(async () => {
  try {
    isLoading.value = true;
    const response = await NewsService.getNewsDetails(route.params.id);
    news.value = response;
    newsContent.value = response.content;
  } catch (error) {
    console.error(error);
    alert('Có lỗi xảy ra khi tải tin tức');
  } finally {
    isLoading.value = false;
  }
});
</script>

<style lang="scss" scoped>
.page-header {
  background: linear-gradient(0deg, rgba(0, 0, 0, 0.64), rgba(0, 0, 0, 0.64)),
    url('@/assets/img/background/bg-news.webp');
  background-size: cover;
}

.news-details {
  .news-topic {
    font-weight: bold;
    color: #666;
  }
  .news-date {
    color: #888;
  }
  .news-summary {
    font-style: italic;
  }
  .news-thumbnail {
    max-width: 100%;
    height: auto;
  }
}

:deep(.ql-editor) {
  padding: 0;
}
:deep(.ql-container) {
  border: none !important;
}
</style>
