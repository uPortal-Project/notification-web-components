# Notification Icon

[![NPM Version](https://img.shields.io/npm/v/@uportal/notification-icon.svg)](https://www.npmjs.com/package/@uportal/notification-icon)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/org.webjars.npm/uportal__notification-icon/badge.svg)](https://maven-badges.herokuapp.com/maven-central/org.webjars.npm/uportal__notification-icon)
[![Build Status](https://travis-ci.org/uPortal-contrib/notification-web-components.svg?branch=master)](https://travis-ci.org/uPortal-contrib/notification-web-components)

## Development

```bash
# clone the repository if you haven't already
git clone https://github.com/uPortal-contrib/notification-web-components

# navigate to the notification-icon folder
cd uPortal-web-components/@uportal/notification-icon

# install the goods
npm install

# start the app
npm start
```

## Build

Pretty simple:

`npm run build`

This will compile your JavaScript in to `build/static/js/main.{buildnumbers}.js`

## Usage

After building, grab the compiled javascript files and place it on your page.
You can now place `<notification-icon>` anywhere on your page.

Here's the guts of that html file:

```html
<notification-icon> </notification-icon>
```

-   Note: The component includes bootstrap, so that you can use bootstrap's styles in your content slots. However, font-awesome has issues crossing the shadow-dom boundry, so that if you include an icon in the content slot, you also need to include a `<link>` to the font-awesome stylesheet from uPortal (or elsewhere if you wish).

##### Notice

We have a `/proxy/` leading the `oidc-url` attribute. This is a developer convenience to be able to query against your local running instance of uPortal. The proxy is configured in `package.json`:

```json
  "proxy": {
    "/proxy": {
      "target": "http://localhost:8080",
      "changeOrigin": true,
      "pathRewrite": {
        "^/proxy": "/"
      }
    }
  },
```

## Attributes

`<notification-icon>` takes the following attributes:

### `notification-api-url`

Url to connect notification-endpoint. Defaults to `/NotificationPortlet/api/v2/notifications`.

```html
<notification-icon
    notification-api-url="/NotificationPortlet/api/v2/notifications"
></notification-icon>
```

### `user-info-api-url`

Url to connect to oidc. The default for this value is `/uPortal/api/v5-1/userinfo`.

```html
<notification-icon user-info-api-url="/uPortal/api/v5-1/userinfo"></notification-icon>
```

### `see-all-notifications-url`

Path to a page in uportal to navigate to for "see all notifications" link.

```html
<notification-icon see-all-notifications-url="/uPortal/p/notification"></notification-icon>
```

### `count-all-notifications`

Flag to tell component to count all notifications vs unread notifications in alert icon.

```html
<notification-icon count-all-notifications="true"></notification-icon>
```

### `navigation-strategy`

A flag to tell the component to navigate to an external link from data or a provided uPortal page (likely the notification-list).

**Options:** list, link

```html
<notification-icon navigation-strategy="list"></notification-icon>
```

### `debug`

Skips oidc call for api token.

```html
<notification-icon debug></notification-icon>
```

## Project setup

```
npm install
```

### Compiles and hot-reloads for development

```
npm run start
```

### Compiles and minifies for production

```
npm run build
```

### Run your tests

```
npm run test
```

### Lints and fixes files

```
npm run lint
```

### Options

-   `count-all-notifications` (optional, boolean): by default the notifications icon will display a count of unread items, `true` will display a count of all (read and unread) items for the user.

### Customize configuration

See [Configuration Reference](https://cli.vuejs.org/config/).
