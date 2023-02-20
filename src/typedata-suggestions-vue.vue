<template>
  <div :class="[defaultClass, ...getClasses(), {'typedata-suggestions-vue_open': (suggestions.length > 0 && focused == true ? true : false)}]">
    <div :class="defaultClass+'__container'">
      <div class="typedata-suggestions-vue__search">
        <input
            type="text"
            :class="defaultClass+'__input'"
            placeholder=""
            v-model="queryInput"
            @input="onInputChange"
            @keydown="onKeyPress"
            @focus="onInputFocus"
            @blur="onInputBlur"
            :auto-complete="autocomplete"
            :disabled="disabled"
            :name="inputName"
        />
      </div>
      <div v-if="suggestions.length > 0 && focused == true" :class="defaultClass+'__suggestions'">
        <div :class="defaultClass+'__suggestions-title'">
          {{ titleSuggest }}
          <img @mousedown="goToSite()" :class="defaultClass+'__suggestions-copy'" src="https://typedata.net/logo.svg">
        </div>
        <Highlighter
            v-for="(suggestion, index) in suggestions"
            :key="`suggestion_${index}`"
            :class="[defaultClass+`__suggestions-item`,{[defaultClass+`__suggestions-item_current`]:index === suggestionIndex}]"
            :searchWords="queryInput.split(' ')"
            :autoEscape="true"
            :textToHighlight="suggestion.value"
            :highlight-class-name="highlightClassName"
            :unhighlight-class-name="unhighlightClassName"
            :highlight-tag="highlightTag"
            @mousedown="onSuggestionClick(index)"
        />
      </div>
    </div>
  </div>
</template>
<script>
import Highlighter from 'vue-highlight-words';
import axios from 'axios';

export default {
  name: 'TypedataSuggestionsVue',
  components: {Highlighter},
  props: {
    token: {
      type: String,
      required: true
    },
    query: {
      type: String,
      default: ''
    },
    placeholder: {
      type: String,
      default: 'Введите адрес в свободной форме'
    },
    titleSuggest: {
      type: String,
      default: 'Выберите вариант или продолжите ввод:'
    },
    autoload: {
      type: Boolean,
      default: false
    },
    autocomplete: {
      type: String,
      default: ''
    },
    disabled: {
      type: Boolean,
      default: false
    },
    inputName: {
      type: String,
      default: ''
    },
    defaultClass: {
      type: String,
      default: 'typedata-suggestions-vue'
    },
    classes: {
      type: String,
      default: ''
    },
    onChange: {
      type: Function,
    },
    highlightClassName: {
      type: String,
      default: ''
    },
    unhighlightClassName: {
      type: String,
      default: ''
    },
    highlightTag: {
      type: String,
      default: 'mark'
    },
  },
  data() {
    return {
      url_api: "https://api.typedata.net/v1/suggest/address",
      focused: false,
      queryInput: "",
      suggestions: [],
      suggestionIndex: -1,
    }
  },
  computed: {},
  async created() {
    this.inputQuery = this.query ? this.query : '';

    if (this.autoload && this.query) {
      this.suggestions = await this.fetchSuggestions();
    }
  },
  methods: {
    getClasses() {
      return this.classes ? this.classes.split(' ') : [];
    },

    onInputFocus() {
      this.focused = true;
    },

    onInputBlur() {
      this.focused = false;
    },

    async onInputChange() {
      if (this.queryInput == "") {
        return false;
      }
      this.suggestions = await this.suggest();
      this.suggestionIndex = -1;
    },

    async onKeyPress(e) {
      const ARROW_DOWN = 40;
      const ARROW_UP = 38;
      const ENTER = 13;
      if ([ARROW_DOWN, ARROW_UP, ENTER].indexOf(e.which) === -1) {
        return false;
      }
      e.preventDefault();
      switch (e.which) {
        case ARROW_DOWN :
          if (this.suggestionIndex < this.suggestions.length - 1) {
            this.suggestionIndex = this.suggestionIndex + 1;
            this.queryInput = this.suggestions[this.suggestionIndex].value;
          }
          break;
        case ARROW_UP :
          if (this.suggestionIndex >= 0) {
            this.suggestionIndex = this.suggestionIndex - 1;
            this.queryInput =
                this.suggestionIndex === -1
                    ? this.queryInput
                    : this.suggestions[this.suggestionIndex].value;
          }
          break;
        case ENTER:
          if (this.suggestionIndex >= 0) {
            await this.selectSuggestion(this.suggestionIndex);
          }
          break;
      }
    },
    async onSuggestionClick(index) {
      await this.selectSuggestion(index);
    },
    async selectSuggestion(index) {
      if (this.suggestions.length < index - 1) {
        return false;
      }
      this.queryInput = this.suggestions[index].value;
      this.focused = false;
      await this.$nextTick();
      if (this.onChange) {
        this.onChange(this.suggestions[index]);
      }

      this.suggestionIndex = -1;
      this.suggestions.length = 0;
    },

    goToSite() {
      const url = `https://typedata.net/?utm_source=widget&utm_medium=copyright&utm_campaign=` + location.host;
      window.open(url, "_blank");
    },

    async suggest() {
      try {
        const request = {
          query: this.queryInput
        };
        const {data: {suggestions}} = await axios.post(this.url_api, request, {
          headers: {
            'Content-Type': 'application/json',
            'Accept': 'application/json',
            'Authorization': `Token ${this.token}`,
          }
        });

        return suggestions;
      } catch (error) {
        this.$emit('handleError', error);
        return [];
      }
    }
  }
};
</script>

<style scoped>
.typedata-suggestions-vue {
  width: 100%;
  position: relative;
}

.typedata-suggestions-vue__container {
  width: 100%;
  position: relative;
}

.typedata-suggestions-vue__input {
  border: 1px solid #cbd5e1;
  border-radius: 7px;
  color: #000;
  height: 42px;
  padding: 0 15px;
  width: 100%;
  outline: none;
  transition: 0.3s;
  box-sizing: border-box;
  font-size: 14px;
}

.typedata-suggestions-vue__input:focus {
  border: 1px solid #4d5fff;
  box-shadow: 0 0 0 rgba(0, 0, 0, 0.15);
}

.typedata-suggestions-vue__suggestions {
  position: absolute;
  z-index: 10;
  width: 100%;
  width: calc(100% - 2px);
  display: flex;
  flex-direction: column;
  background-color: #fff;
  border: 1px solid #999;
  border-radius: 7px;
  margin-top: -1px;
  overflow: hidden;
}

.typedata-suggestions-vue__suggestions-item {
  padding: 10px;
  cursor: pointer;
  transition: 0.3s;
}

.typedata-suggestions-vue__suggestions-title {
  padding: 5px 10px;
  color: #565656;
}

.typedata-suggestions-vue__suggestions-copy {
  float: right;
  height: 15px;
  margin-top: 3px;
  cursor: pointer;
}

.typedata-suggestions-vue__suggestions-item-highlight {
  background-color: #ffdfbd;
}

.typedata-suggestions-vue__suggestions-item:hover {
  background-color: #f2f2f2;
}

.typedata-suggestions-vue__suggestions-item_current {
  background-color: #fff5e7;
}

.typedata-suggestions-vue_open .typedata-suggestions-vue__input {
  border-radius: 0;
  border-top-left-radius: 7px;
  border-top-right-radius: 7px;
}

.typedata-suggestions-vue_open .typedata-suggestions-vue__suggestions {
  border-radius: 0;
  border-bottom-right-radius: 7px;
  border-bottom-left-radius: 7px;
}
</style>
