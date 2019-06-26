
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

The `options` property accepts arrays of strings.

```html
  <v-select :options="['Item 1', 'Item 2']" />
```
And arrays of objects

```html
<v-select :options="[{value: 1, text: 'Item 1'}, {value: 2, text: 'Item 2'}]" />
```

Or you can provide a function to fetch the items.
```html
<template>
<v-select :options="fetchData" />
</template>
<script>
    export default {
        data() {
            return {
                users: [ /* list of users*/ ],
            }  
        },
        methods: {
            fetchData(ctx) {
                return this.users.filter(user => user.nickname.indexOf(ctx.search) > -1);
            }
        }
    }
</script>
```

Even asynchronously.
```html
<template>
    <v-select :options="fetchData" />
</template>
<script>
    export default {
        methods: {
            fetchData(ctx) {
                return fetch(`/users?search=${ctx.search}`);
            }
        }
    }
</script>
```

To customize the look of the dropdown, you can use the `label` slot.
```html
<v-select :options="...">
    <template slot="label" slot-scope="data">
        <img :src="data.option.picture">
        {{data.option.name}}
    </template>
</v-select>
```

### Supported Props
#### `value-prop`
To determine which item is selected, we use the `"value"` property on the selected object by default. 
You may override this behaviour by setting name in the `value-prop` property.

#### `text-prop`
When not using the `label` slot, you can override which property will show as label
by setting the `text-prop` property.

#### `label-not-found`
If there are no items to be displayed (or they are all filtered away), by default we show "No results matched",
You may override this text by setting the desired text in the `label-not-found` property.
> CAVEAT: Be aware the search term is always appended to this string.
> Use the `label-not-found` template to provide your own formatting. This slot accepts the 
> Scope `search` for the search term.

#### `label-title`
If there are items in the array, but none were selected. This text will appear.
Default: `"Nothing selected`

#### `label-search-placeholder`
This property overrides the placeholder set in the search `<input>`. This is `"Search"` by default.


#### `searchable`
Boolean - Determine wether the search box should be enabled.

#### `show-default-option`
Boolean - Determine wether `label-title` should be shown.
