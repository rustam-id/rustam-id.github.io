# Vue+Vuetify table
Работающий пример доступен по [ссылке](https://rustam-id.github.io/)

## О приложении
Работающий прототип частично адаптивной страницы с интерактивной таблицей с функциями сортировки, поиска (фильтрации), а так же редактированием полей через модальное окно (с валидацией значений времени).

## Интерфейс

Шапка и элементы управления:

![](https://raw.githubusercontent.com/rustam-id/rustam-id.github.io/master/readme_img/top.png)

Нижняя часть таблицы

![](https://raw.githubusercontent.com/rustam-id/rustam-id.github.io/master/readme_img/bottom.png)

Активное модальное окно (доступно по клику на строке)

![](https://raw.githubusercontent.com/rustam-id/rustam-id.github.io/master/readme_img/modal.png)

## Используемые средства:

Реализовано на [@vue/cli 4.0.5](https://vuejs.org/)+[vuetify](https://vuetifyjs.com/ru/)

Для удобства работы с датами использована библиотека 
[moment.js](https://momentjs.com/)

## Установка и запуск:


### Подготовка к установке:
```
npm install
```

### Запуск в режиме разработки:
```
npm run serve
```

### Подготовка проекта к размещению:
```
npm run build
```

### Больше подробностей по конфигурации:
See [Configuration Reference](https://cli.vuejs.org/config/).
