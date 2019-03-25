<template>
  <div class="notification-list-container">
    <div
      class="notification-list-header notification-list-section d-flex justify-content-between"
    >
      <slot name="header"></slot>
      <button
        class="btn btn-link btn-mark-read text-muted"
        @click="markAllAsRead"
      >
        Mark all as read
      </button>
    </div>
    <div class="notification-list-content notification-list-section">
      <article
        class="media py-4 border-bottom d-flex justify-content-between"
        v-for="(item, index) in this.notifications"
        :key="index"
      >
        <img
          class="media-image mr-3 rounded"
          :src="item.image"
          :alt="item.title"
          v-if="item.image"
        />
        <div class="media-image mr-3 rounded img-placeholder" v-else />
        <div class="media-body">
          <h4 class="mt-0">
            {{ item.title }}
            <span class="text-muted" v-if="item.dueDate">
              - Due {{ item.dueDate | dueDate }}</span
            >
          </h4>
          <p>{{ item.body }}</p>
          <p class="media-link" v-if="item.url">
            <a :href="item.url" target="_blank">{{ item.url }}</a>
          </p>
        </div>
        <div
          class="media-actions"
          v-if="item.availableActions && item.availableActions.length"
        >
          <dropdown id="_uid" no-caret dropleft toggle-class="btn-icon">
            <template slot="button-content">
              <font-awesome-icon icon="ellipsis-v" size="2x" fixed-width />
            </template>
            <dropdown-item-button
              v-for="(action, num) in item.availableActions"
              :key="num"
              @click="gotoAction(action)"
            >
              {{ action.label }}
            </dropdown-item-button>
          </dropdown>
        </div>
      </article>
      <form
        class="hidden"
        method="POST"
        :action="selectedAction.apiUrl"
        target="blank"
        ref="gotoForm"
        v-if="selectedAction"
      >
        <input type="hidden" value="something" />
      </form>
    </div>
    <div class="notification-list-footer notification-list-section">
      <slot name="footer"></slot>
    </div>
  </div>
</template>
<script>
import oidc from '@uportal/open-id-connect';

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
  name: 'notification-list',
  data() {
    return {
      notifications: [],
      selectedAction: null
    };
  },
  props: {
    markAllAsReadLink: {
      type: String,
      default: '/uPortal/api/v5-1/markAllAsRead'
    },
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
    }
  },
  methods: {
    gotoAction(action) {
      if (action && action.apiUrl) {
        this.selectedAction = action;
        setTimeout(() => {
          this.$refs.gotoForm.submit();
        }, 1);
      }
    },
    markAllAsRead() {
      this.gotoAction({ apiUrl: this.markAllAsReadLink });
    },
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
        console.error(err);
      }
    }
  },
  filters: {
    dueDate: function(value) {
      if (!value) {
        return '';
      }
      let date = format(new Date(value.time), 'MM/DD/YYYY');
      return date;
    }
  },
  mounted() {
    this.fetchNotificationData();
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
.notification-list-container /deep/ {
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

  .media-image {
    object-fit: cover;
    width: 64px;
    height: 64px;

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

  .btn-icon {
    background: transparent;
    border-color: transparent;
    width: 18px;
    height: 25px;
    margin-left: 4px;
    margin-right: 4px;
    background: transparent;
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
    &:after {
      display: none;
    }
  }

  .dropdown-toggle-no-caret {
    &::before {
      display: none !important;
      content: none;
      margin: 0px;
    }
  }

  .dropdown-menu {
    max-width: 30rem;
    padding: 0px;

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
