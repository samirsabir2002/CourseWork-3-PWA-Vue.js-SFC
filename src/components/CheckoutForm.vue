<template>
  <section>
    <div class="container my-5">
      <div class="row d-flex justify-content-center align-items-center">
        <div class="col">
          <div class="card">
            <div class="card-body p-4">
              <div class="row">
                <div class="col-lg-7">
                  <h5 class="mb-3">
                    <a href="#!" class="text-body" @click="showhomePageBtn">
                      <i class="fas fa-long-arrow-alt-left me-2"></i>
                      Continue shopping
                    </a>
                  </h5>
                  <hr>
                  <div v-if="cart.length">
                    <div class="d-flex justify-content-between align-items-center mb-4">
                      <div>
                        <p class="mb-1">Shopping cart</p>
                        <p class="mb-0">You have {{ CartItemCount }} items in your cart</p>
                      </div>
                    </div>
                    <div v-for="item in groupedCart" :key="item.id">
                      <div class="card mb-3">
                        <div class="card-body">
                          <div class="d-flex justify-content-between">
                            <div class="d-flex flex-row align-items-center">
                              <div>
                                <img :src="ServerImage(item.picture)" class="img-fluid rounded-3" alt="Shopping item" style="width: 65px;">
                              </div>
                              <div class="ms-3">
                                <h5>{{ item.subject }}</h5>
                              </div>
                            </div>
                            <div class="d-flex flex-row align-items-center">
                              <div style="width: 50px;">
                                <h5 class="fw-normal mb-0">{{ item.quantity }}</h5>
                              </div>
                              <div style="width: 80px;">
                                <h5 class="mb-0">{{ (item.price * item.quantity).toFixed(2) }} AED</h5>
                              </div>
                              <a v-on:click="removeFromCart(item.id)" href="#!" style="color: #cecece;">
                                <i class="fas fa-trash-alt"></i>
                              </a>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div v-else>
                    <!-- Message when cart is empty -->
                    <p>Your cart is empty.</p>
                  </div>
                </div>
                <div class="col-lg-5">
                  <div class="card bg-primary text-white rounded-3">
                    <div class="card-body">
                      <div class="d-flex justify-content-between align-items-center mb-4">
                        <h5 class="mb-0">User details</h5>
                        <div class="bg-white rounded">
                          <img src="Images/visualhunter-1d714a7743.png" class="img-fluid rounded-3" style="width: 45px;" alt="Avatar">
                        </div>
                      </div>
                      <form class="mt-4">
                        
                        <p v-if="!isFormValid" class="text-danger" >Please fill in all fields.</p>
                          <label class="form-label text-white" for="firstNameInput">First Name</label>
                          <input type="text" id="firstNameInput" class="form-control form-control-lg" placeholder="First Name" v-model.trim="order.firstName" required>

                          <label class="form-label text-white" for="phoneInput">Phone Number</label>
                          <input type="tel" id="phoneInput" class="form-control form-control-lg" v-model.number="order.phone" required placeholder="Phone Number">
                      </form>
                      <hr class="my-4">
                      <div class="d-flex justify-content-between mb-4">
                        <p class="mb-2">Total (Incl. taxes)</p>
                        <p class="mb-2">AED {{ cartTotal }}</p>
                      </div>
                      <button :disabled="!isFormValid" data-mdb-ripple-init data-mdb-modal-init type="button" data-mdb-toggle="modal" data-mdb-target="#placeordermodal" class="btn btn-info btn-block btn-lg" @click="submitOrder">
                        <div class="d-flex justify-content-between">
                          <span>AED {{ cartTotal }}</span>
                          <span>
                            Checkout
                            <i class="fas fa-long-arrow-alt-right ms-2"></i>
                          </span>
                        </div>
                      </button>
                    
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Order confirmation modal -->
        <div class="modal fade" id="placeordermodal" tabindex="-1" aria-labelledby="placeordermodalLabel" aria-hidden="true">
          <div class="modal-dialog">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title" id="placeordermodalLabel">Order Confirmation</h5>
                <button type="button" class="btn-close" data-mdb-dismiss="modal" aria-label="Close"></button>
              </div>
              <div class="modal-body">Order Confirmed Successfully</div>
              <div class="modal-footer">
                <button type="button" class="btn btn-primary" data-mdb-dismiss="modal" @click="showhomePageBtn">Close</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: "CheckoutForm",
  props: {
    cart: { type: Array, required: true },
    saveOrder: { type: Function, required: true },
    showhomePageBtn: {
      type: Function,
      required: true
    },
 
  },
  data() {
    return {
      order: {
        firstName: '',
        phone: ''
      },
      isFormValid: false
    }
  },
  watch: {
    order: {
      handler() {
        this.validateForm();
      },
      deep: true
    }
  },
  computed: {
    groupedCart() {
  let grouped = {};
  this.cart.forEach(item => {
    if (!grouped[item.id]) {
      grouped[item.id] = { ...item, quantity: 0 };
    }
    grouped[item.id].quantity += 1;

  });
      console.log("🚀 ~this.cart:", Object.values(grouped));
      return Object.values(grouped);
},
    cartTotal() {
      return this.groupedCart.reduce((sum, item) => sum + item.price * item.quantity, 0).toFixed(2);
    },
    CartItemCount() {
      return this.cart.length;
    },
  },
  methods: {
ServerImage(img) {
      // Construct the full URL for an image
      const NodeServerUrl = "https://coursework-2-after-school.onrender.com";
      const Image = img.split("/").pop().trim();
      const FullPath = NodeServerUrl + "/" + Image;
      return FullPath;
    },
    validateForm() {
      this.isFormValid = !!this.order.firstName && !!this.order.phone;
    },
    submitOrder() {
      if (this.isFormValid) {
 this.order.lessonItems = this.groupedCart.map(item => ({
      id: item._id,
      quantity: item.quantity
 }));
        this.saveOrder(this.order);
      }
    },
    CartCount(product) {
      this.$emit('cart-count', product);
    },
    removeFromCart(lessonId) {
      this.$emit('remove-item-from-cart', lessonId);
    },
    itemTotalPrice(item) {
      return (item.price * item.availability).toFixed(2);
    },
    getImageUrl(item) {
      return item.picture || 'path/to/your/fallback/image.png'; // Provide a fallback image path
    },
    
  },

};
</script>

<style scoped>
/* Add your custom styles here */
</style>
