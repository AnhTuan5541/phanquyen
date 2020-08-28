<template>
  <!-- Content Wrapper. Contains page content -->
  <div class="content-wrapper">
    <!-- Content Header (Page header) -->
    <section class="content-header">
      <div class="container-fluid">
        <div class="row mb-2">
          <div class="col-sm-6">
            <h1>jsGrid</h1>
          </div>
          <div class="col-sm-6">
            <ol class="breadcrumb float-sm-right">
              <li class="breadcrumb-item"><a href="#">Home</a></li>
              <li class="breadcrumb-item active">jsGrid</li>
            </ol>
          </div>
        </div>
      </div><!-- /.container-fluid -->
    </section>

    <!-- Main content -->
    <section class="content">
      <div class="card">
        <div class="card-header">
          <h3 class="card-title">jsGrid</h3>
        </div>
        <!-- /.card-header -->
        <div class="card-body">
          <div id="jsGrid1"></div>
        </div>
        <!-- /.card-body -->
      </div>
      <!-- /.card -->
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
                    name: '',
                    price: 0
                },
                selectedProduct: {},
                errors: [],
                list_products: []
            }
        },
        created() {
            this.getListProducts()
        },
        updated() {
            $("#jsGrid1").jsGrid({
                height: "100%",
                width: "100%",
        
                sorting: true,
                paging: true,
        
                data: db.clients,
        
                fields: [
                    { name: "Name", type: "text", width: 150 },
                    { name: "Age", type: "number", width: 50 },
                    { name: "Address", type: "text", width: 200 },
                    { name: "Country", type: "select", items: db.countries, valueField: "Id", textField: "Name" },
                    { name: "Married", type: "checkbox", title: "Is Married" }
                ]
            });
        },
        methods: {
            createProduct() {
                this.errors = []
                axios.post('/products', {name: this.product.name, price: this.product.price})
                .then(response => {
                    this.list_products.unshift({ ...response.data.product, isEdit: false })
                    $('#testDatatable').data.reload();
                    console.log('ok')
                })
                .catch(error => {
                    this.errors = error.response.data.errors.name
                })
            },
            getListProducts() {
                axios.get('/products')
                .then(response => {
                    this.list_products = response.data
                    this.list_products.forEach(item => {
                        Vue.set(item, 'isEdit', false)
                    })
                })
                .catch(error => {
                    this.errors = error.response.data.errors.name
                })
            },
            selecteProduct (product) {
				this.selectedProduct = { ...product }
				product.isEdit = true
			},
			updateProduct(index) {
				axios.put('/products/' + this.selectedProduct.id, {name: this.selectedProduct.name, price: this.selectedProduct.price})
				.then(response => {

					this.list_products[index].name = this.selectedProduct.name
					this.list_products[index].price = this.selectedProduct.price
					this.list_products[index].isEdit = false
				})
				.catch(error => {
					this.errors = error.response.data.errors.name
				})
			},
            deleteProduct(product, index) {
                axios.delete('/products/' + product.id)
                .then(response => {
                    console.log(response.data.result)
                    this.list_products.splice(index, 1)
                })
                .catch(error => {
                    this.errors = error.response.data.errors.name
                })
            }
        }
    }
</script>

<style lang="scss" scoped>
.error {
    span {
        color: red;
    }
}
</style>