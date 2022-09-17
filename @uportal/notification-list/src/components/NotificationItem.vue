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
                v-if="notification.image && !imageError"
                @error="imageError = true"
            />
            <div class="media-image mr-3 img-placeholder" :style="{ color: bgColor }" v-else>
                <font-awesome-icon icon="comment-alt" size="2x" fixed-width />
            </div>
            <div class="media-body">
                <h4>
                    <strong v-if="notification.title">{{ notification.title }}</strong>
                </h4>
                <p class="text-dark" v-if="notification.body" v-html="notification.body"></p>
                <p class="mt-0" v-if="itemHeading">
                    <span v-if="itemCategories">{{ itemCategories }}</span>
                    <span v-if="itemCategories && itemDueDate">&nbsp;-&nbsp;</span>
                    <span v-if="itemDueDate" :class="{ 'text-danger': isPastDue }">{{
                        itemDueDate
                    }}</span>
                </p>
                <p class="blockquote-footer">
                    Source: <cite :title="notification.source">{{ notification.source }}</cite>
                </p>
                <p class="media-link" v-if="notification.url">
                    <a
                        href=""
                        target="_blank"
                        @click="openAction($event, linkAction)"
                        rel="noopener noreferrer"
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
                        @click="openAction($event, action)"
                    >
                        {{ action.label }}
                    </dropdown-item-button>
                </dropdown>
            </div>
        </div>
    </article>
</template>
<script>
import { BDropdown, BDropdownItemButton } from 'bootstrap-vue';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faEllipsisV } from '@fortawesome/free-solid-svg-icons/faEllipsisV';
import { faCommentAlt } from '@fortawesome/free-solid-svg-icons/faCommentAlt';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { format, isPast, distanceInWordsToNow } from 'date-fns';

library.add(faEllipsisV);
library.add(faCommentAlt);

export default {
    name: 'notification-item',
    data() {
        return {
            imageError: false,
        };
    },
    props: {
        notification: {
            type: Object,
            required: true,
        },
        highlight: {
            type: Boolean,
            default: false,
        },
        colorMap: {
            type: Object,
            default: () => ({}),
        },
        dateFormat: {
            type: String,
            default: 'MM/DD/YYYY',
        },
    },
    methods: {
        openAction($event, action) {
            $event.preventDefault();
            this.$emit('performaction', action);
        },
    },
    computed: {
        isRead() {
            return JSON.parse(this.notification?.attributes?.READ?.[0] || 'true');
        },
        linkAction() {
            return {
                apiUrl: this.notification.url,
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
            if (!this.notification.dueDate) {
                return null;
            }
            const dueDate = this.notification.dueDate;
            const notificationDate = new Date(dueDate.time);
            let ago = '';
            if (this.isPastDue) {
                ago = ` ${distanceInWordsToNow(notificationDate, { addSuffix: true })}`;
            }
            return `Due${ago} on ${format(notificationDate, this.dateFormat)}`;
        },
        isPastDue() {
            if (!this.notification.dueDate) {
                return false;
            }
            return isPast(new Date(this.notification.dueDate.time));
        },
        bgColor() {
            return this.notification.attributes.category
                ? this.colorMap[this.notification.attributes.category[0]]
                : '';
        },
    },
    components: {
        Dropdown: BDropdown,
        DropdownItemButton: BDropdownItemButton,
        FontAwesomeIcon,
    },
};
</script>
<style lang="scss" scoped>
:deep(.notification-item-container) {
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
        width: 40px;
        height: 40px;

        &.img-placeholder {
            text-align: right;
            color: #999;
            color: var(--notif-list-icon-ph-color, #999);
        }
    }

    .media-body {
        p.media-link {
            margin: 0;
        }
    }

    .unread {
        background-color: aliceblue;
        background-color: var(--notif-unread-bg-color, aliceblue);
    }

    .read {
        background-color: #fff;
        background-color: var(--notif-read-bg-color, #fff);
    }

    .highlight {
        background-color: honeydew;
        background-color: var(--notif-highlight-bg-color, honeydew);
    }

    .blockquote-footer::before {
        content: none;
    }

    .btn-icon {
        background: transparent;
        border-color: transparent;
        width: 12px;
        width: var(--notif-list-item-dd-width, 12px);
        height: 28px;
        height: var(--notif-list-item-dd-height, 28px);
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
