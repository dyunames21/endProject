<template>
  <div class="orders">
    <div class ="nav">
      <ul class="nav col-12 col-lg-auto me-lg-auto mb-2 justify-content-center mb-md-0">
        <li><router-link to="#" class="nav-link px-2 link-secondary">현재 주문 리스트</router-link></li>
        <li><router-link to="/orderdata" class="nav-link px-2 link-dark">전체 주문 조회</router-link></li>
        <li><router-link to="/admin" class="nav-link px-2 link-dark">메뉴 관리</router-link></li>
      </ul>
    </div>

    <div class="container">

      <div>
        <button class="btn btn-danger btn-sm" @click="stop_auto_reload" v-if="auto_reload == true">
          <i class="fa fa-stop" aria-hidden="true"></i> 정지
        </button>
        <button class="btn btn-primary btn-sm" @click="start_auto_reload" v-if="auto_reload == false">
          <i class="fa fa-play" aria-hidden="true"></i>시작
        </button>
      </div>


      <div class="orderCard" v-for="(o, idx1) in state.orders" :key="idx1">
        <div class="text-center">
          <div class="col">
            <div class="card mb-4 rounded-3 shadow-sm">
              <div class="card-header py-3">
                <h4 class="my-0 fw-normal">{{idx1}}</h4>
              </div>
              <div class="card-body">
                <h2 class="card-title pricing-card-title">{{o.name}}</h2>
                <div>{{o.date}}</div>
                <ul class="list-unstyled mt-3 mb-4">
                  <div v-for="(i, idx2) in o.items" :key="idx2">{{ i.name }} * {{i.many}}</div>
                </ul>
                <button type="button" class="w-100 btn btn-lg btn-outline-primary" @click="OrderCom(o.id)" >완료하기</button>
              </div>
            </div>
          </div>
        </div>
    </div>

  </div>
  </div>
  <button style="margin-left: 30px " type="button" class="btn btn-link" @click="payCheck()">주문 취소하러 가기</button>


</template>


<script>
import {computed, reactive} from "vue";
import axios from "axios";



export default {

  methods: {
    payCheck(){
      window.open("https://classic-admin.iamport.kr/payments", "_blank");
      },
    start_auto_reload() {
      console.log('start!!');
      this.auto_reload = true;
      this.auto_reload_func = setInterval(() => {
        this.reload_order()
      }, this.auto_reload_delay)
    },
    stop_auto_reload() {
      console.log('stop!!');
      this.auto_reload = false;
      clearInterval(this.auto_reload_func);
    },

  },

  setup() {
    const state = reactive({
      orders: [],
    })


    axios.get("/api/seller/orders").then(({data}) => {
      state.orders = [];

      for (let d of data) {
        if (d.items) {
          d.items = JSON.parse(d.items);
        }
        state.orders.push(d);
      }
    })

    const OrderCom = (orderId) => {
      axios.post(`/api/seller/orders/${orderId}`).then(() => {
        window.location.reload(true);
        alert("success")
      })
    }

    const reload_order = () => {
      axios.get("/api/seller/orders").then(({data}) => {
        state.orders = [];

        for (let d of data) {
          if (d.items) {
            d.items = JSON.parse(d.items);
          }
          state.orders.push(d);
        }
      })
    }


    return {state,OrderCom,reload_order }
  },

  data(){
    return{
      auto_reload: false,
      auto_reload_delay:3*1000,
      auto_reload_func: null
    }
  }
}
</script>

<style scoped>
.table {
  margin-top: 30px;
}
.table > tbody {
  border-top: 1px solid #eee;
}
th, td {
  text-align: center;
}

.bd-placeholder-img {
  font-size: 1.125rem;
  text-anchor: middle;
  -webkit-user-select: none;
  -moz-user-select: none;
  user-select: none;
}

.orderCard{
  width: 300px;
  display: inline-block;
  margin: 10px;
}

.nav{
  margin: auto;
  padding-top: 10px;

}


</style>
