<template>
  <div>
    <div class="detail-container">
      <h3 class="text-start"><strong>{{ product?.fin_prdt_nm }}</strong></h3>
      <div class="article-info">
          <table class="table mt-4">
            <tbody>
              <tr class= "header-title text-start">
                <th style="width: 10%">은행명</th>
                <td colspan="3" style="width:90%">
                  {{ product?.kor_co_nm }}
                </td>
              </tr>
              <tr class="text-start">
                <th style="width:20%">가입제한*</th>
                <td style="width:20%">{{ product?.join_deny }}</td>
                <th style="width:20%">가입방법</th>
                <td style="width:30%">{{ product?.join_way }}</td>
              </tr>
              <tr class="height=100%" >
                <td colspan="4" class="p-3 text-start" style="height: 300px">
                  {{ product?.etc_note }}
                </td>
              </tr>
            </tbody>
          </table>
        <div class="d-flex m-4">
          <h6>* 1 - 제한없음, 2 - 서민 전용, 3 - 일부 제한</h6>
        </div>
      </div>
    </div>
    <!-- <div class="d-flex flex-column mb-3">
      <div class="p-2 text-start">
        <p>금융회사명 : {{ product?.kor_co_nm }}</p>
      </div>
      <div class="p-2 text-start">
        <p>상품명 : {{ product?.fin_prdt_nm }}</p>
      </div>
      <div class="p-2 text-start">
        <p>가입제한* : {{ product?.join_deny }}</p>
      </div>
      <div class="p-2 text-start">
        <p>가입방법 : {{ product?.join_way }}</p>
      </div>
      <div class="p-2 text-start">
        <p>우대조건 : {{ product?.spcl_cnd }}</p>
      </div>
    </div>
    <div class="d-flex m-4">
      <h6>* 1 - 제한없음, 2 - 서민 전용, 3 - 일부 제한</h6>
    </div> -->
    <div>
      <b-button v-if="!likeStatusD" pill variant="success" @click="likeDeposit">관심상품 등록</b-button>
      <b-button v-else pill @click="likeDeposit">관심상품 등록 해제</b-button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
const API_URL = 'http://127.0.0.1:8000'

export default {
  name: 'DepositProductDetail',
  props: {
    fin_prdt_cd: String,
  },
  data() {
    return {
      product: null,
      likeStatusD: false, // likeStatusD의 초기값을 false로 설정
    }
  },
  created() {
    this.getProductDetail()
    this.retrieveLikeStatusD()
  },
  methods: {
    getProductDetail() {
      axios({
        method: 'get',
        url: `${API_URL}/finlife/deposit-products/${this.fin_prdt_cd}/`,
      })
        .then((res) => {
          this.product = res.data
        })
        .catch(err => console.log(err))
    },

    retrieveLikeStatusD() {
      const likeStatusD = localStorage.getItem(`likeStatusD_${this.fin_prdt_cd}`)
      if (likeStatusD !== null) {
        this.likeStatusD = JSON.parse(likeStatusD)
      }
    },

    saveLikeStatusD() {
      localStorage.setItem(`likeStatusD_${this.fin_prdt_cd}`, JSON.stringify(this.likeStatusD))
    },

    likeDeposit() {
      axios({
        method: 'post',
        url: `${API_URL}/finlife/deposit-products/${this.fin_prdt_cd}/likes/`,
        headers: {
          Authorization: `Token ${this.$store.state.token}`
        },
      })
        .then(() => {
          if (!this.likeStatusD) {
            alert('관심상품으로 등록되었습니다.')
            this.likeStatusD = true
          } else {
            alert('관심상품 등록이 해제되었습니다.')
            this.likeStatusD = false
          }

          this.saveLikeStatusD() // like 상태 변경 후 저장
        })
        .catch(err => console.log(err))
    },

    goBack() {
        this.$router.go(-1)
    }
  },
}
</script>

<style>
div h6 {
  font-size: 0.8rem;
}

</style>