
[![AUR version](https://img.shields.io/npm/v/@alfsnd/vue-bootstrap-select.svg)](https://github.com/Sandalf/vue-bootstrap-select)
[![npm bundle size (minified)](https://img.shields.io/bundlephobia/min/react.svg)](https://github.com/Sandalf/vue-bootstrap-select)

# @alfsnd/vue-bootstrap-select
A vue version of [bootstrap select](https://github.com/snapappointments/bootstrap-select/)

# Install

```shell
npm install @alfsnd/vue-bootstrap-select --save
```

# Usage

```js
import VSelect from '@alfsnd/vue-bootstrap-select'

export default {
  name: 'app',
  components: {
    VSelect
  }
}
```

```html
<template>
  <div id="app">
    <v-select :items="[{value: 1, text: 'Item 1'}, {value: 2, text: 'Item 2'}]" @input="handleInput($event)"/>
  </div>
</template>
```
