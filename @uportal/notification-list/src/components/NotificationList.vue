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
  computed: {},
  props: {
    oidcUrl: {
      type: String,
      default: '/uPortal/api/v5-1/userinfo'
    },
    dataUrl: {
      type: String,
      required: true
    }
  },
  methods: {
    handleOidcError(err) {
      this.hasError = true;
      this.errorMessage = 'There was a problem authorizing this request.';
    },
    handleWflError(err) {
      this.hasError = true;
      this.errorMessage = 'There was a problem retrieving notifications.';
    },
    async getToken() {
      try {
        return await oidc({ userInfoApiUrl: this.oidcUrl });
      } catch (err) {
        console.error(err);
        this.handleOidcError(err);
      }
    },
    async fetchNotificationData() {
      const token = this.debug ? null : (await this.getToken()).encoded;

      try {
        const response = await fetch(this.url, {
          credentials: 'same-origin',
          headers: {
            Authorization: 'Bearer ' + token,
            'content-type': 'application/jwt'
          }
        });

        if (!response.ok || response.status !== 200) {
          throw new Error(response.statusText);
        }

        const payload = await response.json();

        console.log(payload);
      } catch (err) {
        console.error(err);
      }
    }
  },
  mounted() {
    this.fetchNotificationData();
  }
};
</script>
<style lang="scss" scoped>
.notification-list-container /deep/ {
  @import '../../node_modules/bootstrap/scss/bootstrap';
}
</style>
