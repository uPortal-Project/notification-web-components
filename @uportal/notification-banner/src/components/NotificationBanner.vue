<template>
    <div class="notification-banner">
        <b-alert v-for="(notification, index) in notifications" :key="index" :varient="notificationVariant" :class="{'alert-custom': customVariant}" show>
            <h4>
                <font-awesome-icon :icon="notificationIcon" :size="notificationIconSize" />
                {{ notification.title }}
            </h4>
            <span v-html="notification.body" />
        </b-alert>
    </div>
</template>

<script>
import { library } from '@fortawesome/fontawesome-svg-core';
import { faInfo, faExclamationTriangle } from '@fortawesome/free-solid-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import bAlert from 'bootstrap-vue/es/components/alert/alert';
import oidc from '@uportal/open-id-connect';
import { get } from 'axios';

library.add(faInfo, faExclamationTriangle);

export default {
    name: 'NotificationBanner',

    props: {
        debug: {
            type: String,
            default: 'false'
        },
        userInfoApiUrl: {
            type: String,
            default: '/uPortal/api/v5-1/userinfo'
        },
        notificationApiUrl: {
            type: String,
            default: '/NotificationPortlet/api/v2/notifications'
        },
        notificationVariant: {
            type: String,
            default: 'info'
        },
        notificationIcon: {
            type: String,
            default: 'info'
        },
        notificationIconSize: {
            type: String,
            default: '1x'
        },
        filter: {
            type: String,
            default: ''
        },
        refresh: {
            type: String,
            default: '0'
        }
    },

    components: {
        'b-alert': bAlert,
        'font-awesome-icon': FontAwesomeIcon
    },

    data() {
        return {
            // list of notifications to display
            notifications: []
        };
    },

    computed: {
        isDebug: function () {
            return this.debug === 'true' ? true : false;
        },
        customVariant: function () {
            const { notificationVariant } = this;
            return ['info', 'primary', 'success', 'danger', 'warning', 'secondary', 'light', 'dark'].indexOf(notificationVariant) === -1;
        }
    },

    methods: {
        async fetchNotifications() {
            // read props
            const { notificationApiUrl, filter, userInfoApiUrl } = this;

            try {
                // Obtain an OIDC Id Token, except in debug mode
                const { encoded: token } = this.isDebug
                    ? { encoded: null }
                    : await oidc({ userInfoApiUrl });

                // gather notifications
                const querystring = filter ? '?' + filter : '';
                const { data: notifications } = await get(notificationApiUrl + querystring, {
                    withCredentials: true,
                    headers: {
                        Authorization: `Bearer ${token}`,
                        'content-type': 'application/jwt'
                    }
                });

                // store notifications to state
                this.notifications = notifications;
            } catch (err) {
                // eslint-disable-next-line no-console
                console.error(err);
            }

            if (this.refresh !== '0') {
                const { refresh } = this;
                const time = parseInt(refresh);

                setTimeout(() => this.fetchNotifications(), time);
            }

        }
    },

    // entrypoint
    created() {
        return this.fetchNotifications();
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.notification-banner {
    // core bootstrap framework
    @import '../../node_modules/bootstrap/scss/functions.scss';
    @import '../../node_modules/bootstrap/scss/variables.scss';
    @import '../../node_modules/bootstrap/scss/mixins.scss';
    // bootstrap styles needed by page
    @import '../../node_modules/bootstrap/scss/alert.scss';

    // custom styles
    margin: 1rem;
    margin: var(--notif-banner-container-margin, 1rem);
    text-align: left;

    .alert.alert-custom {
        background: var(--notif-banner-bg-color, grey);
        color: var(--notif-banner-body-fg-color, white);
        border-color: var(--notif-banner-border-color, var(--notif-banner-bg-color, grey));
        border-radius: var(--notif-banner-border-radius, 0.25rem);
        margin: var(--notif-banner-item-margin, 1rem);

        > h4 {
            color: var(--notif-banner-heading-fg-color, white);
            margin-top: 0;
        }
    }
}

.notification-banner svg {
    height: 2rem;
    position: relative;
    top: 0.1rem;
}
</style>
