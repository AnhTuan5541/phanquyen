<template>
    <div class="card-footer clearfix" >
        <ul class="pagination pagination-md m-0 float-right" v-if="data.length > 10 || currentPage > 1">
            <li class="page-item" v-if="currentPage === 1" style=" display: none "><a class="page-link">Previous</a></li>
            <li class="page-item" v-else style=" display: block " @click="onClickPreviousPage" ><a class="page-link">Previous</a></li>
            <li class="page-item" v-for="(page, index) in pages" :key="index" @click="onClickPage(page.number)" :class= "{ active: isPageActive(page.number) }"><a class="page-link">{{ page.number }}</a></li>
            <li class="page-item" v-if="currentPage === totalPages" style=" display: none "><a class="page-link">Next</a></li>
            <li class="page-item" v-else style=" display: block " @click="onClickNextPage" ><a class="page-link">Next</a></li>
        </ul>
    </div>
</template>

<script>
    export default {
        props: {
            data: {
                type: Array,
                required: true
            },
            maxVisibleButtons: {
                type: Number,
                required: false,
                default: 10
            },
            totalPages: {
                type: Number,
                required: true
            },
            perPage: {
                type: Number,
                required: true
            },
            currentPage: {
                type: Number,
                required: false
            }
        },
        computed: {
            paginatedData() {
                let start = (this.currentPage - 1) * this.perPage, end = start + this.perPage
                return this.data.slice(start, end)
            },
            // startPage() {
            //     if (this.currentPage === 1) return 1
            //     if (this.currentPage === this.totalPages) return this.totalPages -  this.maxVisibleButtons + (this.maxVisibleButtons - 1)
            //     return this.currentPage - 1
            // },
            // endPage() { return Math.min(this.startPage + this.maxVisibleButtons - 1, this. totalPages)},
            pages() {
                let range = []
                for (let i = 1; i <= this.totalPages; i ++) {
                    range.push({
                        number: i,
                        isDisable: i === this.currentPage
                    });
                }
                return range
            },
        },
        methods: {
            onClickPreviousPage() {
                this.$emit('pagechanged', this.currentPage - 1)
            },
            onClickPage(page) {
                this.$emit('pagechanged', page)
            },
            onClickNextPage() {
                this.$emit('pagechanged', this.currentPage + 1)
            },
            isPageActive(page) {
                return this.currentPage === page
            },
            // onPageChange(page) {
            //     this.currentPage = page
            // }
        }
    }
</script>

<style lang="scss" scoped>
</style>