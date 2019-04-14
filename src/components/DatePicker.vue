<template>
  <v-menu
    v-model="menu"
    :close-on-content-click="false"
    :nudge-right="40"
    lazy
    transition="scale-transition"
    offset-y
    full-width
    min-width="290px"
  >
    <v-text-field
      slot="activator"
      :value="value"
      :label="label"
      prepend-icon="event"
      readonly
      @input="onChange"
    />
    <v-date-picker
      :value="value"
      :max="max"
      :min="min"
      :allowed-dates="allowedDates"
      @input="onChange"
    />
  </v-menu>
</template>

<script>
export default {
  name: 'DatePicker',
  props: {
    value: {
      type: String,
      required: true,
    },
    label: {
      type: String,
      required: true,
    },
    max: {
      type: String,
      default: undefined,
    },
    min: {
      type: String,
      default: undefined,
    },
    allowedDates: {
      type: Function,
      default: null,
    },
  },
  data() {
    return {
      menu: false,
    }
  },
  methods: {
    onChange(newValue) {
      this.menu = false
      this.$emit('input', newValue)
    },
  },
}
</script>
