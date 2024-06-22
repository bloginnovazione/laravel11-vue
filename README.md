<h3>Laravel 11 and Vue.js: installation, configuration and examples</h3>

<b>Laravel 11 and Vue.js is a great combination for writing clean and efficient code.
In this tutorial we see together how to install, configure and develop in Laravel 11 using the power of Vue.js.
To test Vue.js we will write code to manage the “Blog Posts” Data entity.</b>

<h4>Table of Contents</h4>

    Vue.js installation
    Vue.js configuration
    Vue component creation
    JS and Tailwind recompilation
    Example
    Related Readings


<h4>Vue.js installation</h4>

We'll set up the Laravel 11 framework, install the Vue.js JavaScript framework, and get it working with Vite. Then we will load our first Vue component.

<pre><code>composer create-project --prefer-dist laravel/laravel crud-spa</code></pre>

Let's configure the details of the Database:

<pre><code>
DB_CONNECTION=mysql <br>
DB_HOST=127.0.0.1 <br>
DB_PORT=3306 <br>
DB_DATABASE=<DATABASE NAME><br>
DB_USERNAME=<DATABASE USERNAME><br>
DB_PASSWORD=<DATABASE PASSWORD><br>
</code></pre>

After installing Laravel, let's proceed to install Laravel Breeze.

You do NOT need Breeze to use Vue.js. It's just a personal preference: we won't use it for the authentication system and we won't use its Vue version, it's just for quick and easy visual design, so we will remove many of its parts.

<pre><code>composer require laravel/breeze --dev
php artisan breeze:install blade</code></pre>

For simplicity, let's just create one public page for Dashboard, we will then remove all links from the navigation where authentication is called. Both in desktop and mobile menu.

The file resources/views/layouts/navigation.blade.php becomes:


<pre class="wp-block-code"><code>&lt;nav x-data="{ open: false }" class="bg-white border-b border-gray-100"&gt;
    &lt;!-- Primary Navigation Menu --&gt;
    &lt;div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8"&gt;
        &lt;div class="flex justify-between h-16"&gt;
            &lt;div class="flex"&gt;
                &lt;!-- Logo --&gt;
                &lt;div class="shrink-0 flex items-center"&gt;
                    &lt;a href="{{ route('dashboard') }}"&gt;
                        &lt;x-application-logo class="block h-9 w-auto fill-current text-gray-800" /&gt;
                    &lt;/a&gt;
                &lt;/div&gt;

                &lt;!-- Navigation Links --&gt;
                &lt;div class="hidden space-x-8 sm:-my-px sm:ms-10 sm:flex"&gt;
                    &lt;x-nav-link :href="route('dashboard')" :active="request()-&gt;routeIs('dashboard')"&gt;
                        {{ __('Dashboard') }}
                    &lt;/x-nav-link&gt;
                &lt;/div&gt;
            &lt;/div&gt;

            &lt;!-- Hamburger --&gt;
            &lt;div class="-me-2 flex items-center sm:hidden"&gt;
                &lt;button @click="open = ! open" class="inline-flex items-center justify-center p-2 rounded-md text-gray-400 hover:text-gray-500 hover:bg-gray-100 focus:outline-none focus:bg-gray-100 focus:text-gray-500 transition duration-150 ease-in-out"&gt;
                    &lt;svg class="h-6 w-6" stroke="currentColor" fill="none" viewBox="0 0 24 24"&gt;
                        &lt;path :class="{'hidden': open, 'inline-flex': ! open }" class="inline-flex" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" /&gt;
                        &lt;path :class="{'hidden': ! open, 'inline-flex': open }" class="hidden" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /&gt;
                    &lt;/svg&gt;
                &lt;/button&gt;
            &lt;/div&gt;
        &lt;/div&gt;
    &lt;/div&gt;

    &lt;!-- Responsive Navigation Menu --&gt;
    &lt;div :class="{'block': open, 'hidden': ! open}" class="hidden sm:hidden"&gt;
        &lt;div class="pt-2 pb-3 space-y-1"&gt;
            &lt;x-responsive-nav-link :href="route('dashboard')" :active="request()-&gt;routeIs('dashboard')"&gt;
                {{ __('Dashboard') }}
            &lt;/x-responsive-nav-link&gt;
        &lt;/div&gt;
    &lt;/div&gt;
&lt;/nav&gt;</code></pre>


In the routes file web.php, we need only one route to the index page and it can only be one view. The CRUD for routes will use the vue-router.

The file routes/web.php becomes:


<?php

use App\Http\Controllers\ProfileController;
use Illuminate\Support\Facades\Route;

Route::view('/', 'dashboard')->name('dashboard'); 

    </code></pre>

Now we need to specify when Vue will be “mounted”. That is, the main element of the HTML structure, where Vue would live “inside it”. Usually, it is one of the main div elements in the main layout and to identify it we assign it id=”app”.

So let's open the file resources/views/layouts/app.blade.php and add the attribute in the first div after opening the body:

<pre><code>
.
.
.
        <!-- Fonts -->
        <link rel="preconnect" href="https://fonts.bunny.net">
        <link href="https://fonts.bunny.net/css?family=figtree:400,500,600&display=swap" rel="stylesheet" />

        <!-- Scripts -->
        @vite(['resources/css/app.css', 'resources/js/app.js'])
    </head>
    <body class="font-sans antialiased">
        <div class="min-h-screen bg-gray-100" id="app">
            @include('layouts.navigation')

            <!-- Page Heading -->
            @isset($header)
                <header class="bg-white shadow">
                    <div class="max-w-7xl mx-auto py-6 px-4 sm:px-6 lg:px-8">
                        {{ $header }}
                    </div>
                </header>
            @endisset
.
.
.



<h4>Vue.js configuration</h4>

By defaultdefinita, Breeze adds Alpine.js, which we won't use though. So we modify the package.json, present in the Laravel root, and remove Alpine.js.

In the devDependencies of package.json, we remove the line

<pre><code>
{
    "private": true,
    "type": "module",
    "scripts": {
        "dev": "vite",
        "build": "vite build"
    },
    "devDependencies": {
        "@tailwindcss/forms": "^0.5.2",
        "autoprefixer": "^10.4.2",
        "axios": "^1.6.4",
        "laravel-vite-plugin": "^1.0",
        "postcss": "^8.4.31",
        "tailwindcss": "^3.1.0",
        "vite": "^5.0"
    }
}
</code></pre>

Now we can install Vue and Vue loader and configure Vue.

<pre><code>
npm install vue vue-loader
    </code></pre>

Next, we need to install the Vue Vite plugin.

<pre><code>
npm install --save-dev @vitejs/plugin-vue
    </code></pre>

And let's edit the file vite.config.js present in laravel root, as follows:

<pre><code>
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import vue from '@vitejs/plugin-vue';

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/css/app.css',
                'resources/js/app.js',
            ],
            refresh: true,
        }),
        vue({
            template: {
                transformAssetUrls: {
                    base: null,
                    includeAbsolute: false,
                },
            },
        }),
    ],
    resolve: {
        alias: {
            vue: 'vue/dist/vue.esm-bundler.js',
        },
    },
});
    </code></pre>

<h4>Vue component creation</h4>

Let's add a Vue component on the Dashboard page. In the future it will show the list of posts, so we will call its HTML tag .

Let's edit the file resources/views/dashboard.blade.php as follows:

<pre><code>
<x-app-layout>
    <x-slot name="header">
        <h2 class="font-semibold text-xl text-gray-800 leading-tight">
            {{ __('Dashboard') }}
        </h2>
    </x-slot>
 
    <div class="py-12">
        <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
            <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                <div class="p-6 text-gray-900">
                    {{ __("You're logged in!") }}  
                    <posts-index></posts-index> 
                </div>
            </div>
        </div>
    </div>
</x-app-layout>
</code></pre>

Substituting {{ __("You're logged in!") }} with <posts-index></posts-index>.

Now let's proceed to create the Vue component resources/js/components/Posts/Index.vue. For now we will only insert dummy static text inside it.
Innovation newsletter
Don't miss the most important news on innovation. Sign up to receive them by email.

Let's edit the file resources/js/components/Posts/Index.vue:

<pre><code>
<template>
    Table coming soon.
</template>
</code></pre>
    
Each Vue component consists of two parts:

    <pre><code>
    <script>
    <template>
    </code></pre>

In this case, we're not doing any JS operations yet, so we won't have the script part, just the template.

Now, back to the element id="app", let's proceed to initialize a Vue app, add the component from above, and mount it to that app element. This all needs to be done in the main file resources/js/app.js. By defaultdefinited with Breeze, the app.js file contains code using Alpine.js, and by implementation choice, we remove it.

The file resources/js/app.js becomes:

<pre><code>
import './bootstrap';

import { createApp } from 'vue'
import PostsIndex from './components/Posts/Index.vue'

createApp({})
    .component('PostsIndex', PostsIndex)
    .mount('#app') 
</code></pre>

In the app.js file defiLet's start the Vue application with createApp(), importing it beforehand We attach a component to the Vue application, importing it beforehand and giving it the name PostIndex.

Let's mount the Vue application on the item #app from the main layout.

This is how we are naming the component PostIndex so you know it's a component for Posts and it's a file Index.vue.

To call components in Vue files, there are two ways:

    The one we used is the kebab-used.
    And secondly, using PascalCase.

Vue.js supports both cases. So for component naming we use PascalCase.

Now let's compile everything using npm run dev o npm run build, when finished, open the page in the browser and we should see the text Table coming Soon, as follows:

Dashboard with vue component


<h4>JS and Tailwind recompilation</h4>

When working with any front-end file, we need to recompile after each change to see the changes in the browser. We must therefore execute npm run build every time after a small change, or even better run npm run dev and keep it running in the background.

When we execute npm run dev with Vite, after each recompilation the browser will be automatically updated. After running npm run dev in your console you will see a result similar to the following:
npm run dev
npm rundev

After npm run build on the video we will have:

npm run build

npm run build

<h4>Example</h4>

Let's now try a simple modification in the Vue file: let's add a simple static table (for now) to the component PostIndex Vue.

Then the file resources/js/components/Posts/Index.vue becomes:

<pre><code>
<template>
    <div class="overflow-hidden overflow-x-auto p-6 bg-white border-gray-200">
        <div class="min-w-full align-middle">
            <table class="min-w-full divide-y divide-gray-200 border">
                <thead>
                <tr>
                    <th class="px-6 py-3 bg-gray-50 text-left">
                        <span class="text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider">ID</span>
                    </th>
                    <th class="px-6 py-3 bg-gray-50 text-left">
                        <span class="text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider">Title</span>
                    </th>
                    <th class="px-6 py-3 bg-gray-50 text-left">
                        <span class="text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider">Content</span>
                    </th>
                    <th class="px-6 py-3 bg-gray-50 text-left">
                        <span class="text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider">Created at</span>
                    </th>
                </tr>
                </thead>
                <tbody class="bg-white divide-y divide-gray-200 divide-solid">
                <tr>
                    <td class="px-6 py-4 whitespace-no-wrap text-sm leading-5 text-gray-900">1</td>
                    <td class="px-6 py-4 whitespace-no-wrap text-sm leading-5 text-gray-900">A</td>
                    <td class="px-6 py-4 whitespace-no-wrap text-sm leading-5 text-gray-900">Poe switched his focus to prose, and spent the next several years working for literary journals and periodicals, becoming known for his own style of literary criticism. His work forced him to move between several cities, including Baltimore, Philadelphia, and New York City. In 1836, when he was 27, he married his 13-year-old cousin, Virginia Clemm. She died of tuberculosis in 1847. In January 1845, he published his poem "The Raven" to instant success. He planned for years to produce his own journal The Penn, later renamed The Stylus. But before it began publishing, Poe died in Baltimore in 1849, aged 40, under mysterious circumstances. The cause of his death remains unknown, and has been variously attributed to many causes including disease, alcoholism, substance abuse, and suicide.</td>
                    <td class="px-6 py-4 whitespace-no-wrap text-sm leading-5 text-gray-900">2024-06-01 13:43:47</td>
                </tr>
                <tr>
                    <td class="px-6 py-4 whitespace-no-wrap text-sm leading-5 text-gray-900">2</td>
                    <td class="px-6 py-4 whitespace-no-wrap text-sm leading-5 text-gray-900">B</td>
                    <td class="px-6 py-4 whitespace-no-wrap text-sm leading-5 text-gray-900">Poe switched his focus to prose, and spent the next several years working for literary journals and periodicals, becoming known for his own style of literary criticism. His work forced him to move between several cities, including Baltimore, Philadelphia, and New York City. In 1836, when he was 27, he married his 13-year-old cousin, Virginia Clemm. She died of tuberculosis in 1847. In January 1845, he published his poem "The Raven" to instant success. He planned for years to produce his own journal The Penn, later renamed The Stylus. But before it began publishing, Poe died in Baltimore in 1849, aged 40, under mysterious circumstances. The cause of his death remains unknown, and has been variously attributed to many causes including disease, alcoholism, substance abuse, and suicide.</td>
                    <td class="px-6 py-4 whitespace-no-wrap text-sm leading-5 text-gray-900">2024-06-02 14:43:47</td>
                </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>
</code></pre>

vue component table
Vue component

But now we have a problem with Tailwind: it doesn't pick up the new CSS styles we added. We need to add the location of the Vue components to the Tailwind configuration file, so that it automatically collects and compiles the classes used in Vue.

Let's edit the file tailwind.config.js:

<pre><code>
import defaultTheme from 'tailwindcss/defaultTheme';
import forms from '@tailwindcss/forms';

/** @type {import('tailwindcss').Config} */
export default {
    content: [
        './vendor/laravel/framework/src/Illuminate/Pagination/resources/views/*.blade.php',
        './storage/framework/views/*.php',
        './resources/views/**/*.blade.php',
        './resources/js/components/**/*.vue',
    ],

    theme: {
        extend: {
            fontFamily: {
                sans: ['Figtree', ...defaultTheme.fontFamily.sans],
            },
        },
    },

    plugins: [forms],
};
</code></pre>

vue component tailwind
Vue component with Tailwind

<pre><code>
<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

<p align="center">
<a href="https://github.com/laravel/framework/actions"><img src="https://github.com/laravel/framework/workflows/tests/badge.svg" alt="Build Status"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://img.shields.io/packagist/dt/laravel/framework" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://img.shields.io/packagist/v/laravel/framework" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://img.shields.io/packagist/l/laravel/framework" alt="License"></a>
</p>
</code></pre>

## About Laravel

Laravel is a web application framework with expressive, elegant syntax. We believe development must be an enjoyable and creative experience to be truly fulfilling. Laravel takes the pain out of development by easing common tasks used in many web projects, such as:

- [Simple, fast routing engine](https://laravel.com/docs/routing).
- [Powerful dependency injection container](https://laravel.com/docs/container).
- Multiple back-ends for [session](https://laravel.com/docs/session) and [cache](https://laravel.com/docs/cache) storage.
- Expressive, intuitive [database ORM](https://laravel.com/docs/eloquent).
- Database agnostic [schema migrations](https://laravel.com/docs/migrations).
- [Robust background job processing](https://laravel.com/docs/queues).
- [Real-time event broadcasting](https://laravel.com/docs/broadcasting).

Laravel is accessible, powerful, and provides tools required for large, robust applications.

## Learning Laravel

Laravel has the most extensive and thorough [documentation](https://laravel.com/docs) and video tutorial library of all modern web application frameworks, making it a breeze to get started with the framework.

You may also try the [Laravel Bootcamp](https://bootcamp.laravel.com), where you will be guided through building a modern Laravel application from scratch.

If you don't feel like reading, [Laracasts](https://laracasts.com) can help. Laracasts contains thousands of video tutorials on a range of topics including Laravel, modern PHP, unit testing, and JavaScript. Boost your skills by digging into our comprehensive video library.

## Laravel Sponsors

We would like to extend our thanks to the following sponsors for funding Laravel development. If you are interested in becoming a sponsor, please visit the [Laravel Partners program](https://partners.laravel.com).

### Premium Partners

- **[Vehikl](https://vehikl.com/)**
- **[Tighten Co.](https://tighten.co)**
- **[WebReinvent](https://webreinvent.com/)**
- **[Kirschbaum Development Group](https://kirschbaumdevelopment.com)**
- **[64 Robots](https://64robots.com)**
- **[Curotec](https://www.curotec.com/services/technologies/laravel/)**
- **[Cyber-Duck](https://cyber-duck.co.uk)**
- **[DevSquad](https://devsquad.com/hire-laravel-developers)**
- **[Jump24](https://jump24.co.uk)**
- **[Redberry](https://redberry.international/laravel/)**
- **[Active Logic](https://activelogic.com)**
- **[byte5](https://byte5.de)**
- **[OP.GG](https://op.gg)**

## Contributing

Thank you for considering contributing to the Laravel framework! The contribution guide can be found in the [Laravel documentation](https://laravel.com/docs/contributions).

## Code of Conduct

In order to ensure that the Laravel community is welcoming to all, please review and abide by the [Code of Conduct](https://laravel.com/docs/contributions#code-of-conduct).

## Security Vulnerabilities

If you discover a security vulnerability within Laravel, please send an e-mail to Taylor Otwell via [taylor@laravel.com](mailto:taylor@laravel.com). All security vulnerabilities will be promptly addressed.

## License

The Laravel framework is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).
