<template>
    <div
            :class="{'disabled': disabled}"
            @keydown.down.prevent="typeAheadDown"
            @keydown.enter.prevent="typeAheadSelect"
            @keydown.up.prevent="typeAheadUp"
            @keyup.esc="onEscape"
            class="v-select"
            v-on-clickaway="hideDropdown">
        <button @click="toggle" class="v-select-toggle">
            <div>{{ title }}</div>
            <div class="arrow-down"></div>
        </button>
        <div class="v-dropdown-container" v-show="show">
            <div class="v-bs-searchbox" v-show="searchable">
                <input
                        :placeholder="labelSearchPlaceholder"
                        autofocus
                        class="form-control"
                        type="text"
                        v-model="searchValue"
                >
            </div>
            <ul>
                <li
                        class="v-dropdown-item"
                        v-show="searchable && renderedItems.length === 0"
                >
                    <slot name="label-not-found" :search="searchValue">
                        {{ labelNotFound }} "{{ searchValue }}"
                    </slot>
                </li>
                <li
                        class="v-dropdown-item disabled default-option"
                        v-if="showDefaultOption"
                >{{ labelTitle }}
                </li>
                <li
                        :class="{'selected' : isSelectedOption(option, index), 'disabled': option[disabledProp]}"
                        :key="`v-select-${index}`"
                        @click="onSelect(option, index)"
                        class="v-dropdown-item"
                        v-for="(option, index) in renderedItems"
                >
                    <slot name="label" :option="option">
                        {{ getOptionLabel(option) }}
                    </slot>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    import {mixin as clickaway} from "vue-clickaway";

    export default {
        name: "VSelect",
        mixins: [clickaway],
        props: {
            disabled: {
                type: Boolean,
                default: false
            },
            disabledProp: {
                type: String,
                default: "disabled"
            },
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
                type: [Array, Function],
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
            // The property that will be used to show the label of the element.
            textProp: {
                type: String,
                default: "text"
            },
            // Predetermined value
            value: {
                type: [Object, String, Number],
                default: null
            },
            // The property that will be used to check what object is currently selected.
            valueProp: {
                type: String,
                default: "value"
            }
        },
        data() {
            return {
                show: false,
                selectedValue: null,
                searchValue: "",
                typeAheadPointer: -1,
                renderedItems: []
            };
        },
        computed: {
            title() {
                return this.selectedValue
                    ? this.getOptionLabel(this.selectedValue)
                    : this.labelTitle;
            },
            reversedOptions() {
                return [...this.filteredOptions].reverse();
            },
            lastOptionIndex() {
                return this.filteredOptions.length - 1;
            }
        },
        watch: {
            value: {
                immediate: true,
                handler(newVal) {
                    const index = this.renderedItems.findIndex(op =>
                        this.isEqualOption(op, newVal)
                    );
                    this.onSelect(newVal, index);
                }
            }
        },
        methods: {
            filteredOptions: async () => {
                if (Array.isArray(this.options)) {
                    if (this.searchable && this.searchValue.length > 0) {
                        this.renderedItems = this.options.filter(item => {
                            if (typeof item === "object") {
                                return (
                                    item[this.textProp]
                                        .toLowerCase()
                                        .indexOf(this.searchValue.toLowerCase()) !== -1);
                            } else {
                                return (
                                    item.toLowerCase().indexOf(this.searchValue.toLowerCase()) !== -1
                                );
                            }
                        });
                    } else {
                        this.renderedItems = this.options;
                    }
                }
                // Otherwise, if we got a function, run that function.
                else if (this.options instanceof Function) {
                    let x = this.options({search: this.searchValue});
                    // If it is asynchronous, put the resolved value in the rendered items array.
                    if(x instanceof Promise) {
                        x.then(result => {this.renderedItems = result});
                    } else {
                        // Otherwise, just put it in straight.
                        this.renderedItems = x;
                    }
                }
            },

            onSelect(option, index) {
                if (option && !option[this.disabledProp]) {
                    this.selectedValue = option;
                    this.typeAheadPointer = index;
                    this.hideDropdown();
                    this.$emit("input", option);
                } else if (option === null) {
                    this.selectedValue = null;
                }
            },
            onEscape() {
                this.hideDropdown();
            },
            typeAheadUp() {
                if (!this.show) {
                    this.show = true;
                }
                if (this.typeAheadPointer > 0) {
                    const nextPointer = this.typeAheadPointer - 1;
                    const option = this.filteredOptions[nextPointer];
                    const isDisabled = option ? option[this.disabledProp] || false : false;
                    if (!isDisabled) {
                        this.typeAheadPointer--;
                    } else {
                        this.typeAheadPointer--;
                        this.typeAheadUp();
                    }
                } else {
                    const nextEnabledOption = this.reversedOptions.findIndex(
                        o => o[this.disabledProp] !== true
                    );
                    this.typeAheadPointer = this.lastOptionIndex - nextEnabledOption;
                }
            },
            typeAheadDown() {
                if (!this.show) {
                    this.show = true;
                }
                if (this.typeAheadPointer < this.lastOptionIndex) {
                    const nextPointer = this.typeAheadPointer + 1;
                    const option = this.filteredOptions[nextPointer];
                    const isDisabled = option ? option[this.disabledProp] || false : false;
                    if (!isDisabled) {
                        this.typeAheadPointer++;
                    } else {
                        this.typeAheadPointer++;
                        this.typeAheadDown();
                    }
                } else {
                    this.typeAheadPointer = this.filteredOptions.findIndex(
                        o => o[this.disabledProp] !== true
                    );
                }
            },
            typeAheadSelect() {
                if (this.filteredOptions[this.typeAheadPointer]) {
                    this.onSelect(
                        this.filteredOptions[this.typeAheadPointer],
                        this.typeAheadPointer
                    );
                }
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
                if (this.typeAheadPointer === -1 && this.selectedValue) {
                    return this.isEqualOption(option, this.selectedValue);
                }
                return this.typeAheadPointer === index;
            },
            isEqualOption(a, b) {
                if (a && b && typeof a === "object" && typeof b === "object") {
                    return (
                        a[this.textProp] === b[this.textProp] &&
                        a[this.valueProp] === b[this.valueProp]
                    );
                }
                return a === b;
            },
            toggle() {
                if (!this.disabled) {
                    this.show = !this.show;
                    if(this.show) {
                        this.filteredOptions();
                    }
                }
            }
        }
    };
</script>

<style lang="scss" scoped>

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
        padding: 0;
        margin: 2px 0 0 0;
    }

    .v-select {
        position: relative;
        width: 100%;
        height: 30px;
        cursor: pointer;

        &.disabled {
            cursor: not-allowed;

            .v-select-toggle {
                background-color: #f8f9fa;
                border-color: #f8f9fa;
                opacity: 0.65;
                cursor: not-allowed;

                &:focus {
                    outline: 0 !important;
                }
            }
        }
    }

    .v-select-toggle {
        display: flex;
        justify-content: space-between;
        user-select: none;
        padding: 0.375rem 0.75rem;
        color: #212529;
        background-color: #f8f9fa;
        width: 100%;
        text-align: right;
        white-space: nowrap;
        border: 1px solid #d3d9df;
        font-size: 12px;
        font-family: inherit, sans-serif;
        line-height: 1.5;
        border-radius: 0.25rem;
        transition: background-color, border-color, box-shadow, 0.15s ease-in-out;
        cursor: pointer;

        &:hover {
            background-color: #e2e6ea;
            border-color: #dae0e5;
        }
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
        padding: 0.5rem 0;
        margin: 0.125rem 0 0;
        color: #212529;
        text-align: left;
        list-style: none;
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

        &.disabled {
            cursor: not-allowed;

            &:hover {
                background-color: #fff;
            }
        }
    }

    .v-bs-searchbox {
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
