<template>
  <div class="right_wrap">
    <div class="notice">
      <h6>세율은 일반과세(15.4%)를 적용했으며, 실제와는 차이가 있을 수 있습니다.</h6>
      <h6>자유적립식도 정액적립식과 같은 월납입금액으로 가정하고 계산했습니다.</h6>
    </div>
      <div class="fix_header d-flex justify-content-between p-3">
        <!-- <table class="main_table"> -->
          <!-- <colgroup>
            <col width="100px">
            <col width="100px">
            <col width="100px">
            <col width="200px">
            <col width="320px">      
          </colgroup> -->
        <table>
          <thead>
          <th style="width: 100px">예치기간</th>
          <th style="width: 100px">금리</th>
          <th style="width: 120px">적립방식</th>
          <th style="width: 100px">지급방식</th>
          <th style="width: 110px">세전이자</th>
          <th style="width: 110px">세후이자</th>
          <th style="width: 130px">은행명</th>
          <th style="width: 270px">상품명</th>
          </thead>
        </table>
      </div> 

      <div class="fix_body d-flex justify-content-between">

        <!-- <table class="main_table content"> -->
            <!-- <colgroup>
              <col width="100px">
              <col width="100px">
              <col width="100px">
              <col width="100px">
              <col width="100px">
            </colgroup> -->
          <table>
            <tbody >

              <tr v-for="product in filteredProducts" :key="product.id" class="body_content d-flex p-3 mt-1">
                <td>
                  <table>
                    <tr v-for="option in product.savingoptions" :key="option.id">
                      <td style="width: 100px">{{ option.save_trm }}</td>
                      <td style="width: 100px">{{ option.intr_rate }}</td>
                      <td style="width: 120px">{{ option.rsrv_type_nm }}</td>
                      <td style="width: 100px">{{ option.intr_rate_type_nm }}</td>
                      <td v-if="option.intr_rate_type_nm === '단리'" style="width: 100px">
                        {{ formatNumber((payloadS.depositAmount * ( 0.01 * option.intr_rate ) * ( option.save_trm / 12 )).toFixed(0) )}}
                      </td>
                      <td v-else style="width: 110px">
                        {{ formatNumber((payloadS.depositAmount * ((1 + 0.01 * option.intr_rate) ** (option.save_trm / 12)) - payloadS.depositAmount).toFixed(0)) }}
                      </td>
                      <td v-if="option.intr_rate_type_nm === '단리'" style="width: 100px">
                        {{ formatNumber((payloadS.depositAmount * ( 0.01 * option.intr_rate ) * ( option.save_trm / 12 ) * (1 - 0.154)).toFixed(0) )}}
                      </td>
                      <td v-else style="width: 110px">
                        {{ formatNumber(((payloadS.depositAmount * ((1 + 0.01 * option.intr_rate) ** (option.save_trm / 12)) - payloadS.depositAmount) * (1 - 0.154)).toFixed(0) )}}
                      </td>
                      
                      <td style="width: 120px">{{ product.kor_co_nm }}</td>
                      <td style="width: 260px ">
                        <b-button @click="toggleModal(product.fin_prdt_cd)" v-b-modal="product.fin_prdt_cd" class="bg-transparent text-dark border border-gray w-200" >
                          {{ product.fin_prdt_nm }}
                        </b-button>

                        <b-modal :id="product.fin_prdt_cd" @hidden="closeModal(product.fin_prdt_cd)" ok-only header-bg-variant="success">
                          <SavingProductDetail
                            :fin_prdt_cd="product.fin_prdt_cd" 
                          />
                          <template #modal-header>
                            <div class="w-100 d-flex align-content-center" style="color: #fff;">
                              <h4>상품 상세정보</h4>
                            </div>
                          </template>
                        </b-modal>
                      </td>            
                    </tr>
                  </table>
                  <!-- <div class="row" v-for="option in product.savingoptions" :key="option.id">                 -->
                    <!-- <div class="col" style="width: 100px">{{ option.save_trm }}</div> -->
                    <!-- <div class="col" style="width: 100px">{{ option.intr_rate }}</div> -->
                    <!-- <div class="col" style="width: 100px">{{ option.rsrv_type_nm }}</div> -->
                    <!-- <div class="col" style="width: 100px">{{ option.intr_rate_type_nm }}</div> -->

                    <!-- <div v-if="option.intr_rate_type_nm === '단리'" class="col" style="width: 100px">{{ ((payloadS.depositAmount * ( 0.01 * option.intr_rate ) * ( option.save_trm + 1 ) * ( option.save_trm ) / 2) / 12).toFixed(0) }}</div> -->
                    <!-- <div v-else class="col" style="width: 100px">{{ (payloadS.depositAmount * option.save_trm * 0.01 * option.intr_rate * 0.55).toFixed(0) }}</div> -->
                    
                    <!-- <div v-if="option.intr_rate_type_nm === '단리'" class="col" style="width: 100px">{{ (((payloadS.depositAmount * ( 0.01 * option.intr_rate ) * ( option.save_trm + 1 ) * ( option.save_trm ) / 2) / 12) * (1 - 0.154)).toFixed(0) }}</div> -->
                    <!-- <div v-else class="col" style="width: 100px">{{ ((payloadS.depositAmount * option.save_trm * 0.01 * option.intr_rate * 0.55) * (1 - 0.154)).toFixed(0) }}</div> -->
                    <!-- <td style="width: 100px">{{ option.intr_rate }} </td>
                    <td style="width: 100px">{{ option.intr_rate_type_nm }}</td>
                    <td style="width: 100px">세전이자</td>
                    <td style="width: 100px">세후이자</td> -->
                  <!-- </div>             -->
                </td>
               
              </tr>

            </tbody>
          </table>
        <!-- </table> -->
      </div>
  </div>
</template>

<script>
import SavingProductDetail from './SavingProductDetail.vue'

export default {
  name: 'SavingProductList',
  components: {
    SavingProductDetail,
  },
  data() {
    return {
      openModals: [],
    }
  },
  props: {
    payloadS: Object,
  },
  computed: {
    savingProducts() {
      return this.$store.state.savingProducts
    },
    filteredProducts() {
      if (this.payloadS.selectBank === '전체') {
        return this.savingProducts
      }
      return this.savingProducts.filter(
        (savingProduct) => savingProduct.kor_co_nm === this.payloadS.selectBank
      )
    }
  },
  created() {
    this.getSavingProducts()
  },
  methods: {
    getSavingProducts() {
      this.$store.dispatch('getSavingProducts')
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

</style>