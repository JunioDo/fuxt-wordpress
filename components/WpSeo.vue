<!--
    This component sets the heads tags based on the route.
    Mostly you do not need to change this, unless your frontend
    routes don't match WordPress routes. Then you can supply a custom path as a prop.

    If you use custom post types, then you will need to change the `~/gql/queries/Seo.gql` file.

    This file is included in Default layout, but on complex sites you
    may want to use it on pages templates as well to have more control over the SEO tags set.
-->
<template lang="html">
    <div
        :key="parsedUri"
        class="wp-seo"
    >
        <!-- Print content to page for SEO gain -->
        <h1
            v-if="parsedTitle"
            v-html="parsedTitle"
        />
        <div
            v-if="parsedDescription"
            v-html="parsedDescription"
        />
    </div>
</template>

<script>
// Helpers
import getStripped from "~/utils/getStripped"

// Queries
import WP_SEO from "~/gql/queries/WpSeo.gql"

export default {
    props: {
        title: {
            type: String,
            default: ""
        },
        imageUrl: {
            type: String,
            default: ""
        },
        description: {
            type: String,
            default: ""
        },
        path: {
            type: String,
            default: ""
        }
    },
    data() {
        return {
            data: {}
        }
    },
    async fetch() {
        // Abort if no path supplied (often because used on homepage)
        if (
            !this.parsedUri ||
            this.parsedUri == "/" ||
            this.parsedUri == "/wp-admin/"
        ) {
            this.data = {}
            return this.data
        }

        // Get data from API
        try {
            const data = await this.$graphql.default.request(WP_SEO, {
                uri: this.parsedUri
            })
            this.data = data.nodeByUri || {}
        } catch (e) {
            console.warn("<wp-seo> Fetch Error:", this.parsedUri, e)
        }

        // Log a warning if nothing returned from server for this route
        if (!this.data || !this.data.id) {
            console.warn(
                "WpSeo fetch came back empty. The path prop probably needs to be set manually for this route.",
                this.parsedUri
            )
        }
    },
    fetchKey(getCounter) {
        return `${this.parsedUri}-${getCounter(this.parsedUri)}`
    },
    head() {
        return {
            title: this.parsedTitle,
            meta: [
                {
                    hid: "og:description",
                    name: "description",
                    property: "og:description",
                    content: this.parsedDescription
                },
                {
                    hid: "twitter:description",
                    name: "twitter:description",
                    property: "twitter:description",
                    content: this.parsedDescription
                },
                {
                    hid: "og:image",
                    property: "og:image",
                    content: this.parsedImageUrl
                },
                {
                    hid: "og:title",
                    property: "og:title",
                    content: this.parsedTitle
                }
            ]
        }
    },
    computed: {
        parsedUri() {
            let output = this.path
            if (!output) {
                // Strip page cursors as those never match anything in WordPress
                output = this.$route.path.replace(
                    `/page/${this.$route?.params?.cursor || ""}/`,
                    ""
                )
            }
            return output
        },
        parsedTitle() {
            let output = this.title

            // Try to set title from data, fallback to site title
            if (!output) {
                output = this.data.title || ""
            }
            if (!output) {
                output = this.data.name || ""
            }
            if (!output) {
                output = this.$store.state.siteMeta.title || undefined
            }

            return output
        },
        parsedDescription() {
            let output = this.description

            // Try to set description from page excerpt or content, fallback to site discription
            if (!output) {
                output = getStripped(this, "data.excerpt", "")
            }
            if (!output) {
                output = getStripped(this, "data.content", "")
            }
            if (!output) {
                output = getStripped(this, "data.description", "")
            }
            if (!output) {
                output = getStripped(
                    this,
                    "$store.state.siteMeta.description",
                    undefined
                )
            }

            return output
        },
        parsedImageUrl() {
            let output = this.image

            if (!output) {
                output = this.data.featuredImage?.node?.sourceUrl || undefined
            }
            if (!output) {
                output =
                    this.$store.state.siteMeta.socialSharedImage || undefined
            }
            if (!output) {
                output =
                    this.$store.state.siteMeta.themeScreenshotUrl || undefined
            }

            // Use supplied image URL, or image from server, fallback to theme screenshot
            return output
        }
    },
    watch: {
        "$route.path"() {
            this.$fetch()
        }
    },
    activated() {
        // This is a cache for Fetch. Will call fetch again if last fetch more than 60 sec ago
        if (this.$fetchState.timestamp <= Date.now() - 60000) {
            this.$fetch()
        }
    }
}
</script>

<style lang="scss" scoped>
.wp-seo {
    display: none;
}
</style>
