layout: page
title: "Astrocentro Webapp"
permalink: /astrocentro-webapp

# Index
- [How To](#how-to)
  - [How to create a Web Component in Astrocentro](#how-to-create-a-web-component-in-astrocentro)
- [Code Style Guide](#code-style-guide)
  - [Conditional Rendering](#conditional-rendering)

# How To

## How to create a Web Component in Astrocentro

### Introduction

This documentation covers the process of creating a native Web Component with the tools provided by the [canoadigital/astrocentro-webapp](https://github.com/Canoa-Digital/astrocentro-webapp) repository.

First of all, you need to setup your local environment by following the instructions at the [README.md](https://github.com/Canoa-Digital/astrocentro-webapp#readme).

#### Folder structure

After having your environment up and running you should see a folder structure similarly to this:

```
├── config
├── public
├── src
│   ├── assets
│   ├── modules
│   │   ├── commons
│   │   │   ├── components
│   │   │   ├── helpers
│   │   │   └── ...
│   │   ├── customers
│   │   │   ├── components
│   │   │   ├── services
│   │   │   └── ...
│   │   └── sellers
│   │   │   ├── components
│   │   │   ├── services
│   │   │   └── ...
│   ├── router
│   ├── views
│   ├── App.vue
│   └── main.js
├── index.html
└── ...
```

An application’s structure should tell you what it does and provide information about its domain. Opening a folder full of services doesn’t provide any context about the logical separation in your service. A long list of components tells nothing about the relationships between them.
Unlike other front-end projects that group code by technical responsibilities - i.e grouping all components in a components folder, grouping all services in a services folder - this project has a different approach that gives an idea what the component is doing and also gives confidence that everything  related to customers, sellers, etc.. it’s inside the it’s corresponding module. No need to dig through the codebase to make sure that you haven’t missed anything. This approach supports changes and growth without adding too much complexity.

#### Creating a Web Component

To create a Web Component you’ll need to follow these steps:

1. Create folder
1. Create component file
1. Add markup, styles and behavior.
1. Export and register the component
1. Testing the component

##### 1. Create folder

To exemplify, let’s create a component that displays a list of customers. It’s clear that our component belongs to the customer domain, so inside ```src/modules/customer``` , we’ll create a ```CustomerList``` folder.
The folder structure will look like this:

```
├── src
│   ├── modules
│   │   ├── commons
│   │   │   └── ...
│   │   ├── customers
│   │   │   ├── components
│   │   │   │   ├── CustomerList
│   │   │   │   └── ...
│   │   │   └── ...
│   │   └── ...
│   └── ...
└── ...
```

##### 2. Create component file

The naming convention for a component filename it’s Pascal Case, so inside ```CustomerList``` folder create a ```CustomerList.ce.vue```.
The ```.ce``` means that we’re creating a custom element and it enables the injection of all the component styles into the Shadow DOM instead of the page’s head. Read more at: [Vue and Web Components | Vue.js](https://vuejs.org/guide/extras/web-components.html#building-custom-elements-with-vue)
The ```.vue``` means that we’re creating a SFC. Read more at: [Single-File Components | Vue.js](https://vuejs.org/guide/scaling-up/sfc.html)

###### 3. Add markup, styles and behavior

Let’s add some markup to our component:

```
<script setup>
import { reactive, watch, onMounted } from "vue";

import { VRoot } from "@astrocentro-webapp/ui/modules/commons/components/VRoot";

const props = defineProps({
  customers: {
    type: [Array, String],
    default() {
      return [];
    },
  },
});

let state = reactive({
  customers: [],
});

onMounted(() => {
  state.customers =
    typeof props.customers === "string"
      ? JSON.parse(props.customers)
      : props.customers;
});

watch(
  () => props.customers,
  (customers) => {
    state.customers = customers;
  }
);
</script>

<template>
  <VRoot>
    <h2>Customer List</h2>
    <ul>
      <li v-for="customer of state.customers" :key="customer.name">
        {{ customer.name }}
      </li>
    </ul>
  </VRoot>
</template>

<style lang="scss">
@import "@/assets/stylesheets/app.scss";

.root {
  font-family: $font-family;
  font-size: $size-7;
}
</style>
```

###### Lines that it’s worth mentioning

***Line 6***: Declaring types in Vue it’s pretty easy but to receive an ```Array``` or ```Object``` as props via DOM attribute we must add the type ```String``` too. With this our component can receive a valid JSON string with ```Objects``` or ```Arrays```.
***Line 17-22***: Our component can receive a JSON string as props, we can’t forget to parse those props into JavaScript objects.
***Line 24-30***: If the environment that we’re embedding our Web Component is restricted, we can track down prop changes and update our state, so the component will re-render with new data.
This is important when the only way to pass props to our component is via DOM properties.
***Line 44***: We must remember to import our application styles, so our component will have all the necessary styles to display our elements correctly.
***Line 46-49***: We must not forget to specify the ```font-family``` that our component will display. Defining the ```font-size``` is also important so tags like ```<div>```, ```<span>``` will inherit the correct size.

##### Using a web component with custom child component styles

Remember to use the VRoot component and add the comment line below in the child components that has custom styles.

```
<script setup>
import { VAvatar } from "@astrocentro-webapp/ui/modules/commons/components/VAvatar";
...
</script>

<template>
  <div class="seller-avatar">
    ...
  </div>
</template>

<style lang="scss" scoped>
/* VueCustomElementChildren */
@import "@astrocentro-webapp/ui/assets/stylesheets/variables.scss";

.seller-avatar {
  ...
}
</style>
```


###### 4. Export and register the component

To export and register our component, we’ll need to create an ```index.js``` file inside ```src/modules/customer/CustomerList``` and add the following code:

```
import { defineCustomElement } from "vue";

import CustomerList from "./CustomerList.ce.vue";

const customElement = defineCustomElement(CustomerList);
window.customElements.define("customer-list", customElement);

export { CustomerList };
```

In short, we’re binding a component to a tag (registering) and exporting it.
To complete the registering of a component, you’ll need to import our newly created component at ```config/web-components.js``` , so our component will be included in the main bundle.
```web-components.js``` should look like this:

```
import { SellerAvailabilitiesList } from "@/modules/sellers/components/SellerAvailabilitiesList";
import { CustomerList } from "@/modules/customers/components/CustomerList";

```
After doing those steps our folder structure should look like this:

```
├── src
│   ├── modules
│   │   ├── commons
│   │   │   └── ...
│   │   ├── customers
│   │   │   ├── components
│   │   │   │   ├── CustomerList
│   │   │   │   │   ├── CustomerList.ce.vue
│   │   │   │   │   └── index.js
│   │   │   │   └── ...
│   │   │   └── ...
│   │   └── ...
│   └── ...
└── ...
```

###### 5. Testing the component

To test our component, we need to add our newly registered tag at ```index.html``` in the project root directory.

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" href="/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite App</title>
  </head>
  <body>
    <div id="app"></div>
    <customer-list
      customers='[{ "name": "Joey Ramone" }, { "name": "Dee Dee Ramone" }]'
    ></customer-list>
    <script type="module" src="/src/main.js"></script>
  </body>
</html>
```

You should see this at ```localhost:3000```:

![alt text](https://lh3.googleusercontent.com/drive-viewer/AJc5JmSB7lcKYgfNTrLSs1sa7cCtU4-8kSOFdDqLAPXDndtTr-vGA46xOFM6_ipYMRvMicosPywL97I=w2880-h1578)

# Code Style Guide

This is a reference on how to write code in a consistent manner for this project

## Conditional Rendering

The directives **v-if**, **v-else**, **v-else-if** and **v-show** are used to conditionally render elements.

The rule for the directive's expression is to set a **Computed Property** when the expression needs too much logic, this way the code is easy to maintain.

```
<script setup>
  import { computed } from "vue";

  ...

  const isChecked = computed(() => props.modelValue === props.value);
</script>

<template>
  ...
  <a v-if="isChecked">Anchor Here </a>
  ...
</template>
```