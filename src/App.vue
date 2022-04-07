<script>
import { ref, computed, watch } from 'vue';
import cartItem from './cartItem.vue'

export default {
  components: {
    cartItem
  },
  setup(){
    const itemList = ref([]);
    fetch('./itemList.json')
      .then(d => d.json())
      .then(res => {
        itemList.value = res;
      })

    const cart = ref([]);
    const discountFlag = ref(false)

     const totalPrice = computed(() =>{
      return cart.value.reduce((previousValue, currentValue) =>{
        return previousValue + currentValue.amount * currentValue.price
      }, 0);
    });

    const addItem = (itemName, itemPrice) => {
      const targetObj = cart.value.find(d => d.name === itemName);

      if(targetObj){
        targetObj.amount++
      }
      else{
        cart.value.unshift({
          name:itemName,
          amount: 1,
          price: itemPrice,
        })
      }
    }

    

   

    const removeItem = itemIdx => {
      cart.value.splice(itemIdx, 1)
      //cart.value = cart.value.fillter(d => d.name !=== itemName)//
    }

    const updateAmount = (item) => {
      const targetObj = cart.value.find(d => d.name === item.name)
      targetObj.amount = item.amount
    }

    const cleanCart = () => {
      cart.value = []
    }

    return{
      addItem,
      itemList,
      totalPrice,
      cart,
      removeItem,
      discountFlag,
      updateAmount,
      cleanCart
    }
  }
}
</script>

<template lang="">
  <div>
    <main>
      <header>
        <nav class="navbar navbar-expand-lg navbar-light bg-light">
          <div class="container-fluid">
            <a class="navbar-brand" href="/"
              ><i class="fas fa-gem"></i> 賺很大商店</a
            >
            <div class="collapse navbar-collapse" id="navbarNav">
              <ul class="navbar-nav">
                <li class="nav-item">
                  <a class="nav-link active" aria-current="page" href="#"
                    >商品</a
                  >
                </li>
                <li class="nav-item">
                  <a 
                  @click.prevent="discountFlag = !discountFlag" :class="discountFlag ? 'red' : ''" class="nav-link" href="#">當日特價</a>
                  
                </li>
              </ul>
            </div>
          </div>
        </nav>
      </header>

      <section class="container mt-4">
        <div class="items row">
          <div class="col-sm-2" v-for="item in itemList">
            <div class="card">
              <img :src="item.cover" class="card-img-top" alt="" />
              <div class="card-body">
                <h5 class="title card-title fs-6 fw-light">{{ item.name }}</h5>
                <p class="price">$ {{item.price}}</p>
                <button class="btn btn-sm btn-warning fw-light " @click="addItem(item.name, item.price)">
                  <i class="fas fa-cat"></i>
                </button>
              </div>
            </div>
          </div>
          
          
          
          
          
        </div>
        <hr />

        <section class="cart" v-if="cart.length > 0">
          <h2>購物車</h2>
          <table class="table">
            <thead>
              <tr>
                <th scope="col">項目</th>
                <th scope="col">數量</th>
                <th scope="col">單價</th>
                <th scope="col">小計</th>
                <th scope="col"></th>
              </tr>
            </thead>
            <tbody>
              <cartItem
              v-for="i in cart"
              :name="i.name"
              :price="i.price"
              :amount="i.amount"
              @update="updateAmount"
              @remove="removeItem(i.name)"/>
             
            </tbody>
            <tfoot>
              <tr>
                <td colspan="2"></td>
                <td>總價</td>
                <td>${{ discountFlag ? totalPrice * 0.8 : totalPrice }}
                  <span
                  v-if ="discountFlag">特價中，打八折</span>
                </td>
                <td></td>
              </tr>
            </tfoot>
          </table>
          <button class="btn btn-lg btn-success" @click="cleanCart">
            <i class="fas fa-baby-carriage"></i> 清空購物車
          </button>
        </section>
      </section>
    </main>
  </div>
</template>



<style lang="scss">
.red {
  color: red !important;
}
  header {
  nav {
    ul {
      margin: 0;

      li {
        display: inline;

        a {
          display: inline-block;
          padding: 1em;
          text-decoration: none;

          &:hover {
            background-color: rgba(180, 180, 180, .3)
          }
        }
      }
    }
  }
}
</style>
