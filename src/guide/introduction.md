---
footer: false
---

# Введение {#introduction}

:::info Вы читаете документацию по Vue 3!

- Поддержка Vue 2 прекратится 31 декабря 2023 г. Узнайте больше о [Vue 2 Extended LTS] (https://v2.vuejs.org/lts/).
- Документация по Vue 2 перемещена на [v2.vuejs.org] (https://v2.vuejs.org/).
- Обновление с Vue 2? Ознакомьтесь с [Руководством по миграции] (https://v3-migration.vuejs.org/).
  :::

<style src="@theme/styles/vue-mastery.css"></style>
<div class="vue-mastery-link">
  <a href="https://www.vuemastery.com/courses/" target="_blank">
    <div class="banner-wrapper">
      <img class="banner" alt="Vue Mastery banner" width="96px" height="56px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vuemastery-graphical-link-96x56.png" />
    </div>
    <p class="description">Learn Vue with video tutorials on <span>VueMastery.com</span></p>
    <div class="logo-wrapper">
        <img alt="Vue Mastery Logo" width="25px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vue-mastery-logo.png" />
    </div>
  </a>
</div>

## Что такое Вью? {#what-is-vue}

Vue (произносится /vjuː/, как **view**) — это среда JavaScript для создания пользовательских интерфейсов. Он строится на основе стандартных HTML, CSS и JavaScript и предоставляет декларативную и основанную на компонентах модель программирования, которая помогает вам эффективно разрабатывать пользовательские интерфейсы, будь то простые или сложные.

Вот минимальный пример:

<div class="options-api">

```js
import { createApp } from 'vue'

createApp({
  data() {
    return {
      count: 0
    }
  }
}).mount('#app')
```

</div>
<div class="composition-api">

```js
import { createApp, ref } from 'vue'

createApp({
  setup() {
    return {
      count: ref(0)
    }
  }
}).mount('#app')
```

</div>

```vue-html
<div id="app">
  <button @click="count++">
    Count is: {{ count }}
  </button>
</div>
```

**Result**

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<div class="demo">
  <button @click="count++">
    Count is: {{ count }}
  </button>
</div>

Приведенный выше пример демонстрирует две основные функции Vue:

- **Декларативный рендеринг**: Vue расширяет стандартный HTML синтаксис шаблона, который позволяет нам декларативно описывать вывод HTML на основе состояния JavaScript.

- **Реактивность**: Vue автоматически отслеживает изменения состояния JavaScript и эффективно обновляет DOM, когда изменения происходят.

Возможно, у вас уже есть вопросы — не волнуйтесь. Мы рассмотрим каждую мелочь в остальной части документации. А пока, пожалуйста, прочитайте, чтобы вы могли получить общее представление о том, что предлагает Vue.

:::tip Предпосылки
Остальная часть документации предполагает базовое знакомство с HTML, CSS и JavaScript. Если вы новичок во фронтенд-разработке, может быть не лучшей идеей прыгать сразу во фреймворк в качестве первого шага — усвойте основы, а затем вернитесь! Вы можете проверить свой уровень знаний с помощью [этого обзора JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript). Предыдущий опыт работы с другими фреймворками помогает, но не обязателен.
:::

## Прогрессивная структура {#the-progressive-framework}

Vue — это фреймворк и экосистема, охватывающая большинство общих функций, необходимых для фронтенд-разработки. Но сеть чрезвычайно разнообразна — вещи, которые мы создаем в сети, могут сильно различаться по форме и масштабу. Имея это в виду, Vue спроектирован так, чтобы быть гибким и постепенно адаптироваться. В зависимости от вашего варианта использования Vue можно использовать по-разному:

- Улучшение статического HTML без шага сборки
- Встраивание в виде веб-компонентов на любую страницу
- Одностраничное приложение (SPA)
- Полный стек/рендеринг на стороне сервера (SSR)
- Jamstack / Создание статического сайта (SSG)
- Ориентация на настольные компьютеры, мобильные устройства, WebGL и даже на терминалы.

Если вы находите эти концепции пугающими, не волнуйтесь! Учебное пособие и руководство требуют только базовых знаний HTML и JavaScript, и вы должны быть в состоянии следовать им, не будучи экспертом ни в одном из них.

Если вы опытный разработчик, заинтересованный в том, как лучше всего интегрировать Vue в свой стек, или вам интересно, что означают эти термины, мы обсудим их более подробно в разделе [Способы использования Vue](/guide/extras/ways-of- использование-вью).

Несмотря на гибкость, основные знания о том, как работает Vue, являются общими для всех этих вариантов использования. Даже если вы сейчас только новичок, знания, полученные на этом пути, останутся полезными, когда вы будете расти, чтобы решать более амбициозные задачи в будущем. Если вы ветеран, вы можете выбрать оптимальный способ использования Vue в зависимости от проблем, которые вы пытаетесь решить, сохраняя при этом ту же производительность. Вот почему мы называем Vue «Прогрессивной платформой»: это среда, которая может расти вместе с вами и адаптироваться к вашим потребностям.

## Однофайловые компоненты {#single-file-components}

В большинстве проектов Vue с поддержкой инструментов сборки мы создаем компоненты Vue, используя HTML-подобный формат файлов, называемый **Single-File Component** (также известный как файлы `*.vue`, сокращенно **SFC**). Vue SFC, как следует из названия, инкапсулирует логику компонента (JavaScript), шаблон (HTML) и стили (CSS) в одном файле. Вот предыдущий пример, написанный в формате SFC:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>

SFC является определяющей функцией Vue и является рекомендуемым способом создания компонентов Vue, **если** ваш вариант использования требует настройки сборки. Вы можете узнать больше о [как и почему SFC](/guide/scaling-up/sfc) в специальном разделе, но пока просто знайте, что Vue выполнит настройку всех инструментов сборки за вас.

## Стили API {#api-styles}

Компоненты Vue можно создавать в двух разных стилях API: **Options API** и **Composition API**.

### API опций {#options-api}

С помощью API параметров мы определяем логику компонента, используя объект параметров, таких как «данные», «методы» и «смонтированные». Свойства, определяемые параметрами, отображаются в this внутри функций, которые указывают на экземпляр компонента:

```vue
<script>
export default {
  // Properties returned from data() become reactive state
  // and will be exposed on `this`.
  data() {
    return {
      count: 0
    }
  },

  // Methods are functions that mutate state and trigger updates.
  // They can be bound as event handlers in templates.
  methods: {
    increment() {
      this.count++
    }
  },

  // Lifecycle hooks are called at different stages
  // of a component's lifecycle.
  // This function will be called when the component is mounted.
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNptkMFqxCAQhl9lkB522ZL0HNKlpa/Qo4e1ZpLIGhUdl5bgu9es2eSyIMio833zO7NP56pbRNawNkivHJ25wV9nPUGHvYiaYOYGoK7Bo5CkbgiBBOFy2AkSh2N5APmeojePCkDaaKiBt1KnZUuv3Ky0PppMsyYAjYJgigu0oEGYDsirYUAP0WULhqVrQhptF5qHQhnpcUJD+wyQaSpUd/Xp9NysVY/yT2qE0dprIS/vsds5Mg9mNVbaDofL94jZpUgJXUKBCvAy76ZUXY53CTd5tfX2k7kgnJzOCXIF0P5EImvgQ2olr++cbRE4O3+t6JxvXj0ptXVpye1tvbFY+ge/NJZt)

### API композиции {#composition-api}

С Composition API мы определяем логику компонента, используя импортированные функции API. В SFC Composition API обычно используется с [`<script setup>`](/api/sfc-script-setup). Атрибут setup — это подсказка, которая заставляет Vue выполнять преобразования во время компиляции, что позволяет нам использовать Composition API с меньшим количеством шаблонов. Например, импорт и переменные/функции верхнего уровня, объявленные в `<script setup>`, можно напрямую использовать в шаблоне.

Вот тот же компонент с точно таким же шаблоном, но с использованием Composition API и `<script setup>`:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// reactive state
const count = ref(0)

// functions that mutate state and trigger updates
function increment() {
  count.value++
}

// lifecycle hooks
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNpNkMFqwzAQRH9lMYU4pNg9Bye09NxbjzrEVda2iLwS0spQjP69a+yYHnRYad7MaOfiw/tqSliciybqYDxDRE7+qsiM3gWGGQJ2r+DoyyVivEOGLrgRDkIdFCmqa1G0ms2EELllVKQdRQa9AHBZ+PLtuEm7RCKVd+ChZRjTQqwctHQHDqbvMUDyd7mKip4AGNIBRyQujzArgtW/mlqb8HRSlLcEazrUv9oiDM49xGGvXgp5uT5his5iZV1f3r4HFHvDprVbaxPhZf4XkKub/CDLaep1T7IhGRhHb6WoTADNT2KWpu/aGv24qGKvrIrr5+Z7hnneQnJu6hURvKl3ryL/ARrVkuI=)

### Что выбрать? {#which-to-choose}

Оба стиля API полностью подходят для распространенных вариантов использования. Это разные интерфейсы, работающие на одной и той же базовой системе. На самом деле API параметров реализован поверх API композиции! Фундаментальные концепции и знания о Vue являются общими для обоих стилей.

API параметров основан на концепции «экземпляра компонента» («this», как показано в примере), который обычно лучше согласуется с основанной на классах ментальной моделью для пользователей, имеющих опыт работы с языком ООП. Он также более удобен для новичков, поскольку абстрагируется от деталей реактивности и обеспечивает организацию кода с помощью групп параметров.

Composition API сосредоточен на объявлении реактивных переменных состояния непосредственно в области действия функции и составлении состояния из нескольких функций вместе, чтобы справиться со сложностью. Это более свободная форма и для эффективного использования требуется понимание того, как реактивность работает в Vue. В свою очередь, его гибкость позволяет использовать более мощные шаблоны для организации и повторного использования логики.

Вы можете узнать больше о сравнении двух стилей и потенциальных преимуществах Composition API в разделе [Composition API FAQ](/guide/extras/composition-api-faq).

Если вы новичок в Vue, вот наша общая рекомендация:

- В учебных целях используйте тот стиль, который кажется вам более понятным. Опять же, большинство основных концепций являются общими для двух стилей. Вы всегда можете подобрать другой стиль позже.

- Для производственного использования:

  - Перейдите с API параметров, если вы не используете инструменты сборки или планируете использовать Vue в первую очередь в сценариях с низкой сложностью, например. прогрессивное улучшение.

  — Используйте Composition API + Single-File Components, если вы планируете создавать полные приложения с помощью Vue.

Вам не нужно придерживаться только одного стиля на этапе обучения. В остальной части документации будут представлены образцы кода в обоих стилях, где это применимо, и вы можете переключаться между ними в любое время с помощью **переключателей предпочтений API** в верхней части левой боковой панели.

## Остались вопросы? {#still-got-questions}

Проверьте наши [FAQ](/about/faq).

## Выберите свой путь обучения {#pick-your-learning-path}

У разных разработчиков разные стили обучения. Не стесняйтесь выбирать путь обучения, который соответствует вашим предпочтениям, хотя мы рекомендуем просмотреть весь контент, если это возможно!

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">Try the Tutorial</p>
    <p class="next-steps-caption">For those who prefer learning things hands-on.</p>
  </a>
  <a class="vt-box" href="/guide/quick-start.html">
    <p class="next-steps-link">Read the Guide</p>
    <p class="next-steps-caption">The guide walks you through every aspect of the framework in full detail.</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">Check out the Examples</p>
    <p class="next-steps-caption">Explore examples of core features and common UI tasks.</p>
  </a>
</div>
