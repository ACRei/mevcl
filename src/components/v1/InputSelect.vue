<template>
    <div class="input-box">
        <input class="input-control" type="text" v-model="inputLabel" @click="open" @input="search"
               :class="{'input-control-not-null' : inputValue !== ''}" :disabled="disabled"/>
        <span class="input-label">{{ placeholder }}</span>
        <span class="underline"></span>
        <div class="global-check" v-show="openSelectOption" @click="restoreValue"></div>
        <div class="select-option-box" ref="selectBox">
            <div class="select-option-list">
                <template v-for="item in data">
                    <div v-if="!item.hidden && item.label.indexOf(inputLabel) !== -1" :key="item.value" class="select-option" @click="clickOption(item)">
                        <span :class="{'select-option-on': item.value === inputValue}" v-html="item.label.replace(inputLabel, `<span style='color: red;'>${inputLabel}</span>`)"></span>
                    </div>
                </template>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "InputSelect",
    model: {
        prop: 'value',
        event: 'change'
    },
    props: {
        value: [String, Number],
        placeholder: String,
        data: Array,
        disabled: {
            type: Boolean,
            default: false
        }
    },
    data() {
        return {
            inputValue: this.data.find(d => d.value === this.value),
            inputLabel: '',
            openSelectOption: false,
        }
    },
    created() {
        this.getValue()
    },
    methods: {
        open() {
            this.inputLabel = ''
            this.countLen()
            this.openSelectOption = true
        },
        countLen() {
            let cnt = 0
            for (let i = 0; i < this.data.length; ++i) {
                if (this.data[i].hidden) {
                    continue;
                }
                cnt++;
                if (cnt >= 5) {
                    break
                }
            }
            this.$refs.selectBox.style.height = `${35 * cnt}px`
        },
        close() {
            this.$refs.selectBox.style.height = '0'
            this.openSelectOption = false
        },
        getValue() {
            this.inputValue = this.data.find(d => d.value === this.value)
            this.inputLabel = this.inputValue === undefined ? '' : this.inputValue.label
            this.inputValue = this.inputValue === undefined ? '' : this.inputValue.value
        },
        restoreValue() {
            this.getValue()
            this.close()
        },
        clickOption(option) {
            this.inputValue = option.value
            this.inputLabel = option.label
            this.$emit('change', option.value)
            this.close()
        },
        search() {
            this.$emit('search', this.inputLabel)
            this.countLen()
        }
    },
    watch: {
        value(v) {
            if (v === this.inputValue) return
            this.getValue()
        },
        data() {
            if (this.openSelectOption) {
                this.countLen()
            }
        }
    }
}
</script>

<style scoped>
.input-box {
    position: relative;
    width: 300px;
    height: 40px;
    margin: 10px 5px;
}

.input-control {
    position: absolute;
    left: 0;
    width: calc(100% - 22px);
    top: 0;
    height: 100%;
    appearance: none;
    -moz-appearance: none;
    -webkit-appearance: none;
    border: 1px solid var(--transparent-color);
    border-bottom: 1px solid var(--border-color-level-3);
    background-color: var(--white-color);
    padding: 0 10px 0 10px;
    outline: 0;
    transition: 0.2s ease all;
    cursor: pointer;
}

.input-label {
    position: absolute;
    width: auto;
    left: 10px;
    top: 50%;
    color: var(--secondary-text-color);
    background-color: var(--white-color);
    transform: translateY(-50%);
    transition: 0.2s ease all;
    pointer-events: none;
    padding: 0 4px;
}

.underline {
    position: absolute;
    margin-top: 1px;
    bottom: -4px;
    height: 4px;
    left: 50%;
    right: 50%;
    background-color: var(--brand-color);
    margin-left: 5px;
    margin-right: 5px;
    transition: 0.2s ease all;
    border-radius: 2px;
}

.select-option-box {
    z-index: 2048;
    position: absolute;
    width: 100%;
    height: 0;
    top: 110%;
    padding: 0 0 0 0;
    max-height: 500%;
    background-color: white;
    border-left: 1px solid var(--border-color-level-3);
    border-right: 1px solid var(--border-color-level-3);
    box-shadow: var(--open-shadowbox);
    border-radius: 10px;
    overflow: auto;
    transition: 0.2s ease all;
}

.select-option-list {
    margin: 0;
    padding: 0;
}

.select-option {
    font-size: 14px;
    /*height: 34px;*/
    line-height: 34px;
    border-bottom: 1px solid var(--border-color-level-3);
    position: relative;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    box-sizing: border-box;
    text-align: center;
    cursor: pointer;
}

.select-option:hover {
    background-color: var(--border-color-level-3);
}

.select-option-on {
    color: var(--brand-color);
    font-weight: 700;
}

/*hover*/
.input-control:hover ~ .underline {
    left: 0;
    right: 0;
}

/*focus*/
.input-control:focus {
    border-radius: 5px;
    background: var(--white-color);
    border: 1px solid var(--brand-color);
    box-shadow: var(--focus-shadowbox);
}

.input-control:focus ~ .input-label {
    top: 0;
    transform: scale(0.8) translateY(-50%);;
    background: var(--white-color);
    color: var(--brand-color);
}

.input-control:focus ~ .underline {
    left: 0;
    right: 0;
}

/*valid*/
.input-control-not-null {
    border-radius: 5px;
    border: 1px solid var(--border-color-level-3);
    background: var(--white-color);
}

.input-control-not-null ~ .input-label {
    top: 0;
    transform: scale(0.8) translateY(-50%);;
    background: var(--white-color);
}

/*disabled*/
.input-control:disabled {
    border-radius: 5px;
}

.input-control:disabled {
    cursor: not-allowed;
    border: 1px solid var(--border-color-level-3);
    border-radius: 5px;
}

.input-control:disabled + .input-label {
    top: 0;
    transform: scale(0.8) translateY(-50%);;
    background: var(--white-color);
}

.input-control:disabled ~ .underline {
    background-color: var(--border-color-level-3);
}

</style>
