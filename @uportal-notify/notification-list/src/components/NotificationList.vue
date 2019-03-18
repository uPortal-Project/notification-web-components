<template>
  <span>Hello from the Notification List</span>
</template>
<script>
import oidc from '@uportal/open-id-connect';

export default {
  name: 'notification-list',
  data() {
    return {
    };
  },
  computed: { },
  props: {
    oidcUrl: {
      type: String,
      default: '/uPortal/api/v5-1/userinfo'
    }
  },
  methods: { },
  mounted() { }
};
</script>
<style lang="scss" scoped>
.notification-list-container /deep/ {
  @import '../../node_modules/bootstrap/scss/bootstrap';
}
</style>
