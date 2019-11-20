<template>
  <v-app>
  <!-- шапка сайта -->
    <v-app-bar
      app
     
    >
      <div class="d-flex align-center">
        <v-img
          alt=" Logo"
          class="shrink mr-2"
          contain
          src="http://www.vniizht.ru/img/img_header/logo_rzd_default_small.jpg"
          transition="scale-transition"
          width="100"
        />

        <v-img
          alt=" Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="http://www.vniizht.ru/img/logo_text_v8.gif"
          width="400"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="http://www.vniizht.ru/"
        target="_blank"
        text
        color="red darken-2"
      >
        <span class="mr-2" >Основной сайт</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>
<!-- подключение компонента таблицы -->
    <v-content>
      <MainTable/>
    </v-content>
  </v-app>
</template>

<script>
import MainTable from './components/MainTable';

export default {
  name: 'App',

  components: {
    MainTable,
  },

  data: () => ({
    //
  }),
};
</script>
