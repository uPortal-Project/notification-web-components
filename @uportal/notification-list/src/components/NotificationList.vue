<template>
    <div class="notification-list-container">
        Hello from the Notification List
    </div>
</template>
<script>
// import oidc from '@uportal/open-id-connect';

export default {
    name: 'notification-list',
    data() {
        return {};
    },
    computed: {}
};
</script>
<style lang="scss" scoped>
.notification-list-container /deep/ {
    @import '../../node_modules/bootstrap/scss/bootstrap';
}
</style>
