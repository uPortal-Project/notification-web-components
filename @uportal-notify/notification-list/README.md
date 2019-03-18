# Notification List

[![NPM Version](https://img.shields.io/npm/v/@uportal-notify/notification-list.svg)](https://www.npmjs.com/package/@uportal-notify/notification-list)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/org.webjars.npm/uportal__notification-list/badge.svg)](https://maven-badges.herokuapp.com/maven-central/org.webjars.npm/uportal__notification-list)
[![Build Status](https://travis-ci.org/uPortal-contrib/notification-web-components.svg?branch=master)](https://travis-ci.org/uPortal-contrib/notification-web-components)

## Development

```bash
# clone the repository if you haven't already
git clone https://github.com/uPortal-contrib/notification-web-components

# navigate to the notification-list folder
cd uPortal-web-components/@uportal-notify/notification-list

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
You can now place `<notification-list>` anywhere on your page.

Here's the guts of that html file:

```html
<notification-list>
  
</notification-list>
```

- Note: The component includes bootstrap, so that you can use bootstrap's styles in your content slots. However, font-awesome has issues crossing the shadow-dom boundry, so that if you include an icon in the content slot, you also need to include a `<link>` to the font-awesome stylesheet from uPortal (or elsewhere if you wish).

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

`<notification-list>` takes the following attributes:

### `oidc-url`

Url to connect to oidc. The default for this value is `/uPortal/api/v5-1/userinfo`.

```html
<notification-list oidc-url="/uPortal/api/v5-1/userinfo"></notification-list>
```

### Theming

Currently this component supports [CSS Variables](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables) for overriding button colors. Defining the following variables will change the colors for the component accordingly. They will fall back to the colors described below.

You should define this in your custom stylesheet.

fg = foreground (text)
bg = background

```css
:root {
}
```
