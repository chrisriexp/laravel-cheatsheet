# Laravel Cheat-Sheet

> Create a Laravel project standalone or as a full-stack application using Vue.js 3 with Vite and Tailwind CSS

## Create Laravel Project

> Replace "project-name" with the app name

`composer create-project --prefer-dist laravel/laravel project-name`

## Setup Vue.js

**Install Vue.js and Vite**

> cd into the new project

`npm install vue@latest vue-loader@next @vitejs/plugin-vue`

**Import Vue.js into Laravel**

> Navigate to the "vite.config.js" file in the root dir
>
> Import Vue.js and add it to the "plugins" array

```Javascript
// vite.config.js

...
import vue from '@vitejs/plugin-vue'

export default defineConfig({
    plugins: [
        vue(),
        laravel({
          ...
        })
    ],
});
```

**Setup App.vue**

> Navigate to the "resource" folder in the root dir
>
> Create "App.vue" and paste the below

```Vue
<!-- resources/App.vue -->

<template>
    <router-view/>
</template>

<script>
export default {
    name: "App"
}
</script>

<style>
/**Font from google font */
#app {
  font-family: 'Montserrat', sans-serif;
}
body {
  background-color: #F5F5F5;
}
@media (-webkit-device-pixel-ratio: 1.10) {
    :root {
      zoom: 0.90;
    }
}
/* Chrome, Safari, Edge, Opera */
input[type="number"]::-webkit-outer-spin-button,
input[type="number"]::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}
/* Firefox */
input[type="number"] {
    -moz-appearance: textfield;
}

input[type="text"]:focus{
  outline: none !important;
  --tw-ring-color: transparent !important;
}
</style>
```

**Add App.vue to app.js**

> Navigate to the "js" folder inside "resources"
>
> Replace the content in the "app.js" file with the below

```Javascript
// resources/js/app.js

import './bootstrap'
import {createApp} from 'vue'
import App from '../App.vue'

createApp(App).mount("#app")
```

**Update Laravel Blade to use Vue.js views**

> Navigate to "views" inside the "resources" folder
>
> Replace the content in the "head" tag of "welcome.blade.php" with the below

```HTML
<!-- resources/views/welcome.blade.php -->

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <!-- ... (styles and fonts) -->
  <title>Laravel Project</title>
  @vite('resources/css/app.css')
</head>
```

> Replace the content in the "body" tag of "welcome.blade.php" with the below

```HTML
<!-- resources/views/welcome.blade.php -->
<!-- remove the class from the body tag -->
<body>
  <div id="app"></div>
  @vite('resources/js/app.js')
</body>
```

**Setup Vue Router**

> Install Vue Router

`npm install vue-router --save`

> Navigate to the "js" folder in "resources"
>
> Create a new folder in "js" called "views"
>
> Create a new file in the "views" folder called "Home.vue" and paste the below

```Vue
<!-- resources/js/views/Home.vue -->

<template>
    <h1 class="text-red-400">Hello World</h1>
</template>

<script>
export default {
    name: "Home"
}
</script>
```

> Navigate back to the "js" folder in "resources"
>
> Create a new file called "router.js" in the "js" folder and paste the below

```Javascript
// resources/js/router.js

import {createRouter, createWebHistory} from 'vue-router';
// import views to be used in the web router
import Home from './views/Home.vue'
// ... (additional view imports)

const routes = [
  {
      // Catch all for routes that do not exist
      path: "/:catchAll(.*)",
      name: "NotFound",
      component: NotFound,
  },
  {
      path: '/',
      name: "Home",
      component: Home
  },
  {
      // restricted view that required authentication to enter
      path: '/dashboard',
      name: "Dashboard",
      component: Dashboard,
      beforeEnter: validateAccessToken
  },
  // ...
]

const router = createRouter({
    history: createWebHistory(),
    routes
})

async function validateAccessToken(to, from, next) {
  ... (validation to make sure access token is valid to enter a restricted route)
  next();
}

export default router
```

**Add router.js to app.js**

> Navigate to the "app.js" file in the "js" folder and import the new router file to be used

```Javascript
... (other imports into the Vue app)
// import router file
import router from './router.js'

createApp(App)
... (other dependencies into Vue)
// use the router in the Vue app
.use(router)
.mount("#app")
```

**Update Laravel Web Routes to use Vue Router**

> Navigate to the routes folder in the root dir
>
> Replace the "web.php" file content with the below

```PHP

<?php
// routes/web.php

use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider and all of them will
| be assigned to the "web" middleware group. Make something great!
|
*/

// Uses the Vue Router for any route not defined before this
Route::get('/{vue_capture?}', function() {
    return view('welcome');
})->where('vue_capture', '[\/\w\.-]*');
```

## Add Tailwind CSS to the Project

**Install Tailwind**

`npm install -D tailwindcss@latest postcss@latest autoprefixer@latest`

> This will create a "tailwind.config.js" file in the root dir

`npx tailwindcss init -p`

> Open the "tailwind.config.js" file in this repo and paste the contents
>
> Check the bottom of the "tailwind.config.js" file to see if it includes "@tailwind/forms" in the "plugins" array; if so install the dependency

`npm install @tailwindcss/forms --save`

> Navigate to the "resources" folder
>
> Inside the "css" folder create a new file called "tailwind.css" and paste the below

```CSS
/* resources/css/tailwind.css */

@import url('https://fonts.googleapis.com/css2?family=PT+Sans:ital,wght@0,400;0,700;1,400;1,700&display=swap');

@tailwind base;
@tailwind components;
@tailwind utilities;
```

> Add the following to the "app.css" file in the "css" folder to customize the scrollbar globally

```CSS
/* resources/css/app.css */

::-webkit-scrollbar {
    width: 8px;
    height: 8px;
}

::-webkit-scrollbar-track {
    border-radius: 100vh;
    /* background: rgb(241, 240, 240); */
    background: transparent;
}

::-webkit-scrollbar-thumb {
background: rgb(197, 196, 196);
    border-radius: 100vh;
}

::-webkit-scrollbar-thumb:hover {
    background: rgb(152, 152, 152);
}
```

> Navigate to the "js" folder inside "resources"
>
> Import the CSS files into the "app.js" file

```Javascript
// resources/js/app.js

... (other imports)
import router from './router.js'
import '../css/tailwind.css'
import '../css/app.css'

createApp(App)
...
.use(router)
.mount("#app")
```

## Laravel 12 Update
Install APIs for the laravel application
`php artisan install:api`

## Start Laravel Server

**Migrate Database**

> Run the following command to migrate the tables to the database

`php artisan migrate`

**Start the Laravel Server**

`php artisan serve`
