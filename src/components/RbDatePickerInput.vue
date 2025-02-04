<template>
  <div class="rb-date-picker-input" :class="cls">
    <b-input-group>
      <b-form-input
        v-model="inputValue"
        type="text"
        :resolve-value="inputValue"
        autocomplete="off"
        @input="inputChange"
        :size="size"
        :placeholder="placeholder"
        :disabled="disabled"
        :state="state"
        @click="clearInput"
      />
      <b-input-group-append>
        <b-form-datepicker
          ref="picker"
          v-model="datePickerValue"
          right
          button-only
          :dropup="dropup"
          locale="ru-RU"
          label-help=""
          :reset-button="!!datePickerValue"
          :reset-value="null"
          label-reset-button="Очистить"
          :start-weekday="startWeekday"
          :size="size"
          :button-variant="variant"
          :show-decade-nav="false"
          :hide-header="true"
          :disabled="disabled"
          :state="state"
          :min="setMinDate()"
          :max="maxDate"
          @input="datePickerChange"
        />
      </b-input-group-append>
    </b-input-group>
  </div>
</template>

<script>
import { UtDate } from '../utils/UtDate';

export default {
  name: 'RbDatePickerInput',
  props: {
    value: { String, Date },
    placeholder: { type: String, default: 'ДД.ММ.ГГГГ' },
    mask: { type: String, default: '##.##.####' },
    state: { type: Boolean, default: null },
    dropup: Boolean,
    disabled: Boolean,
    size: { type: String, default: null },
    variant: { type: String, default: 'light-outline' },
    minDate: { type: String, default: '' },
    maxDate: { type: String, default: '' },
    minCurrentDate: { type: Boolean, default: false },
    startWeekday: {
      type: [Number, String],
      default: 1,
    },
  },
  data: function () {
    return {
      inputValue: null,
      datePickerValue: null,
      inputPattern: 'DD.MM.YYYY',
      pickerPattern: 'YYYY-MM-DD',
    };
  },
  computed: {
    cls() {
      return {
        'is-invalid': this.state === false,
        'is-valid': this.state === true,
      };
    },
  },
  watch: {
    value: {
      handler() {
        this.onPropValueChange();
      },
      immediate: true,
    },

    datePickerValue() {
      this.$emit('change');
    },
  },
  methods: {
    datePickerChange(v) {
      if (!v) {
        return this.$emit('input', null);
      }
      let dt = this.strToDate(v);
      this.inputValue ='';
      this.$emit('input', UtDate.toIsoString(dt));
    },
    inputChange(v) {
      const date = this.strToDate(v);
      if (!v || v === '' || !date) {
        this.$emit('input', null);
      } else if (date) {
        if (v.length === this.mask.length && date) {
          this.datePickerValue = '';
          this.$emit('input', UtDate.toIsoString(date));
        }
      }
    },
    onPropValueChange() {
      if (this.value) {
        const dt = new Date(this.value);
        this.inputValue = '';
        this.datePickerValue = '';
      } else {
        this.inputValue = null;
        this.datePickerValue = null;
      }
    },
    strToDate(str) {
      if (/[0-9]{2}\.[0-9]{2}\.[0-9]{4}/.test(str)) {
        let dd = str.substring(0, 2);
        let mm = str.substring(3, 5);
        let yyyy = str.substring(6, 10);
        return new Date(`${mm}/${dd}/${yyyy}`);
      } else if (/[0-9]{4}-[0-9]{2}-[0-9]{2}/.test(str)) {
        let yyyy = str.substring(0, 4);
        let mm = str.substring(5, 7);
        let dd = str.substring(8, 10);
        return new Date(`${mm}/${dd}/${yyyy}`);
      } else {
        return null;
      }
    },
    setMinDate() {
      if (this.minDate) {
        return this.minDate;
      }
      if (this.minCurrentDate) {
        return new Date().toISOString();
      }
      return '';
    },
    clearInput() {
      if (!this.disabled) this.inputValue = '';
    },
    setValue() {
      const date = new Date(this.value);
      if (date instanceof Date) {
        this.onPropValueChange();
      }
    },
  },
  created() {
    this.setValue();
  },
};
</script>
