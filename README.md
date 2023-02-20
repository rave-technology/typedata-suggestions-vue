# Vue Typedata

[![npm version](https://badge.fury.io/js/typedata-suggestions-vue.svg)](https://badge.fury.io/js/typedata-suggestions-vue)
[![npm downloads](https://img.shields.io/npm/dw/typedata-suggestions-vue)](https://badge.fury.io/js/typedata-suggestions-vue)
[![NPM license](https://img.shields.io/npm/l/typedata-suggestions-vue)](https://github.com/rave-technology/typedata-suggestions-vue/blob/main/LICENSE)

Vue-плагин для сервиса подсказок [TypeData.net](https://typedata.net?utm_source=github&utm_medium=vue-component).

| Версия   | Описание          |
|-----------|----------------------|
| 1.\*.\*   | Версия для Vue-2 |

## Установка

[npm package](https://www.npmjs.com/package/typedata-suggestions-vue)

```bash
$ npm install typedata-suggestions-vue --save
```

## Использование

```html
<template>
    <div id="app">
        <typedata-suggestions-vue
                :token="token"
                :on-change="changed"
        />
    </div>
</template>
<script>
    import Vue from 'vue';
    import TypedataSuggestionsVue from '@/typedata-suggestions-vue.vue';

    export default Vue.extend({
        name: 'ServeDev',
        components: {
            TypedataSuggestionsVue
        },
        data() {
            return {
                token: "TOKEN"
            }
        },
        methods: {
            changed(data) {
                console.log(data);
            }
        }
    });
</script>
```

### Свойства

| Свойство | Обязательно | Тип | Описание | По умолчанию |
|---|---|---|---|---|
| token | Да | `string` | Токен авторизации TypeData.net | - |
| query | Нет | `string` | Заранее вписанный запрос | - |
| placeholder | Нет | `string` | Placeholder  | Введите адрес в свободной форме |
| titleSuggest | Нет | `string` | Текс над подсказками | Выберите вариант или продолжите ввод: |
| autoload | Нет | `bool` | Если указан параметр `query`, то автоматически выполнится поиск | false |
| disabled | Нет | `bool` | Блокирует ввод данный в input | false |
| inputName | Нет | `string` | Параметр name в input | - |
| defaultClass | Нет | `string` | CSS класс по-умолчанию | typedata-suggestions-vue |
| classes | Нет | `string` | список пользовательских классов через пробел | - |
| onChange | Нет | `function` | Функция, которая возвращает выбранный элемент | - |
| highlightClassName | Нет | `string` | Имя класса CSS, примененное к выделенному тексту | - |
| unhighlightClassName | Нет | `string` | Имя класса CSS, примененное к невыделенному тексту | - |
| highlightTag | Нет | `string` | Тип тега для обертывания вокруг выделенных совпадений; по умолчанию для `mark` но также может быть компонентом | mark |


## Одноранговые зависимости
- [vue](https://github.com/vuejs/vue)

## Зависимости
- [axios](https://github.com/axios/axios)
- [vue-word-highlighter](https://github.com/kawamataryo/vue-word-highlighter)

Copyright (c) 2023 Rave Technology. Licensed under the [MIT license](https://github.com/rave-technology/typedata-suggestions-vue/blob/master/LICENSE).