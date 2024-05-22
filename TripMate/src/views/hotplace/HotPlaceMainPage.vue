<template>
  <v-container>
    <!-- 내 장소 섹션 -->
    <v-row v-if="!isLoadingMyPlace && isMyPlaceLoaded && myPlace.length > 0">
      <v-col cols="12">
        <v-row justify="center" class="mb-4">
          <v-col cols="auto">
            <h1 class="display-1" style="text-align: center;">내 장소</h1>
            <hr>
            <p class="mt-3 mb-5">좋아하시는 장소를 모아뒀어요!</p>
          </v-col>   
        </v-row>
        <v-card>
          <v-data-iterator
            :items="myPlace"
            :items-per-page="3"
          >
            <template v-slot:default="{ items }">
              <v-container class="pa-2" fluid>
                <v-row dense>
                  <v-col
                    v-for="(item, index) in items"
                    :key="index"
                    cols="12"
                    md="4"
                  >
                    <place-card
                      :hitCount="item.hits"
                      :imageURL="item.place?.firstImage || '안나오는중'"
                      :title="item.place?.title || '안나오는중'"
                      :addr1="item.place?.addr1 || '안나오는중'"
                      :addr2="item.place?.addr2 || '안나오는중'"
                    />
                  </v-col>
                </v-row>
              </v-container>
            </template>

            <template v-slot:footer="{ page, pageCount, prevPage, nextPage }">
              <div class="d-flex align-center justify-center pa-4">
                <v-btn
                  :disabled="page === 1"
                  density="comfortable"
                  icon="mdi-arrow-left"
                  variant="tonal"
                  rounded
                  @click="prevPage"
                ></v-btn>

                <div class="mx-2 text-caption">
                  Page {{ page }} of {{ pageCount }}
                </div>

                <v-btn
                  :disabled="page >= pageCount"
                  density="comfortable"
                  icon="mdi-arrow-right"
                  variant="tonal"
                  rounded
                  @click="nextPage"
                ></v-btn>
              </div>
            </template>
          </v-data-iterator>
        </v-card>
      </v-col>
    </v-row>

    <!-- 핫플레이스 섹션 -->
    <v-row v-if="isAllPlaceLoaded">
      <v-col cols="12" class="mt-10">
        <v-row justify="center" class="mb-4">
          <v-col cols="auto">
            <h1 class="display-1" style="text-align: center;">핫플레이스</h1>
            <hr>
            <p class="mt-3 mb-5">인기있는 장소들을 둘러보세요!</p>
          </v-col>   
        </v-row>
        <v-card>
          <v-data-iterator
            :items="allPlace"
            :items-per-page="3"
          >
            <template v-slot:default="{ items }">
              <v-container class="pa-2" fluid>
                <v-row dense>
                  <v-col
                    v-for="(item, index) in allPlace"
                    :key="index"
                    cols="12"
                    md="4"
                  >
                    <place-card
                      :hitCount="item.hits"
                      :imageURL="item.place?.firstImage || '안나오는중'"
                      :title="item.place?.title || '안나오는중'"
                      :addr1="item.place?.addr1 || '안나오는중'"
                      :addr2="item.place?.addr2 || '안나오는중'"
                    />
                  </v-col>
                </v-row>
              </v-container>
            </template>

            <template v-slot:footer="{ page, pageCount, prevPage, nextPage }">
              <div class="d-flex align-center justify-center pa-4">
                <v-btn
                  :disabled="page === 1"
                  density="comfortable"
                  icon="mdi-arrow-left"
                  variant="tonal"
                  rounded
                  @click="prevPage"
                ></v-btn>

                <div class="mx-2 text-caption">
                  Page {{ page }} of {{ pageCount }}
                </div>

                <v-btn
                  :disabled="page >= pageCount"
                  density="comfortable"
                  icon="mdi-arrow-right"
                  variant="tonal"
                  rounded
                  @click="nextPage"
                ></v-btn>
              </div>
            </template>
          </v-data-iterator>
        </v-card>
      </v-col>
    </v-row>

    <!-- 로딩 스피너 -->
    <v-row v-if="isLoadingMyPlace || !isAllPlaceLoaded" justify="center" align="center">
      <v-col cols="auto">
        <v-progress-circular indeterminate></v-progress-circular>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import { loadMyHotPlace, loadAllHotPlace } from "@/api/hotplace";
import PlaceCard from '@/components/hotplace/PlaceCard.vue';
import { all } from 'axios';

const userId = 1; // TODO: 현재 userId로 바꾸기
const myPlace = ref([]);
const allPlace = ref([]);
const isMyPlaceLoaded = ref(false);
const isAllPlaceLoaded = ref(false);
const isLoadingMyPlace = ref(true); // 데이터 로딩 중 여부를 나타내는 변수

async function getMyHotPlace() {
  console.log("Load MyPlace...........");
  await loadMyHotPlace(
    userId,
    ({ data }) => {
      console.log("Success!....MyPlace:", data);
      myPlace.value = data;
      isMyPlaceLoaded.value = true;
      isLoadingMyPlace.value = false; // 데이터 로딩이 완료되면 로딩 상태를 false로 변경
    }, 
    ({ error }) => {
      console.log("Fail to Load MyPlace.....", error);
      isMyPlaceLoaded.value = true; // 오류가 발생해도 로딩 플래그는 true로 설정
      isLoadingMyPlace.value = false; // 데이터 로딩이 실패해도 로딩 상태를 false로 변경
    }
  );
}

async function getAllHotPlace() {
  console.log("Load All HotPlace...........");
  await loadAllHotPlace(
    ({ data }) => {
      console.log("Success!.....HotPlace:", data);
      allPlace.value = data;
      isAllPlaceLoaded.value = true;
    }, 
    ({ error }) => {
      console.log("Fail to Load All HotPlace.....", error);
      isAllPlaceLoaded.value = true; // 오류가 발생해도 로딩 플래그는 true로 설정
    }
  );
}

onMounted(async () => {
  await getMyHotPlace();
  await getAllHotPlace();
});
</script>

<style>
.scroll-container {
  display: flex;
  overflow-x: auto;
  scroll-behavior: smooth;
}
</style>