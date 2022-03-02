<template>
  <div>
    <div class="input-box">
      <div class="input-box__chip-list">
        <div
          class="input-box__chip"
          v-for="option in selectedOptions"
          :key="option.id"
          @mousedown.prevent="removeOptionFromSelectedArray(option)"
        >
          <div>
            {{ option.name }}
          </div>
          <div class="input-box__icon">
            <!-- remove cross icon -->
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">
              <!--! Font Awesome Pro 6.0.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. -->
              <path
                d="M175 175C184.4 165.7 199.6 165.7 208.1 175L255.1 222.1L303 175C312.4 165.7 327.6 165.7 336.1 175C346.3 184.4 346.3 199.6 336.1 208.1L289.9 255.1L336.1 303C346.3 312.4 346.3 327.6 336.1 336.1C327.6 346.3 312.4 346.3 303 336.1L255.1 289.9L208.1 336.1C199.6 346.3 184.4 346.3 175 336.1C165.7 327.6 165.7 312.4 175 303L222.1 255.1L175 208.1C165.7 199.6 165.7 184.4 175 175V175zM512 256C512 397.4 397.4 512 256 512C114.6 512 0 397.4 0 256C0 114.6 114.6 0 256 0C397.4 0 512 114.6 512 256zM256 48C141.1 48 48 141.1 48 256C48 370.9 141.1 464 256 464C370.9 464 464 370.9 464 256C464 141.1 370.9 48 256 48z"
              />
            </svg>
          </div>
        </div>
        <input
          class="input-box__input"
          ref="filterInput"
          type="text"
          v-model.trim="filterInputText"
          @focus="openOptions"
          @blur="closeOptions"
          @keydown="openOptions"
          :placeholder="placeholder"
        />
      </div>
      <div
        class="input-box__icon"
        :class="{ 'input-box__icon--opened': showOptions }"
        @mousedown.prevent="handleArrowClick"
      >
        <!-- arrow icon -->
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 384 512">
          <path
            d="M192 384c-8.188 0-16.38-3.125-22.62-9.375l-160-160c-12.5-12.5-12.5-32.75 0-45.25s32.75-12.5 45.25 0L192 306.8l137.4-137.4c12.5-12.5 32.75-12.5 45.25 0s12.5 32.75 0 45.25l-160 160C208.4 380.9 200.2 384 192 384z"
          />
        </svg>
      </div>
    </div>
    <transition name="fade">
      <div v-if="showOptions" class="options-box">
        <div class="options-box__container" v-if="filteredOptions.length > 0">
          <div
            class="options-box__option"
            :class="{ 'options-box__option--selected': option.selected }"
            v-for="option in filteredOptions"
            :key="option.id"
            @mousedown.prevent="toogleSelectedOption(option)"
          >
            <div>{{ option.name }}</div>
            <div
              class="options-box__icon"
              :class="{ 'options-box__icon--hide': !option.selected }"
            >
              <!-- check icon -->
              <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512">
                <!--! Font Awesome Pro 6.0.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. -->
                <path
                  d="M438.6 105.4C451.1 117.9 451.1 138.1 438.6 150.6L182.6 406.6C170.1 419.1 149.9 419.1 137.4 406.6L9.372 278.6C-3.124 266.1-3.124 245.9 9.372 233.4C21.87 220.9 42.13 220.9 54.63 233.4L159.1 338.7L393.4 105.4C405.9 92.88 426.1 92.88 438.6 105.4H438.6z"
                />
              </svg>
            </div>
          </div>
        </div>
        <div class="options-box__option--empty-state" v-else>
          {{ noResults }}
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  props: {
    options: { type: Array, required: true },
    placeholder: { type: String, required: false, default: 'Type here to filter' },
    noResults: { type: String, required: false, default: 'No results' }
  },
  name: 'MultiSelect',
  data () {
    return { filterInputText: '', selectedOptions: [], showOptions: false }
  },
  watch: {
    selectedOptions () {
      this.$emit('getSelection', this.cleanSelectedArrayToReturn())
    }
  },
  computed: {
    selectOptions () {
      return this.options.map(option => {
        return { ...option }
      }) // Deep copy options array
    },
    filteredOptions () {
      if (this.filterInputText === '') return this.selectOptions
      return this.selectOptions.filter(
        option =>
          option.name.toLowerCase().includes(this.filterInputText.toLowerCase()) // Filtering by name ignoring case
      )
    }
  },
  methods: {
    getIndexOptionInSelectedArray (optionId) {
      return this.selectedOptions.findIndex(
        selectedOption => selectedOption.id === optionId
      )
    },
    removeOptionFromSelectedArray (option) {
      const indexOptionInSelectedArray = this.getIndexOptionInSelectedArray(
        option.id
      )
      this.selectedOptions.splice(indexOptionInSelectedArray, 1)
      option.selected = false
    },
    addOptionToSelectedArray (option) {
      this.selectedOptions.push(option)
      option.selected = true
    },
    toogleSelectedOption (option) {
      if (option.selected) {
        this.removeOptionFromSelectedArray(option)
      } else {
        this.addOptionToSelectedArray(option)
      }
    },
    openOptions () {
      this.showOptions = true
    },
    closeOptions () {
      this.showOptions = false
    },
    focusInput () {
      this.$refs.filterInput.focus()
    },
    handleArrowClick () {
      if (this.showOptions) {
        this.closeOptions()
      } else {
        this.openOptions()
        this.focusInput()
      }
    },
    clearFilter () {
      this.filterInputText = ''
    },
    cleanSelectedArrayToReturn () {
      return [
        ...this.selectedOptions.map(({ selected, ...keepAttrs }) => keepAttrs)
      ] // remove 'selected' prop from final array
    }
  }
}
</script>

<style>
.input-box {
  display: flex;
  align-items: center;
  column-gap: 8px;
  width: 400px;
  border: solid var(--border-color) 1px;
  border-radius: 4px;
  padding: 8px;
}
.input-box__chip-list {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  width: 100%;
  gap: 8px;
}
.input-box__chip {
  border: 1px solid var(--border-color);
  padding: 4px 8px;
  border-radius: 16px;
  height: 30px;
  display: flex;
  align-items: center;
  font-size: 14px;
  color: var(--text-color);
  display: flex;
  align-items: center;
  column-gap: 8px;
  background-color: var(--bg-color);
  cursor: pointer;
}
.input-box__chip:hover {
  box-shadow: var(--box-shadow);
}
.input-box__icon {
  height: 100%;
  display: flex;
  align-items: center;
  width: 14px;
}
.input-box__icon svg {
  fill: var(--text-color);
}
.input-box__icon--opened {
  transform: rotateX(180deg);
}
.input-box__input {
  flex-grow: 1;
  min-width: 60px;
  height: 30px;
  font-size: 16px;
  border: none;
  color: var(--text-color);
}
.input-box__input:focus {
  outline: none;
}
.options-box {
  display: flex;
  width: 400px;
  box-shadow: var(--box-shadow);
  border-radius: 4px;
}
.options-box__container {
  width: 100%;
  max-height: 400px;
  overflow-y: auto;
}
.options-box__option {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 24px;
  cursor: pointer;
  color: var(--text-color);
}
.options-box__option:hover {
  background-color: var(--bg-color);
}
.options-box__icon {
  height: 100%;
  display: flex;
  align-items: center;
  width: 14px;
}
.options-box__icon svg {
  fill: var(--text-color);
}
.options-box__icon--hide {
  visibility: hidden;
}
.options-box__option--selected {
  background-color: var(--bg-color);
}
.options-box__option--empty-state {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 24px;
  color: var(--text-color);
}
</style>
