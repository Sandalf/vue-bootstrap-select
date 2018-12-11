<template>
    <div
        v-on-clickaway="hideDropdown"
        class="z-select-container">
        <div
            @click="show = !show"
            class="z-select-toggle">
            <div>{{ selectedValue ? selectedValue[textProp] : defaultValue }}</div>
            <div class="arrow-down"></div>
        </div>
        <div
            v-show="show"
            class="z-dropdown-container">
            <ul>
                <li
                    v-if="search"
                    class="search-container">
                    <input
                        placeholder="Buscar"
                        class="form-control"
                        type="text"
                        v-model="searchValue"
                        autofocus>
                </li>
                <li
                    v-show="search && filteredProps.length === 0"
                    class="z-select-option">
                    No hay resultados para: "{{ searchValue }}"
                </li>
                <li
                    v-if="showDefaultOption"
                    class="z-select-option disabled default-option">
                    {{ defaultValue }}
                </li>
                <li
                    class="z-select-option"
                    v-for="(item, index) in filteredProps"
                    :key="`z-select-${index}`"
                    @click="handleSelect(item)">
                    {{ item[textProp]}}
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import {mixin as clickaway} from 'vue-clickaway';

export default {
    name: 'ZSelect',
    mixins: [clickaway],
    props: {
        items: {
            type: Array,
            default: () => []
        },
        textProp: {
            type: String,
            default: 'text'
        },
        valueProp: {
            type: String,
            default: 'value'
        },
        value: {
            type: [Object, String],
            default: null
        },
        defaultValue: {
            type: String,
            default: 'Seleccione'
        },
        search: {
            type: Boolean,
            default: false
        },
        showDefaultOption: {
            type: Boolean,
            default: false
        }
    },
    data() {
        return {
            show: false,
            selectedValue: this.value,
            searchValue: ''
        };
    },
    computed: {
        filteredProps() {
            return this.search && this.searchValue.length > 0 ? this.items.filter(item => item[this.textProp].toLowerCase().indexOf(this.searchValue) !== -1) : this.items;
        }
    },
    methods: {
        handleSelect(item) {
            this.selectedValue = item;
            this.hideDropdown();
            this.$emit('input', item[this.valueProp]);
        },
        hideDropdown() {
            this.show = false;
            this.searchValue = '';
        }
    }
};
</script>

<style lang="css" scoped>
.z-select-container {
    position: relative;
    width: 100%;
    height: 30px;
    background-color: #f8f9fa;
    border-color: #f8f9fa;
    border-radius: 0.25rem;
    line-height: 1.5;
    color: #212529;
    font-size: 12px;
    transition: background-color, border-color, box-shadow, .15s ease-in-out;
    cursor: pointer;
}

.z-select-container:hover {
    background-color: #e2e6ea;
    border-color: #dae0e5;
}

.z-select-toggle {
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

.z-dropdown-container {
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
    box-shadow: 0 2px 4px -1px rgba(0, 0, 0, 0.2), 0 4px 5px 0 rgba(0, 0, 0, 0.14), 0 1px 10px 0 rgba(0, 0, 0, 0.12);
    z-index: 1000;
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

.z-select-option {
    line-height: 25px;
    padding: 0.5rem 1.25rem;
    user-select: none;
}

.z-select-option:hover:not(.default-option) {
    text-decoration: none;
    background-color: #f8f9fa;
}

.z-select-option.disabled {
    color: #9A9B9B;
}

.search-container {
    padding: 0.5rem 1.25rem;
}

input {
    width: 100%;
}
</style>