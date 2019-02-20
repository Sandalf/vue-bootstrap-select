
[![AUR version](https://img.shields.io/npm/v/@alfsnd/vue-bootstrap-select.svg)](https://github.com/Sandalf/vue-bootstrap-select)
[![npm bundle size (minified)](https://img.shields.io/bundlephobia/min/react.svg)](https://github.com/Sandalf/vue-bootstrap-select)

# @alfsnd/vue-bootstrap-select
A vue version of [bootstrap select](https://github.com/snapappointments/bootstrap-select/)

# Demo

[![Edit Vue Bootstrap Select Demo](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/ovq821j566)

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
  },
  data() {
    return {
      selectedValue: null
    };
  }
}
```

```html
<template>
  <div id="app">
    <v-select :options="[{value: 1, text: 'Item 1'}, {value: 2, text: 'Item 2'}]" v-model="selectedValue" />
  </div>
</template>
```

### Passing options

The `options` prop accepts arrays of strings

```html
  <v-select :options="['Item 1', 'Item 2']" />
```
And arrays of objects

```html
<v-select :options="[{value: 1, text: 'Item 1'}, {value: 2, text: 'Item 2'}]" />
```
