# kfc-kmx-iframe

An iframe component specially designed for kmx pages

## maintainer

zhangzhenbang

## install
```bash
git remote add -f kfc-kmx-iframe git@github.com:k2jf/kfc-kmx-iframe.git
git subtree add -P src/components/kfc-kmx-iframe kfc-kmx-iframe master --squash
```

> important: this components RELY on centain constant in `src/config/index.js`, namely:
> - casLoginUrl: the entry point for iframe'd webpages.
> - iframeTimeoutInMillis: how long before failing with blank page.
> so, you have to add these configs inside `src/config/index.js` like
> ```js
> {
>     casLoginUrl: process.env.VUE_APP_CAS_LOGIN_URL || 'https://bigdata.goldwind.com.cn:8443/cas/login',
>     iframeTimeoutInMillis: process.env.IFRAME_TIMEOUT_MS || 30000
> }
> ```
> 

#### also note
... there is another param `K2Key`, which has been externalized as
a **function** inside `src/components/kfc-iframe/api/index.js`. Currently
only a **mock** value is coded there. You have to provide with your
own implementation.

#### then you can use it by simply put it inside `<template>`
```html
<template>
  <KmxFrame :targetUrl="targetUrl"></KmxFrame>
</template>
```
where `targetUrl` is the desired page you want to embed.

> **note**: you may find that the iframe is too narrow as just a ribbon.  
> That's because the outer element has 'auto' height.  
> In this case, you have to specify `"position: relative"` and `"height: ...px"` yourself.

## other props
- **cutOffBreadCrumb**: Number or Boolean
  controls how much whitespace on the top side should the page be cut off.
  - false (default): only cut off the kmx frameset header
  - true: will also cut off the kmx page breadcrumb
  - custom (a number): manually control the bleeding

```
  +-----------------------------+
  + x x  (cut-off bleeding) x x +
  +---+-------------------------+
  + x |                         +
  + x |                         +
  + x |                         +
  + x |                         +
  + x |                         +
  +---+-------------------------+
```

## limitation
- kmx pages are currently all coated with frameset, so css-hack is applied
to cut it off. In the future the web pages may stand on their own, and I
will try to make it look the same then. 
