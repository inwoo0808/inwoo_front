<template>
  <div class="container">
    <div class="row">
      <div v-for="(item, i) in $store.state.recordData" :key="i" class="col">
        <div class="card-group d-flex justify-content-center">
          <div class="card col">
            <img :src="item.image" class="card-img-top" alt="..." />
            <div class="card-body">
              <h5 class="card-title">{{ item.title }}</h5>
              <p class="card-text">{{ item.content }}</p>
              <div class="action-buttons" style="margin-left: 17%">
                <router-link
                  :to="{ name: 'detail', params: { id: item.id } }"
                  class="btn btn-link mt-3"
                  style="height: 50%; color: black"
                  >상세보기</router-link
                >
                <router-link
                  :to="{ name: 'edit', params: { id: item.id } }"
                  class="btn btn-link mt-3"
                  style="height: 50%; color: black"
                  >수정</router-link
                >
                <button
                  @click="deleteItem(item)"
                  class="btn btn-link mt-3"
                  style="height: 50%; color: black"
                >
                  삭제
                </button>
              </div>
              <p class="card-text">
                <small class="text-muted">{{ item.date }}</small>
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useStore } from "vuex";
import { ref } from "vue";

const store = useStore();

const viewDetails = (item) => {
  // 상세보기 로직
  alert(`상세보기: ${item.title}`);
  // 예를 들어, 모달을 띄우거나 상세 페이지로 이동할 수 있습니다.
};

const editItem = (item) => {
  // 수정 로직
  const updatedItem = {
    ...item,
    title: prompt("새 제목을 입력하세요", item.title),
  };
  if (updatedItem.title) {
    store.dispatch("updateRecord", updatedItem);
    alert(`수정됨: ${updatedItem.title}`);
  }
};

const deleteItem = (item) => {
  if (confirm(`정말로 ${item.title}을(를) 삭제하시겠습니까?`)) {
    store.dispatch("deleteRecord", item.id);
    alert(`삭제됨: ${item.title}`);
  }
};
</script>

<style lang="scss" scoped>
.avata {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  overflow: hidden;
}

.action-buttons {
  margin-left: auto;
  display: flex;
  gap: 10px;

  .btn {
    cursor: pointer;
  }
}
</style>
