# fuxt

A complete Headless WordPress tech stack built on Nuxt, using GraphQL.


## Features

-   Optimized for Netlify deployment. Includes custom WordPress plugin for manual and auto deploys.
-   WordPress Previews and Drafts work with regular WordPress logins. `Preview` token in requests to work with CDNs.
-   Includes a WordPress admin bar on frontend for logged in users.
-   Supports Gutenberg blocks on frontend and backend!
-   Includes [Nuxt Storybook module](https://storybook.nuxtjs.org/)!
-   Includes custom module for nuxtGenerateInit. Populate the store once at the beginning of Generate!
-   Optimized WordPress theme:
    -   Auto installs any WordPress plugin dependencies
    -   GraphQL powered API using [Nuxt GraphQL Request](https://www.npmjs.com/package/nuxt-graphql-request)
    -   [ACF](https://www.advancedcustomfields.com/) first class citizen
    -   Includes GQL email mutation to send email from frontend via backend
-   Google Analytics plugin baked in, pulls from WordPress dashboard (requires [ACF Pro](https://www.advancedcustomfields.com/pro/))
-   Includes lots of components for common WordPress needs
    -   wp-gutenberg (supports custom frontend gutenberg components)
    -   wp-menu and wp-menu-item (supports sub-menus!)
    -   wp-image (uses WordPress generated src-sets and much more!)
    -   wp-link (handles parsing WordPress permalinks)
-   Built in webfontloader for fast font loading
-   Uses Nuxt Auto Components
-   Built in proxy server, to make secure HTTP requests via WordPress
-   Lots of common functions to save you time!
    -   Mobile swipe events baked in
    -   Solve for [100vh on mobile](https://stackoverflow.com/questions/58886797/how-to-access-the-real-100vh-on-ios-in-css) baked in
    -   Tracks window dimensions and scroll top position into Vuex
    -   IP Stack region detection built in (requires API key)
    -   Lodash baked in
    -   Prettier and ESLint built in for nice looking code

## Build Setup

**This is just a [Nuxt site](https://nuxtjs.org), so it builds and deploys like any other Nuxt project.**

Works best with the  WordPress theme as the backend.

**First step:** Duplicate and rename `.example.env` to `.env`. Define any vars environment needed there.

```bash
# install dependencies
$ npm install

# serve with hot reload at localhost:3000
$ npm run dev

# serve with hot reload Storybook at localhost:3003
$ npm run storybook

# build for production and launch server
$ npm run build
$ npm start

# build Storybook for production
$ npx nuxt storybook build

# generate static project
$ npm run generate

```

## Documentation

For detailed explanation on how things work, checkout.
