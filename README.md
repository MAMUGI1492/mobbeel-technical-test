# Mobbeel technical test

[![Netlify Status](https://api.netlify.com/api/v1/badges/338f59f1-ca39-436f-8390-1d0b098d6371/deploy-status)](https://app.netlify.com/sites/elegant-lewin-ac6893/deploys)

## Description

The project has been made with the maximum possible simplicity in mind, making the selection of the 2 flows a tab selector and (apart from the inputs of each flow) only one more button to finalize the process.

The main difficulty encountered was sending the form to the endpoint in FormData format, with which it has not worked for some time, but once the relevant documentation was reviewed, it could be done without any problem.

## Demo

The project has been hosted on [Netlify](https://www.netlify.com/), and as we talked about including the possibility of not being indexed by search engines, as well as a URL difficult to access.

The link would be as follows: [demo](https://elegant-lewin-ac6893.netlify.app/)

## DIY

In case you want to do the compilation yourself locally, you just need to:

### Install the dependencies

```bash
npm install
```

### Start the app in development mode (hot-code reloading, error reporting, etc.)

```bash
quasar dev
```

### Build the app for production

```bash
quasar build
```

## Stack

The stack chosen was the v2 of the Vue metaframework, [Quasar](https://quasar.dev/), which apart from using the v3 of Vue, and all its attached libraries (Vue Router, Vuex, Vue-I18n...) allows you to implement compilations in a very fast way for PWA, Cordova/Capacitor (Android/iOS), Electron (PC). As well as an endless number of visual components, and utilities of all kinds ready to use.

As Languages I have chosen to use JavaScript, along with HTML and CSS (over the SASS preprocessor, in its syntactic variant of SCSS).

It is also worth mentioning that for the consumption of the different APIs I have chosen to use [Axios](https://axios-http.com/) instead of its native variant [Fecth API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API), due to greater familiarity.

## License

MIT
