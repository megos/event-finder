<template>
  <v-menu
    :close-on-content-click="false"
    v-model="menu"
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
        @input="onChange"
        :label="label"
        prepend-icon="event"
        readonly
    ></v-text-field>
    <v-date-picker :value="value" @input="onChange"></v-date-picker>
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
