<template>
  <div class="main">
    <div class="main__output-result">
      Selected States on parent component:
      <div v-if="selectedStates.length > 0">
        <span v-for="state in selectedStates" :key="state.id">
          {{ state.name }}
        </span>
      </div>
      <div v-else>
        None
      </div>
    </div>
    <div v-if="loadingRequest" class="main__loading-skeleton"></div>
    <div v-else-if="errorOnRequest" class="main__error-message">
      <div class="main__icon">
        <!-- error icon -->
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">
          <!--! Font Awesome Pro 6.0.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. -->
          <path
            d="M256 0C114.6 0 0 114.6 0 256s114.6 256 256 256s256-114.6 256-256S397.4 0 256 0zM232 152C232 138.8 242.8 128 256 128s24 10.75 24 24v128c0 13.25-10.75 24-24 24S232 293.3 232 280V152zM256 400c-17.36 0-31.44-14.08-31.44-31.44c0-17.36 14.07-31.44 31.44-31.44s31.44 14.08 31.44 31.44C287.4 385.9 273.4 400 256 400z"
          />
        </svg>
      </div>
      {{ errorMessage }}
      <div @click="getStates()" class="main__reload-button">Reload</div>
    </div>
    <div class="main__component" v-else>
      <MultiSelect
        @getSelection="handleSelection"
        :options="states"
        :placeholder="MULTISELECT_PLACEHOLDER_TEXT"
        :noResults="MULTISELECT_NO_RESULTS_TEXT"
      />
    </div>
  </div>
</template>

<script>
import MultiSelect from './elements/MultiSelect.vue'

const MULTISELECT_PLACEHOLDER_TEXT = 'Select states'
const MULTISELECT_NO_RESULTS_TEXT = 'No states found'

export default {
  name: 'StatesList',
  components: {
    MultiSelect
  },
  data () {
    return {
      MULTISELECT_PLACEHOLDER_TEXT,
      MULTISELECT_NO_RESULTS_TEXT,
      selectedStates: [],
      states: [],
      loadingRequest: false,
      errorOnRequest: false,
      errorMessage: ''
    }
  },
  methods: {
    statesObjectToArray (object) {
      const statesArray = []
      for (const key in object) {
        statesArray.push({
          id: key,
          name: object[key]
        })
      }
      return statesArray
    },
    clearErrorState () {
      this.errorOnRequest = false
      this.errorMessage = ''
    },
    handleError (errorMessage) {
      this.errorMessage = errorMessage
      this.errorOnRequest = true
    },
    async getStates () {
      try {
        this.clearErrorState()
        this.loadingRequest = true
        const states = await this.$http.get(process.env.BACKEND_URL)
        const statesObject = states.body
        this.states = this.statesObjectToArray(statesObject)
      } catch (error) {
        this.handleError(error.bodyText)
      } finally {
        this.loadingRequest = false
      }
    },
    handleSelection (selection) {
      this.selectedStates = selection
    }
  },
  mounted () {
    this.getStates()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main {
  display: flex;
  flex-direction: column;
  align-items: center;
  row-gap: 16px;
}
.main__loading-skeleton {
  width: 400px;
  height: 50px;
  border-radius: 4px;
  animation: skeleton-pulse 2s infinite ease-in-out;
}
.main__error-message {
  border-radius: 4px;
  box-shadow: none;
  font-weight: 400;
  background-color: var(--error-bg-color);
  display: flex;
  align-items: center;
  column-gap: 8px;
  padding: 12px 24px;
  color: var(--error-text-color);
}
.main__icon {
  height: 100%;
  display: flex;
  align-items: center;
  width: 16px;
}
.main__icon svg {
  fill: var(--error-text-color);
}

.main__reload-button {
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
  background-color: white;
  cursor: pointer;
}
.main__reload-button:hover {
  box-shadow: var(--box-shadow);
}
</style>
