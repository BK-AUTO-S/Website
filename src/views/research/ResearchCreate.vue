<template>
  <div>
    <Loading :is-loading="isLoading" />
    <PageHeader :subLinks="['Trang chủ']" mainLink="Nghiên cứu" title="Tạo nghiên cứu mới" />
    <div class="page-content">
      <div class="row">
        <div class="col-12">
          <a-input
            class="mb-3"
            v-model:value="formState.title"
            addonBefore="Tiêu đề"
            placeholder="v.d Nghiên cứu về xe tự lái"
          />

          <div class="content-writer mt-3">
            <QuillEditor
              v-model:content="researchContent"
              contentType="html"
              theme="snow"
              toolbar="full"
              @textChange="onEditorChange"
              placeholder="Nhập nội dung"
            />
          </div>
        </div>
        <div class="action mt-4 text-center">
          <a-button type="primary" @click="submitResearch"> Đăng nghiên cứu</a-button>
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
import { reactive } from 'vue';
import ResearchService from '@/services/ResearchService';
import router from '@/router';

useTitle('menu.dashboard');
const { t } = useI18n();
const isLoading = ref(false);

const researchContent = ref('');
const editorContent = ref('');

const formState = reactive({
  title: '',
});

const onEditorChange = ({ editor }) => {
  editorContent.value = editor.getHTML();
};

const submitResearch = async () => {
  try {
    const data = {
      title: formState.title,
      content: editorContent.value || '',
    };
    const response = await ResearchService.createResearch(data);
    alert('Tạo nghiên cứu ' + response.title + ' thành công ');
    router.push('/research/manage');
  } catch (error) {
    alert('Có lỗi xảy ra');
  }
};
</script>

<style lang="scss" scoped>
.page-header {
  background: linear-gradient(0deg, rgba(0, 0, 0, 0.64), rgba(0, 0, 0, 0.64)),
    url('@/assets/img/background/bg-research.webp');
  background-size: cover;
}
</style>
