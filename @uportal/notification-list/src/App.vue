<template>
  <main class="p-3">
    <div class="container">
      <notification-list debug notification-api-url="sample-notifications.json">
        <template v-slot:header>
          <h2>Recent Updates</h2>
        </template>
      </notification-list>
    </div>
  </main>
</template>
<script>
import NotificationList from './components/NotificationList.vue';

export default {
  name: 'app',
  components: {
    NotificationList
  }
};
</script>

<style>
html {
  font-size: 10px;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
}

body {
  font-size: 14px;
  padding: 0;
  margin: 0;
}
</style>
