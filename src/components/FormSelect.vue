<template>
  <div class="combobox" v-click-outside="hideOptions">
    <label id="combobox-label" class="combobox__label">{{ label }}</label>
    
    <div class="combobox__body">
        <button
          type="button"
          ref="button"
          role="combobox"
          id="combobox-button" 
          class="combobox__button" 
          aria-haspopup="listbox"
          aria-labelledby="combobox-label" 
          :aria-expanded="optionsVisible"
          tabindex="0"
          @click="toggleOptions"
          @keyup.up.down.prevent="toggleOptions"
        >
          {{ chosenOption ? chosenOption.value: '' }}
          <span v-if="!chosenOption" class="combobox__placeholder">
            {{ placeholder }}
          </span>

          <img
            src="@/assets/images/icons/dropdown-icon.svg"
            class="dropdown"
            aria-hidden="hidden"
            alt=""
          >
        </button>

        <ul
          v-show="optionsVisible"
          ref="optionsMenu"
          class="options__list"
          role="listbox"
          aria-labelledby="combobox-label"
          :aria-activedescendant="activeDescendant"
          tabindex="-1"
          @keyup.up.prevent="selectPrevOption"
          @keyup.down.prevent="selectNextOption"
          @keydown.enter.space.prevent="setValue"
          @keydown.esc.prevent="reset"
        >
          <li
            v-for="(option, index) in options"
            :key="option.id"
            :id="`option-${option.id}`"
            role="option"
            :aria-selected="chosenOptionIndex === index"
            :data-value="option.value"
            class="option"
            :class="chosenOptionIndex === index && 'option--active'"
            @click="handleOptionClick(index)"
            ref="listboxOption"
          >
            {{ option.value }}
          </li>
        </ul>
    </div>
  </div>
</template>

<script>
import vClickOutside from 'click-outside-vue3'

export default {
  name: 'FormSelect',

  directives: {
    clickOutside: vClickOutside.directive
  },

  props: {
    label: {
      type: String,
      required: true
    },
    placeholder: {
      type: String,
      default: "Select"
    },
    options: {
      type: Array,
      default: () => []
    },
    chosenOption: {
      type: Object,
      default: () => {}
    }
  },

  data() {
    return {
      chosenOptionIndex: -1,
      optionsVisible: false,
      tabKeyPressed: false,
    }
  },
  
  computed: {
    activeOptionIndex() {
      return this.options.findIndex(
        option => option === this.chosenOption
      );
    },
    activeDescendant() {
      return this.chosenOptionIndex !== -1 ? `option-${this.chosenOptionIndex}`: 'option-0';
    },
  },

  methods: {
    handleOptionClick(index) {
      this.$emit("setValue", index);
      this.reset();
    },
    handleBlur(e) {
      if (this.$el.contains(e.relatedTarget)) return;
      this.hideOptions();
    },
    toggleOptions() {
      this.optionsVisible ? this.hideOptions() : this.showOptions();
    },
    async showOptions() {
      this.optionsVisible = true;
      await this.$nextTick();
      this.$refs.optionsMenu.focus();
      this.chosenOptionIndex = 0;
    },
    hideOptions() {
      this.optionsVisible = false;
    },
    async reset() {
      this.hideOptions();
      await this.$nextTick();
      this.$refs.button.focus();
    },
    selectPrevOption() {
      if (this.chosenOptionIndex === 0) {
        this.chosenOptionIndex = this.options.length - 1;
      }
      else {
        this.chosenOptionIndex--;
      }
    },
    selectNextOption() {
      if (this.chosenOptionIndex === this.options.length - 1) {
        this.chosenOptionIndex = 0;
      }
      else {
        this.chosenOptionIndex++;
      }
    },
    setValue() {
      this.$emit("setValue", this.chosenOptionIndex);
      this.reset();
    },
  }
}
</script>

<style scoped lang="scss">
  .combobox {
    width: fit-content;
    margin: 0 auto;

    color: $black;

    &__body {
      margin-top: 4px;
    }

    &__button {
      position: relative;

      width: 289px;
      height: 50px;
      padding: 14px 20px;

      background: transparent;

      border: 1px solid $black;
      border-radius: 5px;

      box-sizing: border-box;

      font-weight: 400;
      font-size: 18px;
      line-height: 22px;
      text-align: left;

      cursor: pointer;

      .dropdown {
        position: absolute;

        top: 18px;
        right: 22px;
      }
    }

    &__placeholder {
      color: $gray;
    }
  }

  .combobox .options {
    &__list {
      margin-top: 5px;

      border: 1px solid $black;
      border-radius: 0px 0px 5px 5px;

      .option {
        padding: 12px 18px;

        cursor: pointer;

        &:hover, &--active {
          background: $lightGray;
        }
      }
    }
  }
</style>
