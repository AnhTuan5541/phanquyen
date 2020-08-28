<template>
<div>
  <div v-if="loading">
    <!-- <Spinner :animation-duration="2000" :size="65" color="#ff1d5e" /> -->
    <!-- <img src="/dist/img/photo1.png" alt="Photo"> -->
    <img :src="'https://flevix.com/wp-content/uploads/2019/07/Curve-Loading.gif'" alt="Photo">
   </div>
  <div v-else>
<!-- Content Wrapper. Contains page content -->
  <div class="content-wrapper">
    <!-- Content Header (Page header) -->
    <section class="content-header">
      <div class="container-fluid">
        <div class="row mb-2">
          <div class="col-sm-6">
            <h1>Simple Tables</h1>
          </div>
          <div class="col-sm-6">
            <ol class="breadcrumb float-sm-right">
              <li class="breadcrumb-item">
                <a href="#">Home</a>
              </li>
              <li class="breadcrumb-item active">Simple Tables</li>
            </ol>
          </div>
        </div>
      </div>
      <!-- /.container-fluid -->
    </section>

    <!-- Main content -->
    <section class="content">
      <div class="container-fluid">
        <div class="row">
          <div class="col-md-12">
            <div class="card card-primary collapsed-card">
              <div class="card-header">
                <h3 class="card-title">Expandable</h3>
                <div class="card-tools">
                  <button type="button" class="btn btn-tool" data-card-widget="collapse"><i class="fas fa-plus"></i>
                  </button>
                </div>
                <!-- /.card-tools -->
              </div>
              <!-- /.card-header -->
              <div class="card-body" id="addProduct">
                <div class="form-group">
                  <label for="inputName">Product Name</label>
                  <input type="text" id="inputName" class="form-control" v-model="product.name"/>
                </div>
                
                <div class="form-group">
                  <label for="inputPrice">Price</label>
                  <input type="number" min="0" id="inputPrice" class="form-control" v-model.number="product.price" />
                </div>
                
                <button class="btn btn-secondary float-right">Cancel</button>
                <button class="btn btn-success float-right" @click="createProduct()">Create</button>
              </div>
              <!-- /.card-body -->
            </div>
            <!-- /.card -->
          </div>

        </div>
      </div>
    </section>
    <!-- /.content -->

    <!-- Main content -->
    <section class="content">
      <div class="container-fluid">
        <div class="row">
          <div class="col-12">
            <div class="card">
              <div class="card-header">
                <h3 class="card-title">Responsive Hover Table</h3>

                <div class="card-tools">
                  <div class="input-group input-group-sm" style="width: 150px;">
                    <input
                      type="text"
                      name="table_search"
                      class="form-control float-right"
                      placeholder="Search"
                    />

                    <!-- <div class="input-group-append">
                      <button type="submit" class="btn btn-default">
                        <i class="fas fa-search"></i>
                      </button>
                    </div> -->
                  </div>
                </div>
              </div>
              <!-- /.card-header -->
              <div class="card-body table-responsive p-0">
                <table class="table table-hover text-nowrap">
                  <thead>
                    <tr>
                      <th>ID</th>
                      <th>Name</th>
                      <th>Price</th>
                      <th>Date created</th>
                      <th>Action</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(prod, index) in list_products" :key="prod.id">
                      <td>{{ prod.id }}</td>
                      <td v-if="!prod.isEdit">{{ prod.name }}</td>
                      <td v-else>
                        <input type="text" class="form-control" v-model="selectedProduct.name" />
                      </td>
                      <td v-if="!prod.isEdit">{{ prod.price }}</td>
                      <td v-else>
                        <input
                          type="text"
                          class="form-control"
                          v-model.number="selectedProduct.price"
                        />
                      </td>
                      <td>{{ prod.created_at }}</td>
                      <td v-if="!prod.isEdit">
                        <button class="btn btn-success" @click="selecteProduct(prod)">Edit</button>
                        <button class="btn btn-danger" @click="deleteProduct(prod, index)">Delete</button>
                      </td>
                      <td v-else>
                        <button class="btn btn-primary" @click="updateProduct(index)">Save</button>
                        <button class="btn btn-danger" @click="prod.isEdit = false">Cancel</button>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <!-- /.card-body -->
              <Pagination 
                :data="list_products"
                :total-pages="Math.ceil(list_products.length / 10)"
                :total = "list_products.length"
                :per-page="10"
                :current-page="currentPage"
                @pagechanged="onPageChange"
              />
              
            </div>
            <!-- /.card -->
          </div>
        </div>
      </div>
      <!-- /.container-fluid -->
    </section>
    <!-- /.content -->
  </div>
  <!-- /.content-wrapper -->
  </div>
</div>
</template>

<script>
  import Pagination from './pagination.vue';
  export default {
    components: { Pagination },

    data() {
      return {
        loading: false,
        product: {
          name: "",
          price: 0,
        },
        selectedProduct: {},
        errors: [],
        list_products: [],
        prevSelectedProduct: null,
        currentPage: 1,
      };
    },
    mounted() {
      this.getListProducts();
    },
    updated() {

    },
    methods: {
      createProduct() {
        this.errors = [];
        axios
          .post("/products", {
            name: this.product.name,
            price: this.product.price,
          })
          .then((response) => {
            this.list_products.unshift({ ...response.data.product, isEdit: false });
          })
          .catch((error) => {
            this.errors = error.response.data.errors.name;
          });
      },
      getListProducts() {
        this.loading = true;//the loading begin
        axios
          .get("/products")
          .then((response) => {
            this.list_products = response.data;
            this.list_products.forEach((item) => {
              Vue.set(item, "isEdit", false);
            });
          })
          .catch((error) => {
            this.errors = error.response.data.errors.name;
          })
          .finally(() => (this.loading = false));// set loading to false when request finish
      },
      selecteProduct(product) {
        if (this.prevSelectedProduct) {
          // nếu trước đó có chọn 1 sản phẩm để edit
          this.prevSelectedProduct.isEdit = false; // đóng form edit
        }

        this.selectedProduct = { ...product };
        product.isEdit = true;

        this.prevSelectedProduct = product; // thiết lập giá trị mới
      },
      updateProduct(index) {
        axios
          .put("/products/" + this.selectedProduct.id, {
            name: this.selectedProduct.name,
            price: this.selectedProduct.price,
          })
          .then((response) => {
            this.list_products[index].name = this.selectedProduct.name;
            this.list_products[index].price = this.selectedProduct.price;
            this.list_products[index].isEdit = false;
          })
          .catch((error) => {
            this.errors = error.response.data.errors.name;
          });
      },
      deleteProduct(product, index) {
        axios
          .delete("/products/" + product.id)
          .then((response) => {
            console.log(response.data.result);
            this.list_products.splice(index, 1);
          })
          .catch((error) => {
            this.errors = error.response.data.errors.name;
          });
      },
      onPageChange(page) {
        this.currentPage = page
      }
    },
  };
</script>



<style type="text/css" scoped>
.breeding-rhombus-spinner {
      height: 65px;
      width: 65px;
      position: relative;
      transform: rotate(45deg);
    }

    .breeding-rhombus-spinner, .breeding-rhombus-spinner * {
      box-sizing: border-box;
    }

    .breeding-rhombus-spinner .rhombus {
      height: calc(65px / 7.5);
      width: calc(65px / 7.5);
      animation-duration: 2000ms;
      top: calc(65px / 2.3077);
      left: calc(65px / 2.3077);
      background-color: #ff1d5e;
      position: absolute;
      animation-iteration-count: infinite;
    }

    .breeding-rhombus-spinner .rhombus:nth-child(2n+0) {
       margin-right: 0;
     }

    .breeding-rhombus-spinner .rhombus.child-1 {
      animation-name: breeding-rhombus-spinner-animation-child-1;
      animation-delay: calc(100ms * 1);
    }

    .breeding-rhombus-spinner .rhombus.child-2 {
      animation-name: breeding-rhombus-spinner-animation-child-2;
      animation-delay: calc(100ms * 2);
    }

    .breeding-rhombus-spinner .rhombus.child-3 {
      animation-name: breeding-rhombus-spinner-animation-child-3;
      animation-delay: calc(100ms * 3);
    }

    .breeding-rhombus-spinner .rhombus.child-4 {
      animation-name: breeding-rhombus-spinner-animation-child-4;
      animation-delay: calc(100ms * 4);
    }

    .breeding-rhombus-spinner .rhombus.child-5 {
      animation-name: breeding-rhombus-spinner-animation-child-5;
      animation-delay: calc(100ms * 5);
    }

    .breeding-rhombus-spinner .rhombus.child-6 {
      animation-name: breeding-rhombus-spinner-animation-child-6;
      animation-delay: calc(100ms * 6);
    }

    .breeding-rhombus-spinner .rhombus.child-7 {
      animation-name: breeding-rhombus-spinner-animation-child-7;
      animation-delay: calc(100ms * 7);
    }

    .breeding-rhombus-spinner .rhombus.child-8 {
      animation-name: breeding-rhombus-spinner-animation-child-8;
      animation-delay: calc(100ms * 8);
    }

    .breeding-rhombus-spinner .rhombus.big {
      height: calc(65px / 3);
      width: calc(65px / 3);
      animation-duration: 2000ms;
      top: calc(65px / 3);
      left: calc(65px / 3);
      background-color: #ff1d5e;
      animation: breeding-rhombus-spinner-animation-child-big 2s infinite;
      animation-delay: 0.5s;
    }


    @keyframes breeding-rhombus-spinner-animation-child-1 {
      50% {
        transform: translate(-325%, -325%);
      }
    }

    @keyframes breeding-rhombus-spinner-animation-child-2 {
      50% {
        transform: translate(0, -325%);
      }
    }

    @keyframes breeding-rhombus-spinner-animation-child-3 {
      50% {
        transform: translate(325%, -325%);
      }
    }

    @keyframes breeding-rhombus-spinner-animation-child-4 {
      50% {
        transform: translate(325%, 0);
      }
    }

    @keyframes breeding-rhombus-spinner-animation-child-5 {
      50% {
        transform: translate(325%, 325%);
      }
    }

    @keyframes breeding-rhombus-spinner-animation-child-6 {
      50% {
        transform: translate(0, 325%);
      }
    }

    @keyframes breeding-rhombus-spinner-animation-child-7 {
      50% {
        transform: translate(-325%, 325%);
      }
    }

    @keyframes breeding-rhombus-spinner-animation-child-8 {
      50% {
        transform: translate(-325%, 0);
      }
    }

    @keyframes breeding-rhombus-spinner-animation-child-big {
      50% {
        transform: scale(0.5);
      }
    }

</style>