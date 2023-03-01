# Front APP

- Install vue router `npm i vue-router@4` (v.4 compatibile con Vue 3)
- Create a router.js file next a main.js `cd src && touch router.js`
- Inside router.js define the router contant and export it

```js
// 1 Import from vue-router createRouter and createWebHistory
import { createRouter, createWebHistory } from 'vue-router'

// 2 define routes
const router = createRouter({
  history: createWebHistory(),
  routes: [
    /* All routes here */
  ]
})

// 3. export the router
export { router }


```

## Define all routes inside the routes option

```js

routes: [
     {
      path: '/',
      name: 'home',
      component: HomeView
    },
    {
      path: '/about',
      name: 'about',
      component: AboutView
    },
  ]

```

NOTE: import the components for the component property

## Create and import the Router views
  
```bash
cd src
mkdir views
cd views
touch HomeView.vue
touch AboutView.vue
touch ContactsView.vue
touch BlogView.vue

```

Define a HomeView.vue file

```js
<script>

export default {
  name: 'HomeView',


}
</script>

<template>
  
  <div class="container">
    <h1>Home Page</h1>
    <p class="lead">
      Lorem ipsum dolor ...</p>
    <p>Lorem ipsum ...</p>
  </div>
</template>


<style lang="scss" scoped>

</style>


```

Repeat for the other pages. Then update the router.js file and import all views components. Your route file should look like the following:

```js
import { createRouter, createWebHistory } from 'vue-router'
/* Import all components to pass to the routes */
import HomeView from './views/HomeView.vue'
import AboutView from './views/AboutView.vue'
import BlogView from './views/BlogView.vue'
import ContactsView from './views/ContactsView.vue'


const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView
    },
    {
      path: '/about',
      name: 'about',
      component: AboutView
    },
    {
      path: '/blog',
      name: 'blog',
      component: BlogView
    },
    {
      path: '/contacts',
      name: 'contacts',
      component: ContactsView
    }
  ]
})


export { router }

```

Import the router instance in the main.js file and instruct Vue to use vue router

```js
import { createApp } from 'vue'
import App from './App.vue'
/* Import the router  */
import { router } from './router'
/* Tell Vue to use our router */
createApp(App).use(router).mount('#app')
```

## Updata main App.vue component

Use the router-view and rouer-link components

App.vue

```js
<script>
import AppNav from './components/AppNav.vue'

export default {
    components: {
        AppNav

    },


}
</script>

<template>

    <AppNav></AppNav>
    <router-view></router-view>
</template>

<style lang="scss">
@use './styles/general.scss';
</style>


```

Use router-link in your navigation. Edit the AppNav component (or whatever you called it)

```js
 <li class="nav-item">
    <router-link class="nav-link" :to="{ name: 'home' }" aria-current="page">Home <span
        class="visually-hidden">(current)</span></router-link>
  </li>

```

Note: replaced the anchor tag `a` with with the `router-link` component and use these syntax
`:to="{ name: 'home' }"` to point to a route.

## Add a single post

To add the single post page, you can add a route with a paramenter using the following syntax

```js
 {
      path: '/blog/:slug',
      name: 'single-post',
      component: SinglePostView
    },
```

Note: the path value uses :slug as the paramenter to recover the single post.
Note2: remember to import the SinglePostView component at the top.

### Add a SinglePostView component

```js
<script>
import axios from 'axios'

export default {
  name: 'SinglePostView',
 
  data() {
    return {
     
    }
  },
  mounted() {
    //API call will go here;    
  }
}
</script>

<template>
  
  <div class="single-post" >
    {{$route.params.slug}}
  
   {/* Your post data here */}
  </div>
</template>


<style lang="scss" scoped>

</style>

```

The special $route property gives us access to the params property and inside we can find our slug paramenter that
we can render onto the page.

## Edit the laravel App

Add to the laravel app a route to handle API calls for the single post

```php

Route::get('/posts/{post:slug}', [PostController::class, 'show']);

```

Implement the show method in the API/PostController

```php
 public function show($slug)
    {
        /* Show the single post as json */
        $post = Post::with('category', 'tags', 'user')->where('slug', $slug)->first();
        //dd($post);
        if ($post) {
            return response()->json([
                'success' => true,
                'results' => $post
            ]);
        } else {
            return response()->json([
                'success' => false,
                'results' => 'Post Not Found'
            ]);
        }
    }

```

The if checks if the post returns an instance of the Post model, if not NULL is returned and we can show
the appropriate json response.

## VUE APP

Back to our vue app, now we can edit the singlePostView and add an ajax call to the newly created endpoint

```js
data() {
    return {
      post: null,
      loading: true,
      api_base_url: 'http://localhost:8001'
    }
  },
  mounted() {
    //console.log(this.$route.params.slug);
    const url = this.api_base_url + '/api/posts/' + this.$route.params.slug
    console.log(url);
    axios.get(url)
      .then(response => {
        if (response.data.success) {
          this.post = response.data.results
          this.loading = false
        } else {
          /* TODO: handle the not found post  
          404 
          */
          // https://router.vuejs.org/guide/essentials/navigation.html#navigate-to-a-different-location
        }
        console.log(response);
      }).catch(error => {
        console.log(error)
      })

  }
```

## Add 404 Page

If the user enters an url that does NOT exist we need to show a 404 page
first create a route

```js
 {
      path: '/:pathMatch(.*)*',
      name: 'not-found',
      component: NotFoundView
    },

```

then create the view component

```js
<script>

export default {
  name: 'NotFoundView',


}
</script>

<template>

  <div class="container">
    <p class="lead">
      😨 Ops, Page not found!
    </p>
    <router-link to="/">Go to Homepage</router-link>

  </div>

</template>


<style lang="scss" scoped>

</style>

```

now if all worked you shoud be able to see the 404 page when visiting an url like `/sopdijfgspdioajf`
but still see the SinglePostView when typing `/blog/skdfpaos` where `skdfpaos` should be the slug of a post that does not exist.

Handle that issue in the else block in the SinglePostView

```js
axios.get(url)
      .then(response => {
        if (response.data.success) {
          this.post = response.data.results
          this.loading = false
        } else {
          /* TODO: handle the not found post  
          404 
          */
          // https://router.vuejs.org/guide/essentials/navigation.html#navigate-to-a-different-location
        }
        console.log(response);
      }).catch(error => {
        console.log(error)
      })


```