# `toRefs()` 和 `toRef()`

`toRefs()` 和 `toRef()` 都是 Vue 3 Composition API 中的函数，它们用于从 `reactive` 创建的响应式对象中提取属性，同时保持响应式特性。尽管它们的功能相似，但它们之间有一些关键区别。

## toRefs():

`toRefs` 是 Vue 3 Composition API 中的一个函数，用于将 `reactive` 创建的响应式对象转换为具有相同属性的普通对象，同时保持响应式特性。这在解构和展开 `reactive` 对象时非常有用，因为直接解构 `reactive` 对象会导致响应式特性丢失。

`toRefs` 的工作原理是将 `reactive` 对象的每个属性转换为一个 `ref` 对象。这意味着，在使用 `toRefs` 转换后的对象中，每个属性都需要使用 `.value` 访问和修改。

**示例：**

```javascript
import { reactive, toRefs } from 'vue';

export default {
  setup() {
    const state = reactive({
      count: 0,
      message: 'Hello, Vue 3!',
    });

    // 使用 toRefs 转换为具有相同属性的普通对象
    const stateRefs = toRefs(state);

    function increment() {
      stateRefs.count.value++; // 注意：使用 .value 访问和修改属性
    }

    // 返回 toRefs 转换后的对象，以便在模板中使用
    return {
      ...stateRefs,
      increment,
    };
  },
};
```

在模板中，我们可以像使用普通对象一样解构和展开 `stateRefs` 对象，同时保留其响应式特性：

```html
<template>
  <p>Count: {{ count }}</p> <!-- 注意：自动解包，无需使用 count.value -->
  <p>Message: {{ message }}</p>
  <button @click="increment">Increment Count</button>
</template>
```

通过使用 `toRefs`，我们可以解决直接解构 `reactive` 对象导致的响应式特性丢失问题。当你需要在组件中解构和展开 `reactive` 对象时，`toRefs` 是一个非常有用的工具。

### toRefs()使用场景

1. 解构响应式对象：当你需要将响应式对象的属性分解为独立的变量时，可以使用 `toRefs()`。这样，在使用解构后的变量时，它们依然保持响应式特性。
2. 向子组件传递响应式对象的属性：当将响应式对象的属性传递给子组件时，可以使用 `toRefs()` 转换为 `ref` 对象。这样，在子组件中使用这些属性时，它们将保持响应式特性。
   **举例：**

```javascript
// ParentComponent.vue
import { reactive, toRefs } from 'vue';
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    ChildComponent,
  },
  setup() {
    const state = reactive({
      count: 0,
      message: 'Hello, Vue 3!',
    });

    const stateRefs = toRefs(state);

    return {
      ...stateRefs,
    };
  },
};
```

```html
<!-- ParentComponent.vue -->
<template>
  <div>
    <child-component :count="count" :message="message" />
  </div>
</template>
```

3. 在组合式函数中返回响应式对象：当你将响应式对象从一个组合式函数返回给其他组件时，可以使用 `toRefs()` 将其转换为普通对象。这样，你可以更方便地在其他组件中使用这些属性，同时保持它们的响应式特性。

```javascript
// useCounter.js
import { reactive, toRefs } from 'vue';

export default function useCounter() {
  const state = reactive({
    count: 0,
  });

  function increment() {
    state.count++;
  }

  function decrement() {
    state.count--;
  }

  return {
    ...toRefs(state),
    increment,
    decrement,
  };
}
```

```js
// MyComponent.vue
import useCounter from './useCounter';

export default {
  setup() {
    const counter = useCounter();
    return {
      ...counter,
    };
  },
};
```

## toRef()

`toRef()` 函数用于从一个响应式对象（由 `reactive()` 创建）中提取单个属性，并将该属性转换为一个 `ref` 对象。这样，你可以将响应式对象的某个属性单独提取出来，同时保持响应式特性。

**示例：**

```js
import { reactive, toRef } from 'vue';

export default {
  setup() {
    const state = reactive({
      count: 0,
      message: 'Hello, Vue 3!',
    });

    const countRef = toRef(state, 'count'); // 从响应式对象中提取 count 属性作为 ref 对象

    function increment() {
      countRef.value++; // 使用 .value 访问和修改 ref 对象的值
    }

    return {
      count: countRef,
      message: state.message,
      increment,
    };
  },
};
```

在这个示例中，我们首先使用 `reactive()` 函数创建了一个响应式对象 `state`，该对象包含两个属性：`count` 和 `message`。然后，我们使用 `toRef()` 函数从响应式对象 `state` 中提取 `count` 属性，并将其转换为 `ref` 对象 `countRef`。这样，我们可以单独使用 `countRef`，同时保持响应式特性。

在模板中，你可以直接使用提取的 `countRef`（会自动解包）：

```html
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>
```

### toRef()使用场景

1. 单独提取响应式对象的属性：当你只需要从响应式对象中提取一个或少数几个属性时，可以使用 `toRef()`。这样，你可以将提取的属性单独使用，同时保持响应式特性。
2. 向子组件传递响应式对象的单个属性：当将响应式对象的某个属性传递给子组件时，可以使用 `toRef()` 将该属性提取为 `ref` 对象。这样，在子组件中使用这个属性时，它将保持响应式特性。
   举例：

```js
// ParentComponent.vue
import { reactive, toRef } from 'vue';
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    ChildComponent,
  },
  setup() {
    const state = reactive({
      count: 0,
      message: 'Hello, Vue 3!',
    });

    const countRef = toRef(state, 'count');

    return {
      count: countRef,
    };
  },
};
```

```html
<!-- ParentComponent.vue -->
<template>
  <div>
    <child-component :count="count" />
  </div>
</template>
```

3. 在组合式函数中返回响应式对象的单个属性：当你需要将响应式对象的某个属性从一个组合式函数返回给其他组件时，可以使用 `toRef()` 将该属性提取为 `ref` 对象。这样，你可以将提取的属性单独使用，同时保持响应式特性。

```js
// useMessage.js
import { reactive, toRef } from 'vue';

export default function useMessage() {
  const state = reactive({
    message: 'Hello, Vue 3!',
  });

  const messageRef = toRef(state, 'message');

  return {
    message: messageRef,
  };
}
```

```js
// MyComponent.vue
import useMessage from './useMessage';

export default {
  setup() {
    const { message } = useMessage();
    return {
      message,
    };
  },
};
```
