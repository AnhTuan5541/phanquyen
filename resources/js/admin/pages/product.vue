<template>
  <!-- Content Wrapper. Contains page content -->
  <div class="content-wrapper">
    <!-- Content Header (Page header) -->
    <section class="content-header">
      <div class="container-fluid">
        <div class="row mb-2">
          <div class="col-sm-6">
            <h1>DataTables</h1>
          </div>
          <div class="col-sm-6">
            <ol class="breadcrumb float-sm-right">
              <li class="breadcrumb-item">
                <a href="#">Home</a>
              </li>
              <li class="breadcrumb-item active">DataTables</li>
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
            <div class="card card-primary">
              <div class="card-header">
                <h3 class="card-title">General</h3>

                <div class="card-tools">
                  <button
                    type="button"
                    class="btn btn-tool"
                    data-card-widget="collapse"
                    data-toggle="tooltip"
                    title="Collapse"
                  >
                    <i class="fas fa-minus"></i>
                  </button>
                </div>
              </div>
              <div class="card-body">
                <div class="form-group">
                  <label for="inputAddName">Name</label>
                  <input type="text" id="inputAddName" class="form-control" v-model="addname"/>
                </div>
                <div class="form-group">
                  <label for="inputAddPrice">Price</label>
                  <input type="text" id="inputAddPrice" class="form-control" v-model="addprice"/>
                </div>
                <button class="btn btn-success float-right" @click="createProduct()">Create</button>
                <button class="btn btn-secondary float-right" @click="cancel()">Cancel</button>
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
                <h3 class="card-title">DataTable with default features</h3>
              </div>
              <!-- /.card-header -->
              <div class="card-body">
                <table id="testDatatable" class="table table-bordered table-striped">
                  <thead>
                    <tr>
                      <th>ID</th>
                      <th>Name</th>
                      <th>Price</th>
                      <th>Created At</th>
                      <th>Action</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(prod, index) in list_products" :key="prod.id">
                      <td>{{ prod.id }}</td>
                      <td>{{ prod.name }}</td>
                      <td>{{ prod.price }}</td>
                      <td>{{ prod.created_at }}</td>
                      <td>
                        <button class="btn btn-success" @click="selecteProduct(prod)">Edit</button>
                        <button class="btn btn-danger" @click="deleteProduct(prod, index)">Delete</button>
                      </td>
                    </tr>
                  </tbody>
                  <tfoot>
                    <tr>
                      <th>ID</th>
                      <th>Name</th>
                      <th>Price</th>
                      <th>Created At</th>
                      <th>Action</th>
                    </tr>
                  </tfoot>
                </table>
              </div>
              <!-- /.card-body -->
            </div>
            <!-- /.card -->
          </div>
          <!-- /.col -->
        </div>
        <!-- /.row -->
      </div>
      <!-- /.container-fluid -->
    </section>
    <!-- /.content -->
  </div>
  <!-- /.content-wrapper -->
</template>

<script>
export default {
    data() {
        return {
        product: {
            name: "",
            price: 0,
        },
        addname: '',
        addprice: 0,
        isShowAddProduct: false,
        selectedProduct: {},
        errors: [],
        list_products: [],
        prevSelectedProduct: null,
        };
    },
    created() {
        this.getListProducts();
    },
    updated() {
      // $('#testDatatable').DataTable( {
      //   destroy: true,
      //   responsive: true,
      //   autoWidth: false,
      // });
      // console.log(1)
    },
    methods: {
        getListProducts() {
        axios
            .get("/products")
            .then((response) => {
            this.list_products = response.data;
            
            })
            .catch((error) => {
            this.errors = error.response.data.errors.name;
            });
        },
        createProduct() {
          console.log(2)
          // if ( $.fn.dataTable.isDataTable( '#testDatatable' ) ) {
          //     $('#testDatatable').DataTable({
          //       destroy: true,
          //     });
          // }
          axios
          .post('/products', {
              name: this.addname,
              price: this.addprice,
          })
          .then((response) => {
            console.log(3)
            this.list_products.push(...response.data.product)
            // this.getListProducts();
            // this.$nextTick(function() {
            // $('#testDatatable').DataTable({
            //     'destroy'     : true,
            //     'responsive'      : true,
                
            //     'autoWidth'   : false,
            //     'dom'         : 'Blfrtip',
                
            //   });
            // });
            // if ( $.fn.dataTable.isDataTable( '#testDatatable' ) ) {
            //     table = $('#testDatatable').DataTable({

            //     });
            // }
            // else {
            //     table = $('#testDatatable').DataTable( {
            //         responsive: true,
            //         autoWidth: false,
            //     });
            // }

          })
          .catch((error) => {
            console.log(error.response.data.errors.name);
          });
        },

    },
    
};
</script>

<style lang="scss" scoped>
</style>