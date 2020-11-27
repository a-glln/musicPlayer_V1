<template>
    <v-card>
      <v-card-title primary-title>
        <h1>Artist</h1>
      </v-card-title>
      <v-card-text>
        <!-- {{ trackInfo.artist }} -->
      </v-card-text>
    </v-card>
</template>
<script>
  export default {
    props: {
      trackInfo: Object
    },
  }
</script>