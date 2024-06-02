<template>
    <div>
      <div>{{ multiselect =='true'? 'Multiselect Enabled' : 'Single Select' }}</div>
      <div class="unique-select-container" @click="toggleDropdown" ref="selectContainer">
        <div class="unique-select-values" ref="selectValues">
          <span v-if="!value1.length">{{ placeholder }}</span>
          <template v-else>
            <span v-for="(option, index) in displayedOptions" :key="index">{{ option }}</span>
            <span v-if="remainingCount > 0">+{{ remainingCount }}</span>
          </template>
        </div>
        <div :class="{ 'unique-arrow-up': arrowUp, 'unique-arrow-down': !arrowUp }" @click.stop="toggleArrowDirection">🔽</div>
        <div v-if="dropdownVisible" class="unique-options" :style="{ width: optionWidth + 'px' }">
          <label v-for="item in options" :key="item.value" @click.stop="selectOption(item)">
            {{ item.label }}
            <div>
              <span v-if="value1.includes(item.value)">✅</span>
            </div>
          </label>
        </div>
      </div>
    </div>
  </template>
  <script>
export default {
  props: {
    placeholder: {
      type: String,
      default: 'Select'
    },
    multiselect: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      options: [
        { value: 'Olma', label: 'Olma' },
        { value: 'Banan', label: 'Banan' },
        { value: 'Lola', label: 'Lola' },
        { value: 'Tarvuz', label: 'Tarvuz' },
        { value: 'Qovun', label: 'Qovun' }
      ],
      value1: [],
      dropdownVisible: false,
      arrowUp: false,
      optionWidth: '',
      optionSpanWidth: '',
      containerWidth: ''
    };
  },
  computed: {
    selectedOptions() {
      return this.value1.map(val => this.options.find(opt => opt.value === val).label);
    },
    displayedOptions() {
      const maxVisibleOptions = Math.floor(this.containerWidth / this.optionSpanWidth);
      return this.selectedOptions.slice(0, maxVisibleOptions);
    },
    remainingCount() {
      const maxVisibleOptions = Math.floor(this.containerWidth / this.optionSpanWidth);
      return this.selectedOptions.length - maxVisibleOptions;
    }
  },
  methods: {
    toggleDropdown() {
      this.dropdownVisible = !this.dropdownVisible;
      this.arrowUp = !this.arrowUp;
      if (this.dropdownVisible) {
        document.addEventListener('click', this.handleClickOutside);
      } else {
        document.removeEventListener('click', this.handleClickOutside);
      }
    },
    handleClickOutside(event) {
      if (!this.$refs.selectContainer.contains(event.target)) {
        this.dropdownVisible = false;
        this.arrowUp = false;
        document.removeEventListener('click', this.handleClickOutside);
      }
    },
    toggleSelect(value) {
      if (this.multiselect) {
        const index = this.value1.indexOf(value);
        if (index === -1) {
          this.value1.push(value);
        } else {
          this.value1.splice(index, 1);
        }
      } else {
        this.value1 = [value];
        this.dropdownVisible = false;
        this.arrowUp = false;
        window.removeEventListener('click', this.handleClickOutside);
      }
      this.$emit('input', this.value1);
    },
    selectOption(option) {
      this.toggleSelect(option.value);
    },
    toggleArrowDirection() {
      if (this.dropdownVisible && this.value1.length > 0) {
        this.arrowUp = !this.arrowUp;
      }
    },
    calculateWidths() {
      this.containerWidth = this.$refs.selectContainer.clientWidth;
      const optionSpans = this.$refs.selectValues.getElementsByTagName('span');
      if (optionSpans.length > 0) {
        this.optionSpanWidth = optionSpans[0].clientWidth + 10;
      } else {
        this.optionSpanWidth = 100;
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.calculateWidths();
      window.addEventListener('resize', this.calculateWidths);
    });
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.calculateWidths);
    window.removeEventListener('click', this.handleClickOutside);
  }
};
</script>
