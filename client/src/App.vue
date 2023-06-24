<template>
        <!-- Navigation-->
        <nav class="navbar navbar-expand-lg navbar-light bg-light">
            <div class="container px-4 px-lg-5">
                <a class="navbar-brand" href="#">Librairie Valentin</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation"><span class="navbar-toggler-icon"></span></button>
                <div class="collapse navbar-collapse" id="navbarSupportedContent">
                    <ul class="navbar-nav me-auto mb-2 mb-lg-0 ms-lg-4">
                        <li class="nav-item"><RouterLink class="nav-link active" aria-current="page" to="/">Accueil</RouterLink></li>
                        <li class="nav-item"><RouterLink class="nav-link" to="/about">A propos de nous</RouterLink></li>
                        <li class="nav-item"><RouterLink class="nav-link" :to="{name: 'add-product'}">Add</RouterLink></li>
                    </ul>
                        <button class="btn btn-outline-dark" @click="toggleSideBar">
                            <i class="bi-cart-fill me-1"></i>
                            Cart
                            <span class="badge bg-dark text-white ms-1 rounded-pill">{{ totalQuantity }}</span>
                        </button>
                </div>
            </div>
        </nav>
        <!-- Header-->
        <header class="bg-light py-5">
           <img class="card-img-top" :src="require(`@/assets/img/visuel-menu.jpg`)" alt="..." />
        </header>
        <RouterView
        :inventory="inventory"
        :add="addToCart"
        :addInv="addInventory"
        :updateInv="updateInventory"
        :removeInv = "removeInventory"
        :remove = "removeItem"
        />
        <Sidebar
        v-if="showSideBar"
        :toggle="toggleSideBar"
        :cart="cart"
        :inventory="inventory"
        :remove="removeItem"
        />
        <!-- Footer-->
        <footer class="py-5 bg-footer">
            <div class="container"><p class="m-0 text-center text-white">Copyright &copy; Librairie Valentin 2023</p></div>
        </footer>
</template>

<script>
import Sidebar from '@/components/SideBar.vue'
import ProductDataService from '@/services/ProductDataService'
export default {
  components: {
    Sidebar
  },
  data () {
    return {
      showSideBar: false,
      inventory: [],
      cart: {}
    }
  },
  methods: {
    toggleSideBar () {
      this.showSideBar = !this.showSideBar
    },
    addToCart (product, index) {
      if (!this.cart[product]) this.cart[product] = 0
      this.cart[product] += this.inventory[index].quantity
      console.log(this.cart[product])
    },
    removeItem (name) {
      delete this.cart[name]
    },
    addInventory (data) {
      this.inventory.push(data)
    },
    updateInventory (index, data) {
      this.inventory[index].name = data.name
      this.inventory[index].photo = data.photo
      this.inventory[index].price = data.price
      this.inventory[index].description = data.description
      this.inventory[index].type = data.type
    },
    removeInventory (index) {
      this.inventory.splice(index, 1)
    }
  },
  computed: {
    totalQuantity () {
      return Object.values(this.cart).reduce((acc, curr) => {
        return acc + curr
      }, 0)
    }
  },
  mounted () {
    ProductDataService.getAll()
      .then(response => {
        this.inventory = response.data
      })
  }
}
</script>
