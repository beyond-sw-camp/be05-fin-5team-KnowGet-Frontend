<template>
  <q-page class="page-wrapper">
    <q-card class="page-card">
      <q-card-section>
        <q-btn
          flat
          round
          dense
          icon="arrow_back"
          @click="goBack"
          class="back-btn"
        />
        <div class="text-h5">성공사례 상세</div>
        <br/>
        <q-input v-model="successCase.title" label="제목" outlined readonly/>
        <div v-html="successCase.content" class="content-container"></div>
        <q-btn
          label="삭제"
          color="negative"
          @click="openDeleteDialog"
          class="delete-button"
        />
      </q-card-section>
    </q-card>

    <q-dialog v-model="isDeleteDialogOpen">
      <q-card>
        <q-card-section>
          <div class="text-h6">삭제 확인</div>
          <p>정말로 이 글을 삭제하시겠습니까?</p>
        </q-card-section>
        <q-card-actions align="right">
          <q-btn flat label="예" color="primary" @click="confirmDelete"/>
          <q-btn
            flat
            label="아니요"
            color="primary"
            @click="closeDeleteDialog"
          />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script setup>
import {onMounted, ref} from 'vue';
import {customApi} from 'src/boot/axios';
import {useRoute, useRouter} from 'vue-router';
import {Notify} from 'quasar';

const router = useRouter();
const route = useRoute();
const successCase = ref({});
const isDeleteDialogOpen = ref(false);

const getSuccessCaseDetail = async () => {
  try {
    const response = await customApi.get(`/success-case/${route.params.id}`);
    successCase.value = response.data;
  } catch (error) {
    console.error('Error fetching success case detail:', error);
    Notify.create({
      type: 'negative',
      message: '성공사례를 불러오는 데 실패했습니다.',
      position: 'top',
    });
  }
};

const deleteSuccessCase = async () => {
  try {
    await customApi.delete(`/success-case/${route.params.id}`);
    Notify.create({
      type: 'positive',
      message: '성공사례가 삭제되었습니다.',
      position: 'top',
    });
    router.push({path: '/mypage'});
  } catch (error) {
    console.error('Error deleting success case:', error);
    Notify.create({
      type: 'negative',
      message: '성공사례 삭제에 실패했습니다.',
      position: 'top',
    });
  }
};

const openDeleteDialog = () => {
  isDeleteDialogOpen.value = true;
};

const closeDeleteDialog = () => {
  isDeleteDialogOpen.value = false;
};

const confirmDelete = () => {
  isDeleteDialogOpen.value = false;
  deleteSuccessCase();
};

const goBack = () => {
  router.go(-1);
};

onMounted(getSuccessCaseDetail);
</script>

<style scoped>
.page-wrapper {
  display: flex;
  justify-content: center;
  padding: 20px;
  box-sizing: border-box;
}

.page-card {
  width: 100%;
  max-width: 1200px;
  margin: 20px auto;
  padding: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  box-sizing: border-box;
}

.content-input {
  width: 100%;
  min-height: 150px;
  margin-top: 20px;
}

.content-container {
  width: 100%;
  min-height: 150px;
  margin-top: 20px;
  padding: 10px;
  background: #f5f5f5;
  border-radius: 5px;
}

.delete-button {
  margin-top: 20px;
}

.back-btn {
  margin-bottom: 20px;
}
</style>
