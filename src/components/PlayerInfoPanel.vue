<template>
  <v-toolbar app>
  <v-toolbar-title><a href="/artist">{{ trackInfo.artist }}</a> - {{ trackInfo.title }}</v-toolbar-title>
    <v-spacer></v-spacer>
    <v-toolbar-title>{{ trackInfo.seek | minutes }}/{{ trackInfo.duration | minutes }}</v-toolbar-title>
  </v-toolbar>
</template>

<script>
  export default {
    props: {
      trackInfo: Object
    },
  }
</script>