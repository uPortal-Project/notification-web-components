<template>
    <div class="notification-list-container">
        <div
            class="notification-list-header notification-list-section d-flex justify-content-between"
        >
            <slot name="header"></slot>
        </div>
        <div class="notification-list-content notification-list-section">
            <notification-item
                v-for="(item, index) in this.notifications"
                :id="item.id ? item.id : ''"
                :key="index"
                :notification="item"
                @performaction="gotoAction($event)"
                :highlight="highlight && item.id === highlight"
                :color-map="colors"
            >
            </notification-item>
            <span v-if="loaded && !notifications.length">No results found.</span>
        </div>
        <div class="notification-list-footer notification-list-section">
            <slot name="footer"></slot>
        </div>
    </div>
</template>
<script>
import oidc from '@uportal/open-id-connect';
import NotificationItem from './NotificationItem';
import queryString from 'query-string';

const DEFAULT_COLOR_MAP = {
    Announcement: '#6649bb',
    Holds: '#487df9',
    'To-Do': '#c85a89'
};

export default {
    name: 'notification-list',
    data() {
        return {
            notifications: [],
            selectedAction: null,
            loaded: false
        };
    },
    props: {
        oidcUrl: {
            type: String,
            default: '/uPortal/api/v5-1/userinfo'
        },
        notificationApiUrl: {
            type: String,
            default: '/NotificationPortlet/api/v2/notifications'
        },
        debug: {
            type: Boolean,
            default: false
        },
        colorMap: {
            type: String,
            default: () => '{}'
        }
    },
    computed: {
        colors() {
            return {
                ...DEFAULT_COLOR_MAP,
                ...JSON.parse(this.colorMap)
            };
        }
    },
    methods: {
        async gotoAction(action) {
            if (action.redirect) {
                const form = document.createElement('form');
                form.action = action.apiUrl;
                form.method = 'POST';
                form.target = '_blank';
                form.style.display = 'none';
                document.body.appendChild(form);
                form.submit();
            }
            else {
                const response = await fetch(action.apiUrl, {
                    credentials: 'same-origin',
                    method: 'POST',
                });

                if (!response.ok || response.status !== 200) {
                    throw new Error(response.statusText);
                }
            }
        },
        markAllAsRead() {
            this.gotoAction(this.markAllAsReadLink);
        },
        handleOidcError() {
            this.hasError = true;
            this.errorMessage = 'There was a problem authorizing this request.';
        },
        handleWflError() {
            this.hasError = true;
            this.errorMessage = 'There was a problem retrieving notifications.';
        },
        async getToken() {
            try {
                return await oidc({ userInfoApiUrl: this.oidcUrl });
            } catch (err) {
                this.handleOidcError(err);
            }
        },
        async fetchNotificationData() {
            const token = this.debug ? null : (await this.getToken()).encoded;

            try {
                const response = await fetch(this.notificationApiUrl, {
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

                this.notifications = payload;
            } catch (err) {
                this.err = err;
            } finally {
                this.loaded = true;
            }
        }
    },
    mounted() {
        this.fetchNotificationData();

        const queryParams = queryString.parse(location.search);
        this.highlight = queryParams.highlight || queryParams.pP_highlight;
    },
    components: {
        NotificationItem
    }
};
</script>
<style lang="scss" scoped>
.notification-list-container {
    @import '../../node_modules/bootstrap/scss/bootstrap';

    .hidden {
        display: none;
    }

    .btn-mark-read {
        font-size: 1.2em;
        padding: 0;
        background: none;
        border: none;
    }

    .notification-list-section:empty {
        display: block;
    }

    .notification-list-header {
        padding: 1rem 0;
        border: 0;
        border-bottom: 1px solid rgba(0, 0, 0, 0.1);
    }
}
</style>
