# Galexia Nuxt Component Share

This component is a wrapper around the [Vue Social Sharing](https://www.npmjs.com/package/vue-social-sharing) component.
It defaults to the [`Navigator.share()`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/share) method that's available natively in some browsers.
If `Navigator.share()` is not available, we use Vue Social Sharing instead.

## Install

We assume you have a Nuxt project.

```bash
yarn add https://github.com/Galexia-Agency/nuxt-component-share
```

```js
// plugins/galexia/nuxt-components/share.js
import Vue from 'vue'
import Galexia_NuxtComponent_Share from 'nuxt-component-share/index.vue'

Vue.component('GalexiaShare', Galexia_NuxtComponent_Share)
```

```js
// nuxt.config.js
...
plugins: [
  '~plugins/galexia/components/share.js'
],
...
```

### Use it like so

```js
// pages/index.js
<GalexiaShare
  title="Page Title"
  titleText="Share Options:"
  description="Page Description"
/>
```
