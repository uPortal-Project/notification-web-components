<template>
    <main>
        <notification-list oidc-url="/proxy/uPortal/api/v5-1/userinfo"></notification-list>
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
