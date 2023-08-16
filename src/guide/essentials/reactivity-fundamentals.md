---
outline: deep
---

# Основы реактивности {#reactivity-fundamentals}

:::tip Предпочтение API
Эта страница и многие другие главы далее в руководстве содержат различное содержимое для API параметров и API композиции. В настоящее время вы предпочитаете <span class="options-api">Options API</span><span class="composition-api">Composition API</span>. Вы можете переключаться между стилями API с помощью переключателей «Предпочтения API» в верхней части левой боковой панели.
:::

<div class="options-api">

## Объявление реактивного состояния \* {#declaring-reactive-state}

В API параметров мы используем параметр «данные», чтобы объявить реактивное состояние компонента. Значение опции должно быть функцией, которая возвращает объект. Vue вызовет функцию при создании нового экземпляра компонента и обернет возвращенный объект в свою систему реактивности. Любые свойства верхнего уровня этого объекта проксируются в экземпляре компонента («this» в методах и хуках жизненного цикла):

```js{2-6}
export default {
  data() {
    return {
      count: 1
    }
  },

  // `mounted` is a lifecycle hook which we will explain later
  mounted() {
    // `this` refers to the component instance.
    console.log(this.count) // => 1

    // data can be mutated as well
    this.count = 2
  }
}
```

[Try it in the Playground](https://play.vuejs.org/#eNpFUNFqhDAQ/JXBpzsoHu2j3B2U/oYPpnGtoetGkrW2iP/eRFsPApthd2Zndilex7H8mqioimu0wY16r4W+Rx8ULXVmYsVSC9AaNafz/gcC6RTkHwHWT6IVnne85rI+1ZLr5YJmyG1qG7gIA3Yd2R/LhN77T8y9sz1mwuyYkXazcQI2SiHz/7iP3VlQexeb5KKjEKEe2lPyMIxeSBROohqxVO4E6yV6ppL9xykTy83tOQvd7tnzoZtDwhrBO2GYNFloYWLyxrzPPOi44WWLWUt618txvASUhhRCKSHgbZt2scKy7HfCujGOqWL9BVfOgyI=)

Эти свойства экземпляра добавляются только при первом создании экземпляра, поэтому вам необходимо убедиться, что все они присутствуют в объекте, возвращаемом функцией `data`. При необходимости используйте `null`, `undefined` или какое-либо другое значение-заполнитель для свойств, для которых желаемое значение еще недоступно.

Можно добавить новое свойство непосредственно в `this`, не включая его в `data`. Однако свойства, добавленные таким образом, не смогут запускать реактивные обновления.

Vue использует префикс `$` при предоставлении своих собственных встроенных API через экземпляр компонента. Он также резервирует префикс `_` для внутренних свойств. Вам следует избегать использования имен для свойств `data` верхнего уровня, которые начинаются с любого из этих символов.

### Реактивный прокси против оригинального \* {#reactive-proxy-vs-original}

В Vue 3 данные становятся реактивными за счет использования [Proxy JavaScript] (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). Пользователи, перешедшие с Vue 2, должны знать о следующем пограничном случае:

```js
export default {
  data() {
    return {
      someObject: {}
    }
  },
  mounted() {
    const newObject = {}
    this.someObject = newObject

    console.log(newObject === this.someObject) // false
  }
}
```

Когда вы получаете доступ к `this.someObject` после его назначения, значение является реактивным прокси исходного `newObject`. **В отличие от Vue 2, исходный `newObject` остается нетронутым и не будет сделан реактивным: убедитесь, что всегда обращаетесь к реактивному состоянию как к свойству `this`.**

</div>

<div class="composition-api">

## Объявление реактивного состояния \*\* {#declaring-reactive-state-1}

### `ref()` \*\* {#ref}

В Composition API рекомендуемым способом объявления реактивного состояния является использование функции [`ref()`](/api/reactivity-core#ref):

```js
import { ref } from 'vue'

const count = ref(0)
```

`ref()` принимает аргумент и возвращает его, заключенный в объект `ref` со свойством `.value`:

```js
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

> Смотрите также: [Typing Refs](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

Чтобы получить доступ к ссылкам в шаблоне компонента, объявите и верните их из функции `setup()` компонента:

```js{5,9-11}
import { ref } from 'vue'

export default {
  // `setup` is a special hook dedicated for the Composition API.
  setup() {
    const count = ref(0)

    // expose the ref to the template
    return {
      count
    }
  }
}
```

```vue-html
<div>{{ count }}</div>
```

Обратите внимание, что нам **не** нужно было добавлять `.value` при использовании ссылки в шаблоне. Для удобства ссылки автоматически разворачиваются при использовании внутри шаблонов (с некоторыми [предостережениями](#caveat-when-unwrapping-in-templates)).

Вы также можете изменить ссылку непосредственно в обработчиках событий:

```vue-html{1}
<button @click="count++">
  {{ count }}
</button>
```

Для более сложной логики мы можем объявить функции, которые изменяют ссылки в той же области видимости, и предоставлять их как методы вместе с состоянием:

```js{7-10,15}
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      // .value is needed in JavaScript
      count.value++
    }

    // don't forget to expose the function as well.
    return {
      count,
      increment
    }
  }
}
```

Затем открытые методы можно использовать в качестве обработчиков событий:

```vue-html{1}
<button @click="increment">
  {{ count }}
</button>
```

Вот пример на [Codepen](https://codepen.io/vuejs-examples/pen/WNYbaqo) без использования каких-либо инструментов сборки.

### `<script setup>` \*\* {#script-setup}

Ручное раскрытие состояния и методов через `setup()` может быть многословным. К счастью, этого можно избежать при использовании [однофайловых компонентов (SFC)](/guide/scaling-up/sfc). Мы можем упростить использование с помощью `<script setup>`:

```vue{1}
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNo9jUEKgzAQRa8yZKMiaNcllvYe2dgwQqiZhDhxE3L3jrW4/DPvv1/UK8Zhz6juSm82uciwIef4MOR8DImhQMIFKiwpeGgEbQwZsoE2BhsyMUwH0d66475ksuwCgSOb0CNx20ExBCc77POase8NVUN6PBdlSwKjj+vMKAlAvzOzWJ52dfYzGXXpjPoBAKX856uopDGeFfnq8XKp+gWq4FAi)

Импорты верхнего уровня, переменные и функции, объявленные в `<script seup>`, автоматически могут использоваться в шаблоне того же компонента. Думайте о шаблоне как о функции JavaScript, объявленной в той же области видимости — естественно, она имеет доступ ко всему, что объявлено вместе с ней.

:::tip
В остальной части руководства мы будем в основном использовать синтаксис SFC + `<script setup>` для примеров кода Composition API, так как это наиболее распространенное использование для разработчиков Vue.

Если вы не используете SFC, вы все равно можете использовать Composition API с опцией [`setup()`](/api/composition-api-setup).
:::

### Почему Refs? \*\* {#why-refs}

Вам может быть интересно, почему нам нужны ссылки с `.value` вместо простых переменных. Чтобы объяснить это, нам нужно будет кратко обсудить, как работает система реактивности Vue.

Когда вы используете ссылку в шаблоне и позже измените значение ссылки, Vue автоматически обнаружит изменение и соответствующим образом обновит DOM. Это стало возможным благодаря системе реактивности, основанной на отслеживании зависимостей. Когда компонент визуализируется в первый раз, Vue **отслеживает** каждую ссылку, которая использовалась во время рендеринга. Позже, когда ссылка будет видоизменена, она **запустит** повторную визуализацию для компонентов, которые ее отслеживают.

В стандартном JavaScript нет способа обнаружить доступ или изменение простых переменных. Но мы можем перехватить операции получения и установки свойства.

Свойство `.value` дает Vue возможность определить, когда ссылка была доступна или изменена. Под капотом Vue выполняет отслеживание в своем геттере и выполняет запуск в своем сеттере. Концептуально вы можете думать о ref как об объекте, который выглядит так:

```js
// pseudo code, not actual implementation
const myRef = {
  _value: 0,
  get value() {
    track()
    return this._value
  },
  set value(newValue) {
    this._value = newValue
    trigger()
  }
}
```

Еще одна приятная черта ссылок заключается в том, что в отличие от обычных переменных, вы можете передавать ссылки в функции, сохраняя при этом доступ к последнему значению и соединению реактивности. Это особенно полезно при рефакторинге сложной логики в повторно используемый код.

Система реактивности обсуждается более подробно в разделе [Реактивность в деталях](/guide/extras/reactivity-in-depth).
</div>

<div class="options-api">

## Объявление методов \* {#declaring-methods}

<VueSchoolLink href="https://vueschool.io/lessons/methods-in-vue-3" title="Free Vue.js Methods Lesson"/>

Чтобы добавить методы к экземпляру компонента, мы используем опцию `methods`. Это должен быть объект, содержащий нужные методы:

```js{7-11}
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  mounted() {
    // methods can be called in lifecycle hooks, or other methods!
    this.increment()
  }
}
```

Vue автоматически привязывает значение this к методам, чтобы оно всегда относилось к экземпляру компонента. Это гарантирует, что метод сохранит правильное значение this, если он используется в качестве прослушивателя событий или обратного вызова. Вам следует избегать использования стрелочных функций при определении `методов`, так как это не позволяет Vue привязать соответствующее значение `this`:

```js
export default {
  methods: {
    increment: () => {
      // BAD: no `this` access here!
    }
  }
}
```

Как и все другие свойства экземпляра компонента, «методы» доступны из шаблона компонента. Внутри шаблона они чаще всего используются в качестве прослушивателей событий:

```vue-html
<button @click="increment">{{ count }}</button>
```

[Try it in the Playground](https://play.vuejs.org/#eNplj9EKwyAMRX8l+LSx0e65uLL9hy+dZlTWqtg4BuK/z1baDgZicsPJgUR2d656B2QN45P02lErDH6c9QQKn10YCKIwAKqj7nAsPYBHCt6sCUDaYKiBS8lpLuk8/yNSb9XUrKg20uOIhnYXAPV6qhbF6fRvmOeodn6hfzwLKkx+vN5OyIFwdENHmBMAfwQia+AmBy1fV8E2gWBtjOUASInXBcxLvN4MLH0BCe1i4Q==)

В приведенном выше примере метод `increment` будет вызываться при нажатии кнопки `<button>`.

</div>

### Глубокая реактивность {#deep-reactivity}

<div class="options-api">

В Vue состояние по умолчанию глубоко реактивно. Это означает, что вы можете ожидать обнаружения изменений даже при изменении вложенных объектов или массивов:

```js
export default {
  data() {
    return {
      obj: {
        nested: { count: 0 },
        arr: ['foo', 'bar']
      }
    }
  },
  methods: {
    mutateDeeply() {
      // these will work as expected.
      this.obj.nested.count++
      this.obj.arr.push('baz')
    }
  }
}
```

</div>

<div class="composition-api">

Ссылки могут содержать значения любого типа, включая глубоко вложенные объекты, массивы или встроенные структуры данных JavaScript, такие как Map.

`Ref` сделает свое значение глубоко реактивным. Это означает, что вы можете ожидать обнаружения изменений даже при изменении вложенных объектов или массивов:

```js
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // these will work as expected.
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

Непримитивные значения превращаются в реактивные прокси через [`reactive()`](#reactive), который обсуждается ниже.

Также можно отказаться от глубокой реактивности с помощью [shallow refs](/api/reactivity-advanced#shallowref). Для неглубоких ссылок отслеживается только доступ к `.value` на предмет реактивности. Неглубокие ссылки можно использовать для оптимизации производительности, избегая затрат на наблюдение за большими объектами, или в случаях, когда внутренним состоянием управляет внешняя библиотека.
Дальнейшее чтение:

- [Уменьшить накладные расходы на реактивность для больших неизменяемых структур](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
- [Интеграция с системами внешнего состояния](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

</div>

### Время обновления DOM {#dom-update-timing}

Когда вы изменяете реактивное состояние, DOM обновляется автоматически. Однако следует отметить, что обновления DOM не применяются синхронно. Вместо этого Vue буферизует их до «следующего тика» в цикле обновления, чтобы гарантировать, что каждый компонент обновляется только один раз, независимо от того, сколько изменений состояния вы сделали.

Чтобы дождаться завершения обновления DOM после изменения состояния, вы можете использовать глобальный API [nextTick()](/api/general#nexttick):

<div class="composition-api">

```js
import { nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // Now the DOM is updated
}
```

</div>
<div class="options-api">

```js
import { nextTick } from 'vue'

export default {
  methods: {
    async increment() {
      this.count++
      await nextTick()
      // Now the DOM is updated
    }
  }
}
```

</div>

<div class="composition-api">

## `reactive()` \*\* {#reactive}

Есть еще один способ объявить реактивное состояние с помощью API `reactive()`. В отличие от ref, который заключает внутреннее значение в специальный объект, `reactive()` делает сам объект реактивным:

```js
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```

> See also: [Typing Reactive](/guide/typescript/composition-api#typing-reactive) <sup class="vt-badge ts" />

Usage in template:

```vue-html
<button @click="state.count++">
  {{ state.count }}
</button>
```

Реактивные объекты — это [прокси-серверы JavaScript] (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) и ведут себя как обычные объекты. Разница в том, что Vue может перехватывать доступ и изменение всех свойств реактивного объекта для отслеживания и запуска реактивности.

`reactive()` глубоко преобразует объект: вложенные объекты также оборачиваются `reactive()` при доступе. Он также вызывается `ref()` внутри, когда значение ref является объектом. Подобно поверхностным ссылкам, существует API [`shallowReactive()`](/api/reactivity-advanced#shallowreactive) для отказа от глубокой реактивности.

### Реактивный прокси против оригинального \*\* {#reactive-proxy-vs-original-1}

Важно отметить, что возвращаемое значение из `reactive()` [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) исходного объекта, который не равен исходному объекту:

```js
const raw = {}
const proxy = reactive(raw)

// proxy is NOT equal to the original.
console.log(proxy === raw) // false
```

Только прокси является реактивным — изменение исходного объекта не приведет к обновлению. Поэтому при работе с системой реактивности Vue лучше всего **использовать исключительно проксированные версии вашего состояния**.

Чтобы обеспечить постоянный доступ к прокси, вызов `reactive()` для одного и того же объекта всегда возвращает один и тот же прокси, а вызов `reactive()` для существующего прокси также возвращает тот же самый прокси:

```js
// calling reactive() on the same object returns the same proxy
console.log(reactive(raw) === proxy) // true

// calling reactive() on a proxy returns itself
console.log(reactive(proxy) === proxy) // true
```

Это правило относится и к вложенным объектам. Из-за глубокой реактивности вложенные объекты внутри реактивного объекта также являются прокси:

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### Ограничения `reactive()` \*\* {#limitations-of-reactive}

API `reactive()` имеет несколько ограничений:

1. **Ограниченные типы значений:** работает только для типов объектов (объекты, массивы и [типы коллекций](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects #keyed_collections), такие как `Map` и `Set`). Он не может содержать [примитивные типы](https://developer.mozilla.org/en-US/docs/Glossary/Primitive), такие как `string`, `number` или `boolean`.

2. **Невозможно заменить весь объект:** поскольку отслеживание реактивности Vue работает над доступом к свойствам, мы всегда должны сохранять одну и ту же ссылку на реактивный объект. Это означает, что мы не можем легко «заменить» реактивный объект, потому что связь реактивности с первой ссылкой потеряна:

   ```js
   let state = reactive({ count: 0 })

   // указанная выше ссылка ({ count: 0 }) больше не отслеживается
   // (связь реактивности потеряна!)
   state = reactive({ count: 1 })
   ```

3. **Не деструктурировать:** когда мы деструктурируем свойство реактивного объекта в локальные переменные или когда мы передаем это свойство в функцию, мы теряем связь с реактивностью:

   ```js
   const state = reactive({ count: 0 })

   // count отключается от state.count при деструктуризации.
   let { count } = state
   // does not affect original state
   count++

   // функция получает простое число и
   // не сможет отслеживать изменения в state.count
   // мы должны передать весь объект, чтобы сохранить реактивность
   callSomeFunction(state.count)
   ```

Из-за этих ограничений мы рекомендуем использовать `ref()` в качестве основного API для объявления реактивного состояния.

## Дополнительные детали распаковки ссылки \*\* {#additional-ref-unwrapping-details}

### Как свойство реактивного объекта \*\* {#ref-unwrapping-as-reactive-object-property}

`Ref` автоматически разворачивается при доступе или изменении как свойство реактивного объекта. Другими словами, оно ведет себя как обычное свойство:

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

Если новый `ref` назначен свойству, связанному с существующим `ref`, он заменит старый `ref`:

```js
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// оригинальный ref теперь отключен от state.count
console.log(count.value) // 1
```

Развертка `Ref` происходит только тогда, когда она вложена внутрь глубокого реактивного объекта. Он не применяется, когда к нему обращаются как к свойству [поверхностного реактивного объекта](/api/reactivity-advanced#shallowreactive).

### Предостережение в массивах и коллекциях \*\* {#caveat-in-arrays-and-collections}

В отличие от реактивных объектов, при доступе к `ref` как к элементу реактивного массива или собственного типа коллекции, например `Map`, разворачивание ** не выполняется**:

```js
const books = reactive([ref('Vue 3 Guide')])
// нужно .value здесь
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// нужно .value здесь
console.log(map.get('count').value)
```

### Предостережение при развертывании в шаблонах \*\* {#caveat-when-unwrapping-in-templates}

Развертка `Ref` в шаблонах применяется только в том случае, если `ref` является свойством верхнего уровня в контексте рендеринга шаблона.

В приведенном ниже примере `count` и `object` являются свойствами верхнего уровня, а `object.id` — нет:

```js
const count = ref(0)
const object = { id: ref(0) }
```

Следовательно, это выражение работает так, как ожидалось:

```vue-html
{{ count + 1 }}
```

... в то время как этот делает **НЕ**:

```vue-html
{{ object.id + 1 }}
```

Результатом рендеринга будет `[object Object]1`, потому что `object.id` не разворачивается при оценке выражения и остается объектом ref. Чтобы исправить это, мы можем деструктурировать `id` в свойство верхнего уровня:

```js
const { id } = object
```

```vue-html
{{ id + 1 }}
```

Теперь результат рендеринга будет «2».

Еще одна вещь, которую следует отметить, это то, что ref разворачивается, если он является окончательным оцененным значением текстовой интерполяции (т. е. тегом <code v-pre>{{ }}</code>), поэтому следующее будет отображать `1` :

```vue-html
{{ object.id }}
```

Это просто удобная функция интерполяции текста, эквивалентная <code v-pre>{{ object.id.value }}</code>.

</div>

<div class="options-api">

### Методы с отслеживанием состояния \* {#stateful-methods}

В некоторых случаях может потребоваться динамическое создание функции метода, например, создание обработчика события debounce:

```js
import { debounce } from 'lodash-es'

export default {
  methods: {
    // Debouncing with Lodash
    click: debounce(function () {
      // ... реагировать на клик ...
    }, 500)
  }
}
```

Однако этот подход проблематичен для компонентов, которые повторно используются, потому что функция, для которой отменено дребезг, является **с сохранением состояния**: она поддерживает некоторое внутреннее состояние в течение прошедшего времени. Если несколько экземпляров компонентов совместно используют одну и ту же функцию устранения дребезга, они будут мешать друг другу.

Чтобы функция debounce для каждого экземпляра компонента оставалась независимой от других, мы можем создать версию debounce в хуке жизненного цикла «created»:

```js
export default {
  created() {
    // каждый экземпляр теперь имеет свою собственную копию обработчика debounced
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // также хорошая идея, чтобы отменить таймер
    // когда компонент удален
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... реагировать на клик ...
    }
  }
}
```

</div>
