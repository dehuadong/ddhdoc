# `watch` 和 `watchEffect`函数

## `watch` 函数的详细介绍：

> `watch` 函数是 Vue 3 Composition API 中用于观察和响应响应式数据源变化的方法。它允许您监视特定的响应式属性（如 refs 或响应式对象）并在监视的属性发生变化时执行回调函数。回调函数接收被监视属性的新值和旧值，使您能够根据旧值和新值之间的差异执行副作用或操作。

1. 响应式源：使用 `watch` 时，您需要明确指定要观察的响应式属性或 refs。您可以提供单个响应式源或一组源。响应式源可以是 **refs、计算属性或响应式对象**的属性。
2. 回调函数：当被监视的属性或属性发生变化时，回调函数会被执行。它接收两个参数：被监视属性的新值和旧值。这使您能够根据旧值和新值之间的差异执行副作用或操作。回调函数也被称为 "watcher"。
3. 清理函数：`watch` 函数返回一个停止句柄，这是一个函数，您可以调用它来停止观察响应式源。当您想要手动停止 watcher 时，这非常有用，例如，当组件被销毁或者您想要取消 watcher 的订阅时。

以下是使用 `watch` 函数的示例：

```javascript
import { ref, watch } from 'vue';

export default {
  setup() {
    const count = ref(0);
    const message = ref('');

    const stopWatch = watch(count, (newValue, oldValue) => {
      message.value = `计数从 ${oldValue} 变为 ${newValue}`;
    });

    function increment() {
      count.value++;
    }

    function stop() {
      stopWatch();
    }

    return {
      count,
      message,
      increment,
      stop,
    };
  },
};
```

在此示例中，`watch` 函数用于观察 `count` ref。当 `count` 发生变化时，watcher 更新 `message` ref 以反映更改。`stop` 函数允许您通过调用 `watch` 函数返回的停止句柄来停止观察 `count` ref。

总之，Vue 3 Composition API 中的 `watch` 函数使您能够观察特定的响应式属性，并在被监视的属性发生变化时执行回调函数。回调函数接收被监视属性的新值和旧值，使您能够根据它们之间的差异执行副作用或操作。当您需要更多地控制副作用并观察特定的响应式源时，`watch` 函数尤其有用。

> 当您需要更复杂的监视和响应场景时，`watch` 函数也可以监视多个响应式源。通过向 `watch` 函数提供一个响应式源数组，您可以观察和处理多个源的更改。在这种情况下，回调函数将接收两个数组，一个包含新值，另一个包含旧值。

以下是一个监视多个响应式源的示例：

```javascript
import { ref, reactive, watch } from 'vue';

export default {
  setup() {
    const count = ref(0);
    const state = reactive({
      value: 10,
    });

    watch(
      [count, () => state.value],
      ([newCount, newValue], [oldCount, oldValue]) => {
        console.log(`计数从 ${oldCount} 变为 ${newCount}`);
        console.log(`值从 ${oldValue} 变为 ${newValue}`);
      }
    );

    function incrementCount() {
      count.value++;
    }

    function incrementValue() {
      state.value++;
    }

    return {
      count,
      state,
      incrementCount,
      incrementValue,
    };
  },
};
```

在此示例中，`watch` 函数用于监视 `count` ref 和 `state.value` 响应式对象属性。当任何一个值发生变化时，watcher 都会输出新值和旧值的日志。这使您能够在多个值之间建立关联，并在它们发生变化时执行相应的操作。

`watch` 函数在 Vue 3 Composition API 中提供了灵活且强大的响应式监视功能。它允许您监视一个或多个响应式源，并在被监视的属性发生变化时执行回调函数。通过提供新值和旧值，您可以根据它们之间的差异执行副作用或操作。这使您能够轻松管理复杂的应用程序状态并实现高效的数据驱动 UI 更新。

> 在实际应用中，`watch` 函数可以与其他 Vue Composition API 功能结合使用，例如计算属性、响应式对象、生命周期钩子等，来实现更多功能和更好的代码组织。

以下是一个包含计算属性和生命周期钩子的示例：

```javascript
import { ref, computed, watch, onMounted } from 'vue';

export default {
  setup() {
    const count = ref(0);
    const state = reactive({
      value: 10,
    });
    const total = computed(() => count.value + state.value);

    watch(
      total,
      (newValue, oldValue) => {
        console.log(`总数从 ${oldValue} 变为 ${newValue}`);
      }
    );

    onMounted(() => {
      console.log('组件已挂载');
    });

    function incrementCount() {
      count.value++;
    }

    function incrementValue() {
      state.value++;
    }

    return {
      count,
      state,
      total,
      incrementCount,
      incrementValue,
    };
  },
};
```

在此示例中，我们添加了一个计算属性 `total`，它将 `count` 和 `state.value` 相加。`watch` 函数监视计算属性 `total`，当 `total` 发生变化时输出新值和旧值的日志。

此外，我们还添加了一个 `onMounted` 生命周期钩子，它在组件挂载时输出一条消息。这种方式可以让您在特定的生命周期阶段执行代码，例如在组件挂载后获取数据、设置事件监听器等。

## watch() 的高级用法

`watch` 函数的高级用法允许您更灵活地处理响应式数据的变化。以下是一些高级用法示例：

1. 深度观察响应式对象：

默认情况下，`watch` 只观察响应式对象的顶层属性。要监视响应式对象的嵌套属性，可以使用 `deep` 选项。

```javascript
import { reactive, watch } from 'vue';

export default {
  setup() {
    const state = reactive({
      user: {
        name: 'John',
        age: 30,
      },
    });

    watch(
      () => state.user,
      (newValue, oldValue) => {
        console.log('User object changed:', newValue);
      },
      { deep: true }
    );

    return {
      state,
    };
  },
};
```

2. 使用 `flush` 选项：

`flush` 选项控制回调函数何时执行。它可以是 `pre`（在 DOM 更新之前执行）、`post`（在 DOM 更新之后执行）或 `sync`（同步执行）。默认情况下，`flush` 为 `post`。

```javascript
import { ref, watch } from 'vue';

export default {
  setup() {
    const count = ref(0);

    watch(
      count,
      (newValue, oldValue) => {
        console.log(`计数从 ${oldValue} 变为 ${newValue}`);
      },
      { flush: 'pre' }
    );

    return {
      count,
    };
  },
};
```

3. 立即执行回调函数：

默认情况下，`watch` 回调函数仅在侦听的源发生更改时才执行。要在初始设置时立即执行回调函数，可以使用 `immediate` 选项。

```javascript
import { ref, watch } from 'vue';

export default {
  setup() {
    const count = ref(0);

    watch(
      count,
      (newValue, oldValue) => {
        console.log(`计数从 ${oldValue} 变为 ${newValue}`);
      },
      { immediate: true }
    );

    return {
      count,
    };
  },
};
```

这些高级用法使您能够更好地控制 `watch` 函数的行为，并根据需要灵活地处理响应式数据变化。通过结合这些高级选项，您可以优化组件的性能和用户体验，确保数据驱动的 UI 更新准确且及时。

## watchEffect()函数的详细介绍

`watchEffect` 是 Vue 3 Composition API 中的一个实用函数，它用于自动监视响应式源，并在任何收集到的响应式源发生变化时执行回调函数。与 `watch` 函数不同，`watchEffect` 不需要明确指定要观察的响应式源。在回调函数内部访问的响应式源会自动被监视。

1. 自动收集响应式源：`watchEffect` 函数接收一个回调函数作为参数。在回调函数内部，您可以访问任意数量的响应式源，如 **refs、computed properties 或响应式对象的属性**。`watchEffect` 会自动收集您访问的所有响应式源。
2. 监视和执行：当收集到的响应式源中的任何一个发生变化时，`watchEffect` 的回调函数将被执行。这使您能够随时了解响应式源的最新状态，并在需要时执行副作用或操作。
3. 清理函数：`watchEffect` 函数返回一个停止句柄，这是一个函数，您可以调用它来停止观察收集到的响应式源。当您想要手动停止监视时，例如在组件被销毁或者您想要取消监视的订阅时，这非常有用。

以下是使用 `watchEffect` 函数的示例：

```javascript
import { ref, watchEffect } from 'vue';

export default {
  setup() {
    const count = ref(0);
    const message = ref('');

    const stopWatchEffect = watchEffect(() => {
      message.value = `计数为 ${count.value}`;
    });

    function increment() {
      count.value++;
    }

    function stop() {
      stopWatchEffect();
    }

    return {
      count,
      message,
      increment,
      stop,
    };
  },
};
```

在此示例中，`watchEffect` 函数用于自动收集和监视 `count` ref。当 `count` 发生变化时，watchEffect 更新 `message` ref 以反映更改。`stop` 函数允许您通过调用 `watchEffect` 函数返回的停止句柄来停止观察 `count` ref。

总之，`watchEffect` 是一个强大的 Vue 3 Composition API 函数，允许您自动监视和响应响应式源的变化。在回调函数内部访问的响应式源会自动被监视，无需明确指定要观察的源。这使得您可以轻松地处理响应式数据变化，并在需要时执行副作用或操作。

> `watchEffect` 与其他 Composition API 功能结合使用的一些建议：

1. 结合计算属性：您可以将 `watchEffect` 与计算属性一起使用，以便在计算属性发生更改时执行操作。这使您可以在计算属性内部处理数据逻辑，并通过 `watchEffect` 在需要时执行副作用。

```javascript
import { ref, computed, watchEffect } from 'vue';

export default {
  setup() {
    const count = ref(0);
    const doubledCount = computed(() => count.value * 2);

    watchEffect(() => {
      console.log(`双倍计数为 ${doubledCount.value}`);
    });

    function increment() {
      count.value++;
    }

    return {
      count,
      increment,
    };
  },
};
```

2. 结合生命周期钩子：您可以将 `watchEffect` 与生命周期钩子一起使用，以在特定生命周期阶段执行操作。例如，您可以在 `onMounted` 钩子中启动 `watchEffect`，并在 `onUnmounted` 钩子中停止观察。

```javascript
import { ref, watchEffect, onMounted, onUnmounted } from 'vue';

export default {
  setup() {
    const count = ref(0);
    let stopWatchEffect;

    onMounted(() => {
      stopWatchEffect = watchEffect(() => {
        console.log(`计数为 ${count.value}`);
      });
    });

    onUnmounted(() => {
      stopWatchEffect();
    });

    function increment() {
      count.value++;
    }

    return {
      count,
      increment,
    };
  },
};
```

3. 结合 `provide` 和 `inject`：当您需要在组件树中共享状态时，可以将 `watchEffect` 与 `provide` 和 `inject` 一起使用。您可以在提供者组件中使用 `watchEffect` 监视共享状态，并在消费者组件中通过 `inject` 注入和使用状态。

这些示例说明了如何将 `watchEffect` 与其他 Vue 3 Composition API 功能结合使用，以实现更复杂和可扩展的应用程序。通过灵活地组合这些功能，您可以更好地管理响应式数据、副作用和组件生命周期，从而实现高性能和易于维护的应用程序。

## watchEffect 函数的高级用法

> `watchEffect` 函数相对简单，但可以与其他 Vue 3 Composition API 功能和概念结合使用，以实现高级用法。以下是一些示例：

1. 使用 `watchEffect` 和计算属性

将 `watchEffect` 与计算属性结合使用，可以在计算属性发生更改时触发回调。这样，您可以在计算属性内处理数据逻辑，并在需要时利用 `watchEffect` 执行副作用。

```javascript
import { ref, computed, watchEffect } from 'vue';

export default {
  setup() {
    const count = ref(0);
    const doubledCount = computed(() => count.value * 2);

    watchEffect(() => {
      console.log(`双倍计数为 ${doubledCount.value}`);
    });

    function increment() {
      count.value++;
    }

    return {
      count,
      increment,
    };
  },
};
```

2. `watchEffect` 和生命周期钩子

`watchEffect` 可以与生命周期钩子（如 `onMounted` 和 `onUnmounted`）结合使用，以便在组件的特定生命周期阶段执行操作。

```javascript
import { ref, watchEffect, onMounted, onUnmounted } from 'vue';

export default {
  setup() {
    const count = ref(0);
    let stopHandle;

    onMounted(() => {
      stopHandle = watchEffect(() => {
        console.log(`计数为 ${count.value}`);
      });
    });

    onUnmounted(() => {
      if (stopHandle) {
        stopHandle();
      }
    });

    function increment() {
      count.value++;
    }

    return {
      count,
      increment,
    };
  },
};
```

在此示例中，在 `onMounted` 钩子中启动 `watchEffect`，并在 `onUnmounted` 钩子中使用停止句柄停止观察。

3. 延迟执行副作用

有时，您可能希望在满足某些条件时才执行副作用。通过将副作用的执行放在 `watchEffect` 回调中的条件语句内，您可以实现此高级用法。

```javascript
import { ref, watchEffect } from 'vue';

export default {
  setup() {
    const count = ref(0);

    watchEffect(() => {
      if (count.value > 10) {
        console.log(`计数超过了 10，当前值为 ${count.value}`);
      }
    });

    function increment() {
      count.value++;
    }

    return {
      count,
      increment,
    };
  },
};
```

在这个示例中，当 `count` 值大于 10 时，副作用（在此示例中为 `console.log`）才会执行。

虽然 `watchEffect` 函数本身相对简单，但与其他 Vue 3 Composition API 功能结合使用时，可以实现更多高级用法。在应用程序中灵活使用 `watchEffect` 可以帮助您更好地处理响应式数据的变化。

> `watchEffect`函数的第二个参数是一个可选对象，用于调整 `watchEffect` 的刷新时机和调试副作用。

它包含以下属性：

1. `flush`：这个选项决定了回调函数的触发时机。默认情况下，副作用将在组件渲染之前执行。设置 `flush: 'post'` 会使副作用延迟到组件渲染之后再执行。如果需要在响应式依赖发生改变时立即触发副作用，可以设置 `flush: 'sync'`。然而，这个设置应谨慎使用，因为如果有多个属性同时更新，这可能会导致性能和数据一致性问题。

```javascript
watchEffect(() => {
  // 副作用函数
}, {
  flush: 'post' // 或 'sync'
});
```

2. `onTrack`：这是一个调试函数，当依赖项被追踪时触发。它接收一个 `DebuggerEvent` 类型的参数，其中包含与追踪事件相关的信息。
3. `onTrigger`：这是另一个调试函数，当依赖项触发副作用重新运行时调用。它也接收一个 `DebuggerEvent` 类型的参数，其中包含与触发事件相关的信息。

```javascript
watchEffect(() => {
  // 副作用函数
}, {
  onTrack(e) {
    // 当依赖项被追踪时执行
    debugger;
  },
  onTrigger(e) {
    // 当依赖项触发副作用重新运行时执行
    debugger;
  }
});
```

## watch 和 watchEffect 之间的主要区别

`watch` 和 `watchEffect` 都是 Vue 3 的 Composition API 的一部分，用于观察和响应响应式数据的变化。尽管它们具有相似的目的，但它们的用途和行为不同。

1. 显式与隐式响应式源：

- `watch`：对于 `watch`，您需要明确指定要观察的响应式属性或引用。您可以提供一个响应式源或一组源。
- `watchEffect`：相反，`watchEffect` 会自动跟踪提供的函数中的所有响应式依赖项。您无需明确指定要观察的属性；`watchEffect` 会为您检测它们。

2. 回调函数：

- `watch`：`watch` 中的回调函数接收两个参数：被观察属性或属性的新值和旧值。这使您可以根据旧值和新值之间的差异执行副作用或操作。
- `watchEffect`：`watchEffect` 函数不向回调函数提供新值和旧值。相反，只要任何响应式依赖项发生变化，它就会重新运行整个函数。

3. 使用场景：

- `watch`：当您需要更多地控制副作用，希望观察特定的响应式源，或需要访问被观察属性的新值和旧值时，使用 `watch`。当您需要根据旧值和新值之间的差异执行操作，或者当您想观察多个源并以特定方式响应变化时，它很有用。
- `watchEffect`：对于不需要知道新旧值或明确指定要观察的响应式属性的简单副作用，使用 `watchEffect`。当您希望在不明确列出它们的情况下自动跟踪和响应函数中的所有响应式依赖项时，它很有用。

总之，`watch` 和 `watchEffect` 之间的主要区别在于它们处理响应式源的方式、提供给回调函数的信息以及它们的使用场景。`watch` 提供更多的控制并明确指定要观察的属性，而 `watchEffect` 自动跟踪所有响应式依赖项，更适合简单的副作用。
