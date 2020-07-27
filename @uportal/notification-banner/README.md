# notification-banner

[![NPM Version](https://img.shields.io/npm/v/@uportal/notification-banner.svg)](https://www.npmjs.com/package/@uportal/notification-banner)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/org.webjars.npm/uportal__notification-banner/badge.svg)](https://maven-badges.herokuapp.com/maven-central/org.webjars.npm/uportal__notification-banner)
[![Build Status](https://travis-ci.com/uPortal-contrib/notification-web-components.svg?branch=master)](https://travis-ci.com/uPortal-contrib/notification-web-components)

## Project setup

```
npm install
```

### Compiles and hot-reloads for development

```
npm run serve
```

### Compiles and minifies for production

```
npm run build
```

### Lints and fixes files

```
npm run lint
```

## Attributes

`<notification-banner>` takes the following attributes:

### `notification-api-url`

Url to connect notification-endpoint. Defaults to `/NotificationPortlet/api/v2/notifications`.

```html
<notification-banner
    notification-api-url="/NotificationPortlet/api/v2/notifications"
></notification-banner>
```

### `user-info-api-url`

Url to connect to oidc. The default for this value is `/uPortal/api/v5-1/userinfo`.

```html
<notification-banner user-info-api-url="/uPortal/api/v5-1/userinfo"></notification-icon>
```

### `notificationVariant`

Bootstrap variant of 'alert' component to style notification items. Default is 'info'. Can be set to 'custom' to add 'alert-custom' class which will allow styling according to theming section below.

```html
<notification-banner notificationVariant="info"></notification-icon>
```

### `notificationIcon`

Icon displayed next to heading. Supports "info" and "exclamation-triangle" from Font Awesome.

```html
<notification-banner notificationIcon="info"></notification-icon>
```

### `notificationIconSize`

Icon size for Font Awesome icon displayed next to heading.

```html
<notification-banner notificationIconSize="2x"></notification-icon>
```

### `filter`

Query string parameter to append to api call to retrieve notifications.

```html
<notification-banner notificationIconSize="2x"></notification-icon>
```

### `debug`

Skips oidc call for api token.

```html
<notification-banner debug></notification-list
```

### Theming

Currently this component supports [CSS Variables](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables) for overriding button colors. Defining the following variables will change the colors for the component accordingly. They will fall back to the colors described below.

You should define this in your custom stylesheet.

fg = foreground (text)
bg = background

```css
:root {
    --notif-banner-heading-fg-color: white; /* text color of notification item heading */
    --notif-banner-body-fg-color: white; /* text color of notification item body  */
    --notif-banner-bg-color: darkred; /* background color of notification items */
    --notif-banner-border-color: darkred; /* border color of notification items */
    --notif-banner-border-radius: 0; /* border radius of notification items */
    --notif-banner-item-margin: 0 0 1px 0; /* margin between notification items */
    --notif-banner-container-margin: 0; /* margin around notification item list */
}
```
