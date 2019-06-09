<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Hotel Rooms Viewer</span>
      </v-toolbar-title>
    </v-toolbar>

    <v-content>
      <HotelViewer/>
    </v-content>
  </v-app>
</template>

<script>
import HotelViewer from './components/HotelViewer'

export default {
  name: 'App',
  components: {
    HotelViewer
  },
  data () {
    return {
      //
    }
  }
}
</script>
