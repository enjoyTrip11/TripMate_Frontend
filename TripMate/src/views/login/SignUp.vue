<template>
  <v-card class="mx-auto" elevation="1" width="500" height="620" style="margin-top: 20px;">
    <v-card-title class="py-5 font-weight-black">회원가입</v-card-title>

    <v-card-text>
      입력 폼을 완성해주세요.
    </v-card-text>

    <v-card-text>
      <div class="text-subtitle-2 font-weight-black mb-1">아이디</div>
      <v-text-field v-model="formData.id" label="아이디를 입력하세요" variant="outlined" single-line></v-text-field>

      <div class="text-subtitle-2 font-weight-black mb-1">비밀번호</div>
      <v-text-field v-model="formData.password" label="비밀번호를 입력하세요" variant="outlined" type="password"
        single-line></v-text-field>

      <div class="text-subtitle-2 font-weight-black mb-1">비밀번호 확인</div>
      <v-text-field v-model="formData.passwordConfirm" label="비밀번호를 다시 입력하세요" variant="outlined" type="password"
        single-line></v-text-field>

      <div class="text-subtitle-2 font-weight-black mb-1">닉네임</div>
      <v-text-field v-model="formData.name" label="이름을 입력하세요" variant="outlined" single-line></v-text-field>

      <v-btn @click="submitForm" class="text-none login-btn" color="#87C4FF" size="x-large" variant="flat" block>
        회원가입
      </v-btn>
    </v-card-text>
  </v-card>
</template>

<style>
.login-btn {
  border: 1px solid #000;
  /* 검정색 테두리 추가 */
  display: flex;
  align-items: center;
}

.google-logo {
  margin-right: 8px;
  /* 구글 로고와 텍스트 사이 간격 조정 */
  width: 24px;
  /* 이미지 너비 조정 */
  height: auto;
  /* 높이 자동 조정 */
  max-width: 100%;
  /* 버튼 크기를 넘지 않도록 설정 */
  max-height: 24px;
  /* 버튼 크기를 넘지 않도록 설정 */
}
</style>

<script>
import axios from 'axios';
import { useStore } from 'vuex';
import { mapActions } from 'vuex';
import { useRouter } from 'vue-router';

export default {
  data() {
    return {
      formData: {
        id: '',
        password: '',
        name: ''
      }
    };
  },
  setup() {
    const store = useStore();
    const router = useRouter();
    return { store, router };
  },
  methods: {
    ...mapActions(['saveAccessToken']),
    async submitForm() {
      try {
        const response = await axios.post('http://localhost:8080/user/signup', this.formData);
        console.log('회원가입 성공:', response.data);
        this.router.push('/'); // 회원가입 성공 시 메인페이지로 이동
      } catch (error) {
        console.error('회원가입 실패:', error);
      }
    }
  }
}
</script>
