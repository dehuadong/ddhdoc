# 响应式代理 vs 原始对象

> 在 Vue 3 的 Composition API 中，reactive() 函数用于创建响应式对象。当你使用 reactive() 封装一个原始对象时，它会返回一个响应式代理对象。这个响应式代理对象与原始对象在许多方面有相似之处，但它们之间还存在一些关键差异。

1、响应式代理 vs. 原始对象：

* 响应式代理：reactive() 返回的对象会自动追踪其依赖关系，并在数据更改时触发更新。当你访问或修改响应式代理对象的属性时，Vue 能够识别这些操作并确保视图或计算属性等自动更新。
* 原始对象：原始对象不具有响应式特性，它们不会自动追踪依赖关系或触发更新

2、嵌套对象：

* 响应式代理：当你访问 reactive() 返回的响应式代理对象中的[嵌套对象](./nested_object.md)时，这些嵌套对象也将自动转换为响应式代理。这意味着整个对象树都将变为响应式，你可以在任何层级上访问和修改数据，同时保持响应式特性。
* 原始对象：原始对象中的嵌套对象不具有响应式特性，因此对嵌套对象的更改不会自动触发更新。

3、比较和相等性检查：

* 响应式代理：由于 reactive() 返回的是一个代理对象，而不是原始对象本身，当你尝试将响应式代理与原始对象进行[比较或相等性检查](./comparison_and_equality_check.md)时，它们会被认为是不同的对象。你可以使用 Vue 3 提供的 toRaw() 函数将响应式代理转换回原始对象进行比较。

* 原始对象：原始对象在比较和相等性检查时不会产生类似代理对象的问题，因为它们本身就是实际的对象。

4、代理对象的陷阱和限制：

* 响应式代理：由于 reactive() 返回的代理对象是基于 ES6 Proxy 实现的，它可能在某些情况下有一些陷阱和限制。例如，对于某些特定的对象（如 Date、Map、Set 等），代理对象可能无法完全模拟原始对象的行为。另外，Proxy 在某些旧版浏览器中可能不被支持或存在兼容性问题。
* 原始对象：原始对象不受这些陷阱和限制的影响，因为它们本身就是实际的对象。然而，由于原始对象不具备响应式特性，它们不能在 Vue 应用程序中自动追踪依赖关系并触发更新。

5、显式反应和计算属性：

* 响应式代理：当你需要创建显式反应或计算属性时，响应式代理对象可以与 watch()、watchEffect() 和 computed() 等 Vue 3 API 一起使用，以便更好地管理依赖关系和更新。
* 原始对象：原始对象不具备响应式特性，因此不能与这些 Vue API 直接配合使用。要使原始对象具备响应式特性，你需要将它们封装为响应式代理对象（如使用 reactive() 或 ref()）。

总之，reactive() 创建的响应式代理对象与原始对象在许多方面具有相似之处

## 限制和注意事项

1. 仅对对象类型有效：reactive() 函数仅对对象类型（如对象、数组以及 Map 和 Set 等集合类型）有效。对于原始类型（如 string、number 和 boolean），你需要使用 ref() 函数来创建响应式数据。
2. 保持响应式对象的相同引用：Vue 的响应式系统通过属性访问进行追踪。因此，在处理响应式对象时，始终需要保持对该对象的相同引用。以下是一些可能导致响应性丢失的情况：

* 替换响应式对象：如果你直接替换了一个响应式对象，原始响应式对象的引用将丢失，导致响应性断开。例如：

> ```js
> const state = reactive({ count: 0 });
> state = reactive({ count: 1 }); // 错误！这将导致响应性丢失。
> ```

* 将响应式对象的属性赋值或解构到本地变量：如果你将响应式对象的属性赋值给一个本地变量或使用解构语法，原始响应式对象的引用将丢失，导致响应性断开。例如：

> ```js
> const state = reactive({ count: 0 });
> const count = state.count; // 响应性丢失，count 不再是响应式的。
> ```

* 将响应式对象的属性传入一个函数：如果你将响应式对象的属性作为参数传递给一个函数，响应性同样会丢失。例如：

> ```js
> const state = reactive({ count: 0 });
> function updateCounter(counter) {
> counter++; // 响应性丢失，counter 不再是响应式的。
> }
> updateCounter(state.count);
> ```

> 为了解决这些问题，你可以使用 Vue 提供的 ref()、toRef() 和 toRefs() 等 API 来确保在操作响应式数据时保持响应性。这些 API 可以帮助你在处理响应式对象时保持对它们的引用，从而确保 Vue 能够正确追踪依赖关系并在数据更改时触发更新。

> 以下是一些使用 Vue 提供的 API 解决响应性丢失问题的例子：

1、使用 ref() 为原始类型创建响应式数据：

```js
const count = ref(0); // 使用 ref() 为原始类型（如数字）创建响应式数据
```

2、使用 toRef() 创建一个响应式对象属性的引用：

```js
const state = reactive({ count: 0 });
const countRef = toRef(state, 'count'); // 创建响应式对象属性 count 的引用

function updateCounter(counterRef) {
counterRef.value++; // 通过引用操作属性，保持响应性
}

updateCounter(countRef); // 将响应式引用传递给函数，保持响应性
```

3、使用 toRefs() 将响应式对象的所有属性转换为响应式引用：

```js
const state = reactive({ count: 0, message: 'Hello' });
const stateRefs = toRefs(state); // 将响应式对象的所有属性转换为响应式引用

// 现在可以安全地解构和操作响应式引用
const { count, message } = stateRefs;

function updateCounterAndMessage(counterRef, messageRef) {
counterRef.value++;
messageRef.value = 'Updated';
}

updateCounterAndMessage(count, message); // 将响应式引用传递给函数，保持响应性
```

通过使用 ref()、toRef() 和 toRefs() 等 Vue API，你可以确保在操作响应式数据时保持响应性。这些 API 可以帮助你在处理响应式对象时保持对它们的引用，从而确保 Vue 能够正确追踪依赖关系并在数据更改时触发更新。
