<template>
  <!-- projects section -->
  <section id="services" class="services">
    <div class="price-title">
      <div class="container text-center">
        <p class="text-white p-2">Prices</p>
      </div>
    </div>
    <div class="container justify-content-center">
      <div class="pb-5 cont">
        <!-- SORT/FILTER/ADD -->
        <div>
          <div class="">
            <div style="" class="sort-content justify-content-center row">
              <div class="col-10 col-md-8 col-lg-3">
                <h6>SortByTitle:</h6>
                <select
                  class="form-select text-center"
                  name=""
                  id="sortPrice"
                  v-model="laundry_services" @change="sortTitle(laundy_services)"
                  
                >
                <option value="">ALL</option>
                  <option value="asc">Ascending</option>
                  <option value="desc">Descending</option>
                </select>
              </div>
              <div class="mx-1 col-10 col-md-8 col-lg-3">
                <h6>SortByPrice:</h6>
                <select
                  class="form-select text-center"
                  name=""
                  id="sortPrice"
                  v-model="service_price" @change="sortPrice(service_price)"
                >
                <option value="">ALL</option>
                  <option value="asc">Ascending</option>
                  <option value="desc">Descending</option>
                </select>
              </div>
              <div class="col-10 col-md-8 col-lg-3" id="main">
                <h6>Filter:</h6>
                <label>
                  <input
                    placeholder="Search service"
                    type="text"
                    v-model="search"
                  />
                </label>
                <!-- <div v-for="customer in filteredCustomers">
                  <span>{{ customer.firstName }} {{ customer.lastName }}</span>
                </div> -->
              </div>
            </div>
          </div>
        </div>
        <!-- CARDS -->
        <section id="products" class="products">
          <div class="container">
            <div v-if="services" class="pb-5 cont justify-content-center">
              <div class="row col-lg-12 proji" style="row-gap: 30px">
                <div
                  v-for="service in filteredServices"
                  :key="service._id"
                  class="col-lg-3 col-sm-6 col-6 col-md-4"
                  style="display: flex; justify-content: center"
                >
                  <div class="card shadow ani-card">
                    <img
                      :src="service.service_image"
                      class="card-img-top"
                      alt="..."
                    />
                    <div class="card-body">
                      <h4 class="card-title text-black">
                        {{ service.laundry_service }}
                      </h4>
                      <p class="card-text text-black">
                        R{{ service.service_price }} per basket
                      </p>
                    </div>

                    <div class="card-body card-body-button text-center">
                      <button type="button" class="btn border-dark card-btn">
                        <router-link :to="{name: 'ServiceDetails', params: {id: service._id}}">More details</router-link>
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div v-else class="load">
              <div class="loading"></div>
            </div>
          </div>
        </section>
      </div>
      <!-- <div class="loading" v-else></div> -->
    </div>
  </section>
</template>

<script>
import axios from "axios";
export default {
  components: {},
  data() {
    return {
      services: null,
      search: "",
      filteredServices: null,
      laundry_service: "",
      service_price: ""
    };
  },

  // GETTING SERVICES
  mounted() {
    fetch("https://laundry-villa.herokuapp.com/services")
      .then((res) => res.json())
      .then((data) => {
        console.log(data);
        this.services=data;
        this.filteredServices = data;
      });
  },
  methods: {
    // SORT
    sortPrice(dir) {
      this.filteredServices = this.filteredServices.sort(
        (a, b) => a.service_price- b.service_price
      );
      if (dir == "desc") this.filteredServices.reverse();
    },
    sortTitle(dir) {
      this.filteredServices = this.filteredServices.sort((a, b) => {
        if (a.laundry_service < b.laundry_service) {
          return -1;
        }
        if (a.laundry_service > b.laundry_service) {
          return 1;
        }
        return 0;
      });
      if (dir == "desc") this.filteredServices.reverse();
    },
    // CREATE PRODUCT(done)
    createProduct() {
      if (!localStorage.getItem("jwt")) {
        alert("User not logged in");
        return this.$router.push({ name: "Login" });
      }
      fetch("https://laundry-villa.herokuapp.com/services", {
        method: "POST",
        body: JSON.stringify({
          product_name: this.product_name,
          product_price: this.product_price,
          product_image: this.product_image,
          product_category: this.product_category,
        }),
        headers: {
          "Content-type": "application/json; charset=UTF-8",
          Authorization: `Bearer ${localStorage.getItem("jwt")}`,
        },
      })
        .then((response) => response.json())
        .then((json) => {
          this.$router.push({ name: "Products" });
        })
        .catch((err) => {
          alert(err);
        });
    },
  },
  computed: {
    filteredServices: function () {
      return this.services.filter((service) => {
        return service.laundry_service.match(this.search);
      });
    },
  },
};
</script>

<style scoped>
a {
  text-decoration-line: none;
}
.load {
  height: 100vh;
}
.price-title {
  height: 40px;
  width: 100%;
  background-color: rgba(139, 102, 96, 0.924);
  z-index: 100;
  position: fixed;
  top: 110px;
}

.services {
  padding-top: 200px;
}
.loading {
  margin-top: 20%;
  justify-content: center;
}
.loading {
  border: 16px solid #f3f3f3;
  position: fixed;
  top: 5%;
  left: 50%;
  border-radius: 50%;
  border-top: 16px solid #000000;
  width: 120px;
  height: 120px;
  -webkit-animation: spin 2s linear infinite; /* Safari */
  animation: spin 2s linear infinite;
}
.card-img-top {
  border-radius: 10px;
  height: 200px;
  object-fit: cover;
}
.card {
  /* height: 300px; */
  width: 18rem;
}
.card-body-button {
  padding-top: 0px;
}
@-webkit-keyframes spin {
  0% {
    -webkit-transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
  }
}
@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
.button-body {
  background: rgb(255 212 0);
  border: 0;
  margin-top: 20px;
  color: rgb(0, 0, 0);
  border-radius: 10px;
  cursor: pointer;
}
button:hover {
  opacity: 0.8;
  background: #000000;
}
.sub:hover {
  color: rgb(255, 255, 255) !important;
}
.sub {
  color: rgb(0, 0, 0);
}
.sort-content {
  padding: 30px 5px 20px 5px;
  /* display: flex; 
  flex-wrap: wrap !important; */
}
.sort {
  background-color: white;
  width: 50%;
  border-radius: 15px;
  margin-bottom: 30px;
}
.products {
  padding-top: 50px;
}
.card {
  border-radius: 10px;
}
.subtitlee {
  margin-top: 70px;
}
input {
  width: 100% !important;
}
label {
  width: 100%;
}
.containe {
  justify-content: center !important;
}
@media all and (max-width: 991px) {
  .sort {
    width: 60%;
    margin-top: 70px;
  }
}
@media all and (max-width: 768px) {
  .sort {
    width: 70%;
    margin-top: 70px;
  }
  .loading {
    border: 16px solid #f3f3f3;
    position: fixed;
    top: 30%;
    left: 32%;
    border-radius: 50%;
    border-top: 16px solid #000000;
    width: 120px;
    height: 120px;
    -webkit-animation: spin 2s linear infinite; /* Safari */
    animation: spin 2s linear infinite;
  }
}
@media all and (max-width: 576px) {
  .sort {
    width: 80%;
    margin-top: 70px;
  }
  .services {
    padding-top: 130px;
  }
  .price-title {
    position: fixed;
    top: 110px;
  }
}
@media all and (max-width: 400px) {
}
</style>
