<template>
  <div v-on-clickaway="hideDropdown" class="v-select">
    <div @click="show = !show" class="v-select-toggle">
      <div>{{ selectedValue ? getOptionLabel(selectedValue) : labelTitle }}</div>
      <div class="arrow-down"></div>
    </div>
    <div v-show="show" class="v-dropdown-container">
      <div v-show="searchable" class="bs-searchbox">
        <input
          :placeholder="labelSearchPlaceholder"
          class="form-control"
          type="text"
          v-model="searchValue"
          autofocus
        >
      </div>
      <ul>
        <li
          v-show="searchable && filteredProps.length === 0"
          class="v-dropdown-item"
        >{{ labelNotFound }} "{{ searchValue }}"</li>
        <li
          v-if="showDefaultOption"
          class="v-dropdown-item disabled default-option"
        >{{ labelTitle }}</li>
        <li
          v-for="(option, index) in filteredProps"
          :key="`v-select-${index}`"
          class="v-dropdown-item"
          :class="{'selected' : isSelectedOption(option, index)}"
          @click="onSelect(option)"
        >{{ getOptionLabel(option) }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import { mixin as clickaway } from "vue-clickaway";

export default {
  name: "VSelect",
  mixins: [clickaway],
  props: {
    labelTitle: {
      type: String,
      default: "Nothing selected"
    },
    labelNotFound: {
      type: String,
      default: "No results matched"
    },
    labelSearchPlaceholder: {
      type: String,
      default: "Search"
    },
    options: {
      type: Array,
      default: () => []
    },
    searchable: {
      type: Boolean,
      default: false
    },
    showDefaultOption: {
      type: Boolean,
      default: false
    },
    textProp: {
      type: String,
      default: "text"
    },
    value: {
      type: [Object, String, Number],
      default: null
    },
    valueProp: {
      type: String,
      default: "value"
    }
  },
  data() {
    return {
      show: false,
      selectedValue: this.value,
      searchValue: "",
      typeAheadPointer: -1
    };
  },
  computed: {
    title() {
      return this.selectedValue
        ? this.getOptionLabel(this.selectedValue)
        : this.labelTitle;
    },
    filteredProps() {
      if (this.searchable && this.searchValue.length > 0) {
        return this.options.filter(item => {
          if (typeof item === "object") {
            return (
              item[this.textProp]
                .toLowerCase()
                .indexOf(this.searchValue.toLowerCase()) !== -1
            );
          } else {
            return (
              item.toLowerCase().indexOf(this.searchValue.toLowerCase()) !== -1
            );
          }
        });
      }
      return this.options;
    }
  },
  methods: {
    onSelect(option) {
      this.selectedValue = option;
      this.hideDropdown();
      this.$emit("input", option);
    },
    hideDropdown() {
      this.show = false;
      this.searchValue = "";
    },
    getOptionLabel(option) {
      if (typeof option === "object") {
        return option[this.textProp];
      }
      return option;
    },
    isSelectedOption(option, index) {
      if (this.typeAheadPointer === -1) {
        return this.selectedValue && option === this.selectedValue;
      }
      return this.typeAheadPointer === index;
    }
  }
};
</script>

<style lang="scss" scoped>
* {
  box-sizing: border-box;
}

input {
  width: 100%;
}

ul {
  font-size: 12px;
  color: #424242;
  text-align: left;
  list-style: none;
  background-color: #fff;
  background-clip: padding-box;
  padding: 0px;
  margin: 0px;
}

.v-select {
  position: relative;
  width: 100%;
  height: 30px;
  background-color: #f8f9fa;
  border-color: #f8f9fa;
  border-radius: 0.25rem;
  line-height: 1.5;
  color: #212529;
  font-size: 12px;
  transition: background-color, border-color, box-shadow, 0.15s ease-in-out;
  font-family: inherit, sans-serif;
  cursor: pointer;

  &:hover {
    background-color: #e2e6ea;
    border-color: #dae0e5;
  }
}

.v-select-toggle {
  display: flex;
  justify-content: space-between;
  user-select: none;
  padding: 0.375rem 0.75rem;
}

.arrow-down {
  display: inline-block;
  width: 0;
  height: 0;
  margin-left: 0.255em;
  margin-top: 7px;
  vertical-align: 0.255em;
  content: "";
  border-top: 0.3em solid;
  border-right: 0.3em solid transparent;
  border-bottom: 0;
  border-left: 0.3em solid transparent;
}

.v-dropdown-container {
  position: absolute;
  width: 100%;
  background: red;
  padding: 0.5rem 0;
  margin: 0.125rem 0 0;
  color: #212529;
  text-align: left;
  list-style: none;
  background-color: #fff;
  background-clip: padding-box;
  border-radius: 0.25rem;
  border: 1px solid rgba(0, 0, 0, 0.15);
  z-index: 1000;
}

.v-dropdown-item {
  text-decoration: none;
  line-height: 25px;
  padding: 0.5rem 1.25rem;
  user-select: none;

  &:hover:not(.default-option) {
    background-color: #f8f9fa;
  }

  &.disabled {
    color: #9a9b9b;
  }

  &.selected {
    background-color: #007bff;
    color: #fff;

    &:hover {
      background-color: #007bff;
      color: #fff;
    }
  }
}

.bs-searchbox {
  padding: 4px 8px;

  .form-control {
    display: block;
    width: 100%;
    padding: 0.375rem 0.75rem;
    font-size: 1rem;
    line-height: 1.5;
    color: #495057;
    background-color: #fff;
    background-clip: padding-box;
    border: 1px solid #ced4da;
    border-radius: 0.25rem;
    transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
  }
}
</style>
