<template>
  <div class="cont">
    <!-- возможный контент+форма поиска/филтрации -->
    <v-card-title>
      <a
        href="https://github.com/rustam-id/rustam-id.github.io"
        target="”_blank”"
        class="mr-2"
      >Git Repo</a>
      <v-spacer></v-spacer>
      <v-text-field v-model="search" label="Поиск..." single-line hide-details color="red darken-2"></v-text-field>
    </v-card-title>

    <!-- создание таблицы с указанием стартовых значений и интерфейса -->
    <v-data-table
      :headers="headers"
      :items="wagons"
      :items-per-page="10"
      :search="search"
      class="elevation-1"
      :custom-sort="customSort"
      :footer-props="{pageText: '{0}-{1} из {2}', itemsPerPageText: 'Строк на странице'}"
    >
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-toolbar-title>Test Table</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>

          <!-- диалоговое модальное окно -->
          <v-dialog v-model="dialog" max-width="600px">
            <v-card>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.lastOperDtFormat"
                        label="Дата/время операции"
                        :rules="[rules.lastOperDtValidation]"
                        color="blue-grey darken-2"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.trainNumber"
                        color="blue-grey darken-2"
                        label="Номер поезда"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.invoiceNumber"
                        color="blue-grey darken-2"
                        label="Номер накладной"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.invoiceId"
                        color="blue-grey darken-2"
                        label="ИД накладной"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.ordNumber"
                        color="blue-grey darken-2"
                        label="№ п/п"
                        disabled
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.carNumber"
                        color="blue-grey darken-2"
                        label="Номер вагона"
                        disabled
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.trainIndex"
                        color="blue-grey darken-2"
                        label="Индекс поезда"
                        disabled
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.carStatus"
                        color="blue-grey darken-2"
                        label="Статус"
                        disabled
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.stateId"
                        color="blue-grey darken-2"
                        label="stateId"
                        disabled
                      ></v-text-field>
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
      <!-- обработка события клика -->
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
      <!-- кнопка сброса при ошибке загрузки -->
      <template v-slot:no-data>
        <v-btn color="primary" @click="init">Reset</v-btn>
      </template>
    </v-data-table>
  </div>
</template>

<script>
import moment from "moment"; //импорт библиотеки для работы с датами

export default {
  data() {
    return {
      //указание базовых значений и переменных состояний
      dialog: false, //начальное значение выключенного модального окна
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
      wagons: [], //старотовое значение массива с данными по вагонам
      editedIndex: -1, //старотовое значение индекса редактируемого вагона в массиве
      editedItem: {}, //старотовое значение данных по редактирумого вагону
      validItem: 1, //состояние валидности данных по полю "время"

      //создание правила для валидации поля с датой
      rules: {
        lastOperDtValidation: value => {
          if (
            moment(value, "DD.MM.YYYY HH:mm", true).isValid() ||
            value == "-"
          ) {
            this.validItem = 1;
            return true;
          } else {
            this.validItem = 0;
            return 'Дата в формате "ДД.ММ.ГГГГ ЧЧ:ММ"';
          }
        }
      }
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
    //запускаем init сразу после загрузки страницы для получения *.json и его обработки
    init() {
      fetch("table-data.json")
        .then(res => res.json())
        .then(
          res =>
            (this.wagons = res.map(item => {
              if (!item.lastOperDt) {
                item.lastOperDtFormat = "-";
              } else {
                //приводим значение времени к требуемому по ТЗ формату ДД.ММ.ГГГГ чч:мм
                item.lastOperDtFormat = moment(item.lastOperDt).format(
                  "DD.MM.YYYY HH:mm"
                );
              }
              return item;
            }))
        );
    },

    //определяем редактируемый элемент
    editItem(item) {
      this.editedIndex = this.wagons.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    close() {
      this.dialog = false;
    },
    //перезаписываем новые значения
    save() {
      if (this.validItem == true) {
        Object.assign(this.wagons[this.editedIndex], this.editedItem);
        this.close();
      } else {
        this.close();
      }
    },
    //метод сортировки значений
    customSort(items, index, isDesc) {
      //переопределяем значение времени в удобный к сортировке формат ISO 
      // в переменной lastOperDtFormatNew
      this.wagons.map(item => {
        item.lastOperDtFormatNew = moment(
          item.lastOperDtFormat,
          "DD.MM.YYYY HH:mm"
        ).format();
      });
      this.wagons.sort((a, b) => {
        //сортировка по значениям времени
        if (index == "lastOperDtFormat") {
          if (!isDesc[0])
            return (
              (a.lastOperDtFormatNew < b.lastOperDtFormatNew && -1) ||
              (b.lastOperDtFormatNew > a.lastOperDtFormatNew && 1) ||
              (a.lastOperDtFormatNew == null && 1)
            );
          else
            return (
              (a.lastOperDtFormatNew > b.lastOperDtFormatNew && -1) ||
              (b.lastOperDtFormatNew < a.lastOperDtFormatNew && 1) ||
              (b.lastOperDtFormatNew == null && 1)
            );
        } else {
          //сортировка по всем остальным значениям в таблице
          if (!isDesc[0])
            return (a[index] < b[index] && -1) || (b[index] > a[index] && 1);
          else return (a[index] > b[index] && -1) || (b[index] < a[index] && 1);
        }
      });
      return this.wagons;
    }
  }
};
</script>


<style scoped>
</style>