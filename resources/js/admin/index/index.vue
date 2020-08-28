<template>
  <div class="wrapper">
    <navbar></navbar>
    <sidebar></sidebar>
    <appcontent></appcontent>
    <appfooter></appfooter>

    <!-- Control Sidebar -->
    <aside class="control-sidebar control-sidebar-dark">
      <!-- Control sidebar content goes here -->
    </aside>
    <!-- /.control-sidebar -->
  </div>
</template>

<script>
    import navbar from './navbar.vue';
    import sidebar from './sidebar.vue';
    import appcontent from './content.vue';
    import appfooter from './footer.vue';

    export default {
        components: {
            navbar : navbar,
            sidebar : sidebar,
            appcontent : appcontent,
            appfooter : appfooter,
        }
    };

</script>
<style lang="scss" scoped>

</style>