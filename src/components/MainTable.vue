<template>
  <div class="cont">
    <v-card-title>
        <a href="https://github.com/rustam-id/rustam-id.github.io" target=”_blank” class="mr-2">Git Repo</a>
      <v-spacer></v-spacer>
      <v-text-field v-model="search" label="Поиск..." single-line hide-details color="red darken-2"></v-text-field>
    </v-card-title>

    <v-data-table
      :headers="headers"
      :items="carriage"
      :items-per-page="10"
      :search="search"
      class="elevation-1"
      :footer-props="{pageText: '{0}-{1} из {2}', itemsPerPageText: 'Строк на странице'}"
    >
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-toolbar-title>Test Table</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="600px">
            <v-card>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.lastOperDtFormat"
                        label="Дата/время операции"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedItem.trainNumber" label="Номер поезда"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedItem.invoiceNumber" label="Номер накладной"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedItem.invoiceId" label="ИД накладной"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedItem.ordNumber" label="№ п/п" disabled></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedItem.carNumber" label="Номер вагона" disabled></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedItem.trainIndex" label="Индекс поезда" disabled></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedItem.carStatus" label="Статус" disabled></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedItem.stateId" label="stateId" disabled></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="red darken-2" text @click="close">Отмена</v-btn>
                <v-btn color="red darken-2" text @click="save">Ок</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item="{item}">
        <tr @click="editItem(item)">
          <td>{{ item.ordNumber }}</td>
          <td>{{ item.carNumber }}</td>
          <td>{{ item.trainIndex }}</td>
          <td>{{ item.trainNumber }}</td>
          <td>{{ item.carStatus }}</td>
          <td>{{ item.lastOperDtFormat}}</td>
          <td>{{ item.invoiceNumber }}</td>
          <td>{{ item.invoiceId }}</td>
          <td>{{ item.stateId }}</td>
        </tr>
      </template>

      <template v-slot:no-data>
        <v-btn color="primary" @click="init">Reset</v-btn>
      </template>
    </v-data-table>
  </div>
</template>



<script>
import moment from "moment";

export default {
  data() {
    return {
      dialog: false,
      search: "",
      headers: [
        { text: "№ п/п", value: "ordNumber" },
        { text: "Номер вагона", value: "carNumber" },
        { text: "Индекс поезда", value: "trainIndex" },
        { text: "Номер поезда", value: "trainNumber" },
        { text: "Статус", value: "carStatus" },
        { text: "Дата/время операции", value: "lastOperDtFormat" },
        { text: "№ накладной", value: "invoiceNumber" },
        { text: "ИД накладной", value: "invoiceId" },
        { text: "stateId", value: "stateId" }
      ],
      carriage: [],
      editedIndex: -1,
      editedItem: {},
      defaultItem: {}
    };
  },
  created() {
    this.init();
  },
  сomputed: {},

  watch: {
    dialog(val) {
      val || this.close();
    }
  },
  methods: {
    init() {
      fetch("table-data.json")
        .then(res => res.json())
        .then(
          res =>
            (this.carriage = res.map(item => {
              if (!item.lastOperDt) {
                item.lastOperDtFormat = "-";
              } else {
                item.lastOperDtFormat = moment(item.lastOperDt).format(
                  "DD.MM.YYYY HH:mm"
                );
              }
              return item;
            }))
        );
    },

    editItem(item) {
      this.editedIndex = this.carriage.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    close() {
      this.dialog = false;
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      }, 300);
    },

    save() {
      Object.assign(this.carriage[this.editedIndex], this.editedItem);

      this.close();
    }
  }
};
</script>


<style scoped>
</style>