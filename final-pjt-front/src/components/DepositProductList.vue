<template>

  <div class="right_wrap">
    <div class="notice">
      <h6>세율은 일반과세(15.4%)를 적용했으며, 실제와는 차이가 있을 수 있습니다.</h6>
    </div>

    <!-- <div class="fix_header d-flex justify-content-between p-2"> -->
    <div class="fix_header">
      <table>
        <thead>
          <!-- <tr class="d-flex justify-content-between p-3"> -->
          <tr class="d-flex justify-content-around p-3">
            <th style="width: 100px">예치기간</th>
            <th style="width: 100px">금리</th>
            <th style="width: 80px">지급방식</th>
            <th style="width: 100px">세전이자</th>
            <th style="width: 100px">세후이자</th>
            <th style="width: 150px">은행명</th>
            <th style="width: 200px">상품명</th>
          </tr>
        </thead>
      </table>
    </div>


    <div  class="fix_body d-flex justify-content-around">
      <table >
        <tbody >
          <tr v-for="product in filteredProducts" :key="product.id" class="body_content d-flex p-3 ">
            <td >
              <table>
                <tr v-for="option in product.depositoptions" :key="option.id">
                  <td style="width: 100px">{{ option.save_trm }}</td>
                  <td style="width: 100px">{{ option.intr_rate }}</td>
                  <td style="width: 80px">{{ option.intr_rate_type_nm }}</td>
                  <td v-if="option.intr_rate_type_nm === '단리'" style="width: 100px">
                    {{ formatNumber((payloadD.depositAmount * ( 0.01 * option.intr_rate ) * ( option.save_trm / 12 )).toFixed(0) )}}
                  </td>
                  <td v-else style="width: 100px">
                    {{ formatNumber((payloadD.depositAmount * ((1 + 0.01 * option.intr_rate) ** (option.save_trm / 12)) - payloadD.depositAmount).toFixed(0)) }}
                  </td>
                  <td v-if="option.intr_rate_type_nm === '단리'" style="width: 100px">
                    {{ formatNumber((payloadD.depositAmount * ( 0.01 * option.intr_rate ) * ( option.save_trm / 12 ) * (1 - 0.154)).toFixed(0) )}}
                  </td>
                  <td v-else style="width: 100px">
                    {{ formatNumber(((payloadD.depositAmount * ((1 + 0.01 * option.intr_rate) ** (option.save_trm / 12)) - payloadD.depositAmount) * (1 - 0.154)).toFixed(0) )}}
                  </td>
                  
                  <td style="width: 150px">{{ product.kor_co_nm }}</td>
                  <td style="width: 200px ">
                    <b-button @click="toggleModal(product.fin_prdt_cd)" v-b-modal="product.fin_prdt_cd" class="bg-transparent text-dark border border-gray w-200" >
                      {{ product.fin_prdt_nm }}
                    </b-button>

                    <b-modal :id="product.fin_prdt_cd" @hidden="closeModal(product.fin_prdt_cd)" ok-only header-bg-variant="success">
                      <DepositProductDetail :fin_prdt_cd="product.fin_prdt_cd"/>
                      <template #modal-header>
                        <div class="w-100 d-flex align-content-center" style="color: #fff;">
                          <h4>상품 상세정보</h4>
                        </div>
                      </template>
                    </b-modal>
                  </td>            
                </tr>
              </table>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

</template>

<script>
import DepositProductDetail from './DepositProductDetail.vue'

export default {
  name: 'DepositProductList',
  components: {
    DepositProductDetail,
  },
  data() {
    return {
      modalShow: false,
      openModals: [],
    }
  },
  props: {
    payloadD: Object,
  },
  computed: {
    depositProducts() {
      return this.$store.state.depositProducts
    },
    filteredProducts() {
      if (this.payloadD.selectBank === '전체') {
        return this.depositProducts
      }
      return this.depositProducts.filter(
        (depositProduct) => depositProduct.kor_co_nm === this.payloadD.selectBank
      )
    },
  },
  created() {
    this.getDepositProducts()
  },
  methods: {
    getDepositProducts() {
      this.$store.dispatch('getDepositProducts')
    },
    formatNumber(number) {
      return number.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },

    toggleModal(fin_prdt_cd) {
      if (this.openModals.includes(fin_prdt_cd)) {
        // 이미 열린 모달이 있는 경우 모달을 닫음
        this.closeModal(fin_prdt_cd);
      } else {
        // 열린 모달이 없는 경우 모달을 열고 openModals 배열에 추가
        this.openModal(fin_prdt_cd);
      }
    },

    openModal(fin_prdt_cd) {
      this.openModals.push(fin_prdt_cd);
      this.$bvModal.show(fin_prdt_cd);
    },

    closeModal(fin_prdt_cd) {
      const index = this.openModals.indexOf(fin_prdt_cd);
      if (index > -1) {
        this.openModals.splice(index, 1);
      }
      this.$bvModal.hide(fin_prdt_cd);
    },

  }
}
</script>

<style>
.right_wrap{
    width: 1000px;
    padding-left: 80px;
    padding-top: 10px;
    /* padding-bottom: 90px; */
    min-height: -moz-calc( 100vh - 170px);
    min-height: calc( 100vh - 170px);
}
.fix_header{
  /* background-color: #F3F2E9; */
  background-color: #f3d993;
  border-radius: 10px;
  /* 수정 */
  display: flex;
  justify-content: center;
}
/* 수정 */
.fix_header table {
  width: 100%;
}
.fix_header th {
  text-align: center;
}


.fix_body {
  display: flex;
  justify-content: center;
  margin-top: 10px;
}

.fix_body table {
  width: 100%;
}

.fix_body td {
  text-align: center;
}

</style>