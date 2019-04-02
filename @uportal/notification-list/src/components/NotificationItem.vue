<template>
    <article class="notification-item-container">
        <div
            class="media p-4 border-bottom d-flex justify-content-between"
            :class="{ read: isRead, unread: !isRead, highlight }"
        >
            <img
                class="media-image mr-3 rounded"
                :src="notification.image"
                :alt="notification.title"
                v-if="notification.image"
            />
            <div
                class="media-image mr-3 rounded img-placeholder"
                :style="{ 'background-color': bgColor }"
                v-else
            />
            <div class="media-body">
                <h4 class="mt-0" v-if="itemHeading">
                    {{ itemHeading }}
                </h4>
                <p>
                    <strong v-if="notification.title">{{ notification.title }}:</strong>&nbsp;{{
                        notification.body
                    }}
                </p>
                <p class="media-link" v-if="notification.url">
                    <a
                        href="javascript:void(0)"
                        target="_blank"
                        @click="$emit('performaction', linkAction)"
                    >
                        {{ notification.linkText || notification.url }}
                    </a>
                </p>
            </div>
            <div
                class="media-actions"
                v-if="notification.availableActions && notification.availableActions.length"
            >
                <dropdown id="_uid" no-caret dropleft toggle-class="btn-icon">
                    <template slot="button-content">
                        <font-awesome-icon icon="ellipsis-v" size="2x" fixed-width />
                    </template>
                    <dropdown-item-button
                        v-for="(action, num) in notification.availableActions"
                        :key="num"
                        @click="$emit('performaction', action)"
                    >
                        {{ action.label }}
                    </dropdown-item-button>
                </dropdown>
            </div>
        </div>
    </article>
</template>
<script>
import Dropdown from 'bootstrap-vue/es/components/dropdown/dropdown';
import DropdownHeader from 'bootstrap-vue/es/components/dropdown/dropdown-header';
import DropdownItemButton from 'bootstrap-vue/es/components/dropdown/dropdown-item-button';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faEllipsisV } from '@fortawesome/free-solid-svg-icons/faEllipsisV';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { format } from 'date-fns';

library.add(faEllipsisV);

/*eslint-disable */

export default {
    name: 'notification-item',
    data() {
        return {};
    },
    props: {
        notification: {
            type: Object,
            required: true
        },
        highlight: {
            type: Boolean,
            default: false
        },
        colorMap: {
            type: Object,
            default: () => ({})
        }
    },
    mounted() {},
    computed: {
        isRead() {
            return JSON.parse(this.notification?.attributes?.READ?.[0] || 'true');
        },
        linkAction() {
            return {
                apiUrl: this.notification.url
            };
        },
        itemHeading() {
            const categories = this.itemCategories;
            const dueDate = this.itemDueDate;
            const separator = categories && dueDate ? ' - ' : '';
            return `${categories || ''}${separator}${dueDate || ''}`;
        },
        itemCategories() {
            return this.notification.attributes.category
                ? this.notification.attributes.category.join(', ')
                : '';
        },
        itemDueDate() {
            let dueDate = this.notification.dueDate;
            if (!dueDate) {
                return null;
            }
            let date = format(new Date(dueDate.time), 'MM/DD/YYYY');
            return date;
        },
        bgColor() {
            return this.notification.attributes.category
                ? this.colorMap[this.notification.attributes.category[0]]
                : '';
        }
    },
    components: {
        Dropdown,
        DropdownHeader,
        DropdownItemButton,
        FontAwesomeIcon
    }
};
</script>
<style lang="scss" scoped>
.notification-item-container /deep/ {
    @import '../../node_modules/bootstrap/scss/bootstrap';

    .hidden {
        display: none;
    }

    .notification-list-header {
        padding: 1rem 0;
        border: 0;
        border-bottom: 1px solid rgba(0, 0, 0, 0.1);
    }

    .media-image {
        object-fit: cover;
        width: 48px;
        height: 48px;

        &.img-placeholder {
            background: #ccc;
            background-color: var(--notif-list-ph-bg-color, #ccc);
        }
    }

    .media-body {
        p.media-link {
            margin: 0;
        }
    }

    .unread {
        background-color: aliceblue;
        background-color: var(--notif-unread-bg-color, aliceblue) !important;
    }

    .read {
        background-color: #fff;
        background-color: var(--notif-read-bg-color, #fff) !important;
    }

    .highlight {
        background-color: honeydew;
        background-color: var(--notif-read-bg-color, honeydew) !important;
    }

    .btn-icon {
        background: transparent;
        border-color: transparent;
        width: 18px;
        height: 25px;
        margin-left: 4px;
        margin-right: 4px;
        border: 0 none;
        line-height: 0;
        padding: 0;
        position: relative;
        color: #333;

        &:hover {
            background: transparent;
            color: #333;
        }

        &:not(:disabled),
        :disabled {
            &:focus,
            &:active {
                outline: none;
                box-shadow: none;
                background-color: transparent;
                border-color: transparent;
            }
        }

        &::after {
            display: none;
        }
    }

    .dropdown-toggle-no-caret {
        &::before {
            display: none !important;
            content: none;
            margin: 0;
        }
    }

    .dropdown-menu {
        max-width: 30rem;
        padding: 0;

        .dropdown-item {
            overflow: hidden;
            text-overflow: ellipsis;
            padding: 1rem 1.5rem;
            font-size: 1.2rem;

            &.text-center {
                text-align: center;
            }
        }
    }
}
</style>
