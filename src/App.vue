<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span class="font-weight-light">event finder</span>
      </v-toolbar-title>
    </v-toolbar>

    <v-content>
      <Top />
    </v-content>
  </v-app>
</template>

<script>
import Top from '@/pages/Top'

export default {
  components: {
    Top,
  },
}
</script>
