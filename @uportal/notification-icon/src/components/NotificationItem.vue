<template>
    <dropdown-item
        :target="target"
        :class="{ read: isRead, unread: !isRead }"
        :href="url"
        rel="noopener noreferrer"
        @click="onClickAction()"
        >{{ notification.title }}</dropdown-item
    >
</template>

<script>
import { DropdownItem } from 'bootstrap-vue';
import { NAVIGATION_STRATEGIES } from './NotificationIcon';

export default {
    name: 'NotificationItem',
    props: {
        notification: Object,
        strategy: {
            type: String,
            validator: (value) => NAVIGATION_STRATEGIES.indexOf(value) >= 0,
        },
        seeAllNotificationsUrl: String,
    },
    computed: {
        redirectAction() {
            return this.notification?.availableActions?.some?.(
                ({ id }) => id === 'MarkAsReadAndRedirectAction'
            );
        },
        apiUrl() {
            let redirectAction = this.notification?.availableActions?.find?.(
                ({ id }) => id === 'MarkAsReadAndRedirectAction'
            );
            return redirectAction?.apiUrl;
        },
        isRead() {
            return JSON.parse(this.notification?.attributes?.READ?.[0] || 'true');
        },
        url() {
            return this.strategy === 'list'
                ? `${this.seeAllNotificationsUrl}${
                      this.notification.id ? `?highlight=${this.notification.id}` : ''
                  }`
                : this.redirectAction
                ? 'javascript:void(0)'
                : this.notification.url;
        },
        onClickAction() {
            if (this.redirectAction) {
                return () => {
                    let form = document.createElement('form');
                    form.action = this.apiUrl;
                    form.method = 'POST';
                    form.style.display = 'none';
                    document.body.appendChild(form);
                    form.submit();
                };
            }
            return () => {};
        },
        target() {
            return this.redirectAction || this.strategy === 'list' ? '_self' : '_blank';
        },
    },
    components: {
        DropdownItem,
    },
};
</script>

<style lang="scss" scoped>
.unread {
    background-color: aliceblue;
    background-color: var(--notif-unread-bg-color, aliceblue) !important;
}

.read {
    background-color: #fff;
    background-color: var(--notif-read-bg-color, #fff) !important;
}
</style>
