# Nuxt.js Cheat Sheet

A comprehensive reference for Nuxt.js - the intuitive Vue framework for building universal applications.

---

## Table of Contents
- [Installation and Setup](#installation-and-setup)
- [Project Structure](#project-structure)
- [Pages and Routing](#pages-and-routing)
- [Components](#components)
- [Layouts](#layouts)
- [Assets and Static Files](#assets-and-static-files)
- [Data Fetching](#data-fetching)
- [Server-Side Rendering](#server-side-rendering)
- [Plugins](#plugins)
- [Modules](#modules)
- [Configuration](#configuration)
- [Deployment](#deployment)

---

## Installation and Setup

### Installation
```bash
# Create new Nuxt app
npx create-nuxt-app my-project
# or
yarn create nuxt-app my-project

# With Nuxt 3
npx nuxi@latest init my-project

# Start development server
npm run dev
# or
yarn dev
```

### Basic Configuration
```javascript
// nuxt.config.js
export default {
  // Target
  target: 'static', // or 'server'
  
  // Global page headers
  head: {
    title: 'My Nuxt App',
    htmlAttrs: {
      lang: 'en'
    },
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: 'My amazing Nuxt app' }
    ],
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  },
  
  // Global CSS
  css: [
    '~/assets/css/main.css'
  ],
  
  // Plugins
  plugins: [
    '~/plugins/vue-notifications.js'
  ],
  
  // Modules
  modules: [
    '@nuxtjs/axios',
    '@nuxtjs/pwa'
  ],
  
  // Build configuration
  build: {
    // Build config
  }
}
```

## Project Structure

### Nuxt Directory Structure
```
my-project/
├── assets/         # Uncompiled assets (LESS, Sass, JS)
├── components/     # Vue components
├── layouts/        # Application layouts
├── middleware/     # Custom middleware
├── pages/          # Application views and routes
├── plugins/        # JavaScript plugins
├── static/         # Static files
├── store/          # Vuex store files
├── nuxt.config.js  # Nuxt configuration
└── package.json
```

## Pages and Routing

### File-based Routing
```bash
pages/
├── index.vue          # /
├── about.vue          # /about
├── contact.vue        # /contact
├── users/
│   ├── index.vue      # /users
│   ├── _id.vue        # /users/:id
│   └── _slug.vue      # /users/:slug
├── blog/
│   ├── index.vue      # /blog
│   ├── _slug.vue      # /blog/:slug
│   └── _category/
│       └── _slug.vue  # /blog/:category/:slug
└── _.vue              # Catch-all route
```

### Dynamic Routes
```vue
<!-- pages/_id.vue -->
<template>
  <div>
    <h1>User ID: {{ $route.params.id }}</h1>
    <p>{{ user.name }}</p>
  </div>
</template>

<script>
export default {
  async asyncData({ params, $axios }) {
    const user = await $axios.$get(`/api/users/${params.id}`)
    return { user }
  }
}
</script>

<!-- pages/blog/_category/_slug.vue -->
<template>
  <div>
    <h1>{{ post.title }}</h1>
    <p>Category: {{ $route.params.category }}</p>
    <div v-html="post.content"></div>
  </div>
</template>

<script>
export default {
  async asyncData({ params, $axios }) {
    const post = await $axios.$get(`/api/posts/${params.category}/${params.slug}`)
    return { post }
  }
}
</script>
```

### Navigation
```vue
<template>
  <div>
    <!-- nuxt-link for internal navigation -->
    <nuxt-link to="/">Home</nuxt-link>
    <nuxt-link to="/about">About</nuxt-link>
    <nuxt-link :to="`/users/${user.id}`">User Profile</nuxt-link>
    
    <!-- Programmatic navigation -->
    <button @click="goToAbout">Go to About</button>
    
    <!-- Active link classes -->
    <nuxt-link to="/blog" active-class="active" exact-active-class="exact-active">
      Blog
    </nuxt-link>
  </div>
</template>

<script>
export default {
  methods: {
    goToAbout() {
      this.$router.push('/about')
    }
  }
}
</script>
```

## Components

### Auto-imported Components
```vue
<!-- components/TheHeader.vue -->
<template>
  <header>
    <nav>
      <nuxt-link to="/">{{ siteName }}</nuxt-link>
      <ul>
        <li><nuxt-link to="/blog">Blog</nuxt-link></li>
        <li><nuxt-link to="/about">About</nuxt-link></li>
      </ul>
    </nav>
  </header>
</template>

<script>
export default {
  props: {
    siteName: {
      type: String,
      default: 'My Site'
    }
  }
}
</script>

<!-- Usage in pages/layouts (auto-imported) -->
<template>
  <div>
    <TheHeader :site-name="siteName" />
    <!-- Other content -->
  </div>
</template>
```

### Async Components
```vue
<!-- components/LazyComponent.vue -->
<template>
  <div>
    <h2>Lazy Component</h2>
    <p>This component is loaded asynchronously</p>
  </div>
</template>

<!-- Usage -->
<template>
  <div>
    <LazyLazyComponent />
  </div>
</template>
```

## Layouts

### Default Layout
```vue
<!-- layouts/default.vue -->
<template>
  <div>
    <TheHeader />
    <main>
      <Nuxt />
    </main>
    <TheFooter />
  </div>
</template>

<style>
html {
  font-family: 'Source Sans Pro', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}

*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
}
</style>
```

### Custom Layouts
```vue
<!-- layouts/blog.vue -->
<template>
  <div class="blog-layout">
    <header class="blog-header">
      <h1>My Blog</h1>
      <nav>
        <nuxt-link to="/blog">All Posts</nuxt-link>
        <nuxt-link to="/blog/categories">Categories</nuxt-link>
      </nav>
    </header>
    
    <div class="blog-content">
      <aside class="blog-sidebar">
        <BlogSidebar />
      </aside>
      <main class="blog-main">
        <Nuxt />
      </main>
    </div>
  </div>
</template>

<!-- Using custom layout -->
<!-- pages/blog/index.vue -->
<template>
  <div>
    <h2>Blog Posts</h2>
    <!-- Blog content -->
  </div>
</template>

<script>
export default {
  layout: 'blog'
}
</script>
```

### Error Layout
```vue
<!-- layouts/error.vue -->
<template>
  <div class="container">
    <h1 v-if="error.statusCode === 404">Page not found</h1>
    <h1 v-else>An error occurred</h1>
    <p>{{ error.message }}</p>
    <nuxt-link to="/">Home page</nuxt-link>
  </div>
</template>

<script>
export default {
  props: ['error']
}
</script>
```

## Assets and Static Files

### Assets Directory
```vue
<template>
  <div>
    <!-- Will be processed by Webpack -->
    <img src="~/assets/images/logo.png" alt="Logo">
    
    <!-- SCSS/CSS -->
    <div class="styled-component">
      Content with styles
    </div>
  </div>
</template>

<style lang="scss" scoped>
@import '~/assets/scss/variables.scss';

.styled-component {
  background-color: $primary-color;
  padding: 20px;
}
</style>
```

### Static Directory
```vue
<template>
  <div>
    <!-- Served as-is -->
    <img src="/images/hero.jpg" alt="Hero">
    <link rel="stylesheet" href="/css/external.css">
  </div>
</template>
```

## Data Fetching

### asyncData
```vue
<template>
  <div>
    <h1>{{ post.title }}</h1>
    <p>{{ post.content }}</p>
  </div>
</template>

<script>
export default {
  async asyncData({ params, $axios, error }) {
    try {
      const post = await $axios.$get(`/api/posts/${params.slug}`)
      return { post }
    } catch (e) {
      error({ statusCode: 404, message: 'Post not found' })
    }
  }
}
</script>
```

### fetch Hook
```vue
<template>
  <div>
    <h1>Posts</h1>
    <div v-if="$fetchState.pending">Loading posts...</div>
    <div v-else-if="$fetchState.error">Error loading posts</div>
    <div v-else>
      <div v-for="post in posts" :key="post.id">
        <h2>{{ post.title }}</h2>
        <p>{{ post.excerpt }}</p>
      </div>
    </div>
    <button @click="$fetch">Refresh</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      posts: []
    }
  },
  
  async fetch() {
    this.posts = await this.$axios.$get('/api/posts')
  },
  
  fetchOnServer: false, // Only fetch on client-side
}
</script>
```

### Nuxt 3 Data Fetching
```vue
<!-- Nuxt 3 -->
<template>
  <div>
    <h1>{{ data.title }}</h1>
    <p v-if="pending">Loading...</p>
    <p v-else-if="error">Error: {{ error }}</p>
    <div v-else>
      {{ data.content }}
    </div>
  </div>
</template>

<script setup>
// useFetch
const { data, pending, error, refresh } = await useFetch('/api/posts/1')

// useLazyFetch (doesn't block navigation)
const { data: posts } = await useLazyFetch('/api/posts')

// $fetch (reactive)
const data = await $fetch('/api/posts')
</script>
```

## Server-Side Rendering

### Universal Mode
```javascript
// nuxt.config.js
export default {
  mode: 'universal', // or 'spa'
  
  // Render configuration
  render: {
    // Configure server-side rendering
    bundleRenderer: {
      shouldPrefetch: (file, type) => {
        return ['script', 'style', 'font'].includes(type)
      }
    }
  }
}
```

### SSR Context
```vue
<script>
export default {
  asyncData(context) {
    // Server-side context
    const {
      params,
      query,
      req,
      res,
      redirect,
      error,
      store
    } = context
    
    if (process.server) {
      // Server-side only
      console.log('Running on server')
    }
    
    if (process.client) {
      // Client-side only
      console.log('Running on client')
    }
  }
}
</script>
```

## Plugins

### Creating Plugins
```javascript
// plugins/vue-notifications.js
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)

// plugins/axios.js
export default function ({ $axios, redirect }) {
  $axios.onRequest(config => {
    console.log('Making request to ' + config.url)
  })

  $axios.onError(error => {
    const code = parseInt(error.response && error.response.status)
    if (code === 400) {
      redirect('/400')
    }
  })
}

// Register in nuxt.config.js
export default {
  plugins: [
    '~/plugins/vue-notifications.js',
    { src: '~/plugins/axios.js', ssr: true },
    { src: '~/plugins/client-only.js', mode: 'client' }
  ]
}
```

### Inject into Context
```javascript
// plugins/api.js
export default ({ $axios }, inject) => {
  const api = {
    async getPosts() {
      return await $axios.$get('/api/posts')
    },
    
    async getPost(id) {
      return await $axios.$get(`/api/posts/${id}`)
    }
  }
  
  // Inject $api
  inject('api', api)
}

// Usage in components
export default {
  async asyncData({ $api }) {
    const posts = await $api.getPosts()
    return { posts }
  },
  
  methods: {
    async loadMore() {
      const posts = await this.$api.getPosts()
      this.posts.push(...posts)
    }
  }
}
```

## Modules

### Built-in Modules
```javascript
// nuxt.config.js
export default {
  modules: [
    '@nuxtjs/axios',
    '@nuxtjs/pwa',
    '@nuxtjs/sitemap',
    '@nuxtjs/robots'
  ],
  
  // Module configuration
  axios: {
    baseURL: 'https://api.example.com'
  },
  
  pwa: {
    workbox: {
      // Workbox configuration
    }
  }
}
```

### Custom Modules
```javascript
// modules/custom-module.js
export default function CustomModule(moduleOptions) {
  // this.options = Nuxt options
  // this.nuxt = Nuxt instance
  
  this.addPlugin({
    src: path.resolve(__dirname, 'plugin.js'),
    options: moduleOptions
  })
  
  this.extendBuild((config) => {
    // Extend webpack config
  })
}

// Register module
export default {
  modules: [
    ['~/modules/custom-module', { option: 'value' }]
  ]
}
```

## Configuration

### Environment Variables
```javascript
// nuxt.config.js
export default {
  env: {
    baseUrl: process.env.BASE_URL || 'http://localhost:3000'
  },
  
  publicRuntimeConfig: {
    baseURL: process.env.BASE_URL || 'http://localhost:3000'
  },
  
  privateRuntimeConfig: {
    apiSecret: process.env.API_SECRET
  }
}
```

### Runtime Config
```vue
<template>
  <div>
    <p>Base URL: {{ $config.baseURL }}</p>
  </div>
</template>

<script>
export default {
  async asyncData({ $config, $axios }) {
    // Access runtime config
    const posts = await $axios.$get('/posts', {
      baseURL: $config.baseURL
    })
    
    return { posts }
  }
}
</script>
```

### Build Configuration
```javascript
// nuxt.config.js
export default {
  build: {
    // Analyze bundle
    analyze: true,
    
    // Extend webpack config
    extend(config, { isDev, isClient }) {
      if (isDev) {
        config.mode = 'development'
      }
    },
    
    // Babel configuration
    babel: {
      presets({ isServer }) {
        return [
          ['@nuxt/babel-preset-app', {
            targets: isServer ? { node: 'current' } : { ie: '11' }
          }]
        ]
      }
    }
  }
}
```

## Deployment

### Static Generation
```javascript
// nuxt.config.js
export default {
  target: 'static',
  
  generate: {
    routes: [
      '/posts/1',
      '/posts/2',
      '/posts/3'
    ],
    
    // Dynamic routes
    async routes() {
      const { data } = await axios.get('https://api.example.com/posts')
      return data.map(post => `/posts/${post.id}`)
    }
  }
}
```

```bash
# Generate static site
npm run generate

# Serve generated site
npm run start
```

### Server Deployment
```javascript
// nuxt.config.js
export default {
  server: {
    port: process.env.PORT || 3000,
    host: process.env.HOST || 'localhost'
  }
}
```

### Docker Deployment
```dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
```

### Netlify/Vercel
```bash
# Build command
npm run generate

# Publish directory  
dist
```

---

## Best Practices

### SEO Optimization
```vue
<template>
  <div>
    <h1>{{ post.title }}</h1>
    <p>{{ post.content }}</p>
  </div>
</template>

<script>
export default {
  head() {
    return {
      title: this.post.title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.post.excerpt
        },
        // Open Graph
        {
          hid: 'og:title',
          property: 'og:title',
          content: this.post.title
        },
        {
          hid: 'og:description',
          property: 'og:description',
          content: this.post.excerpt
        }
      ]
    }
  },
  
  async asyncData({ params, $axios }) {
    const post = await $axios.$get(`/api/posts/${params.slug}`)
    return { post }
  }
}
</script>
```

### Performance Tips
```javascript
// nuxt.config.js
export default {
  // Prefetch links
  router: {
    prefetchLinks: false
  },
  
  // Bundle analyzer
  build: {
    analyze: {
      analyzerMode: 'server'
    }
  },
  
  // Compression
  render: {
    compressor: require('compression')()
  }
}
```

---

## Resources
- [Official Nuxt.js Documentation](https://nuxtjs.org)
- [Nuxt 3 Documentation](https://v3.nuxtjs.org)
- [Nuxt.js Examples](https://github.com/nuxt/nuxt.js/tree/dev/examples)
- [Nuxt Community](https://github.com/nuxt-community)
- [Awesome Nuxt](https://github.com/nuxt-community/awesome-nuxt)

---
*Originally compiled from various sources. Contributions welcome!*