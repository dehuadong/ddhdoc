# ref和 reactive

`ref` 和 `reactive` 都是 Vue 3 Composition API 中用于创建响应式数据的函数。它们有一些区别，具体如下：

## 基本类型和对象

`ref` 用于创建基本类型（如字符串、数字、布尔值等）的响应式数据，同时也可以用于创建响应式对象。`reactive` 仅用于创建响应式对象。

```javascript
import { ref, reactive } from 'vue';

const count = ref(0); // 基本类型
const user = ref({ name: 'John', age: 30 }); // 对象

const userInfo = reactive({ name: 'Jane', age: 28 }); // 对象
```

## 访问和修改值

`ref` 创建的响应式数据通过 `.value` 属性访问和修改。而 `reactive` 创建的响应式对象可以直接访问和修改其属性。

```javascript
// 使用 ref
count.value++; // 修改值
console.log(count.value); // 访问值

// 使用 reactive
userInfo.age++; // 修改值
console.log(userInfo.age); // 访问值
```

## 解构和展开

当你解构或展开 `reactive` 创建的响应式对象时，它会失去响应式特性。但是，`ref` 创建的响应式数据不受此限制。

```javascript
// reactive 的解构问题
const userInfoReactive = reactive({ name: 'Jane', age: 28 });
const { age } = userInfoReactive;
age++; // 不会触发响应式更新

// ref 的解构
const userRef = ref({ name: 'John', age: 30 });
const { value } = userRef;
value.age++; // 仍然触发响应式更新
```

## 模板中的使用

在模板中，`ref` 创建的响应式数据会自动解包，无需使用 `.value` 访问。而 `reactive` 创建的响应式对象可以直接访问其属性。

```html
<template>
  <!-- ref -->
  <p>{{ count }}</p> <!-- 自动解包，无需使用 count.value -->

  <!-- reactive -->
  <p>{{ userInfo.name }}</p>
</template>
```

选择使用 `ref` 还是 `reactive` 取决于具体需求和使用场景。对于基本类型数据，你应该使用 `ref`。对于对象，可以根据实际情况选择 `ref` 或 `reactive`。不过，在某些情况下，你可能需要混合使用它们。例如，当你使用 `reactive` 创建的对象包含 `ref` 创建的响应式数据时，可以利用 `ref` 的自动解包特性。

在一个对象中混合使用 `ref` 和 `reactive`。例如，当你有一个包含多个属性的响应式对象，并且其中一些属性需要单独使用时，可以利用 `ref` 的自动解包特性。下面是一个示例：

```javascript
import { ref, reactive } from 'vue';

export default {
  setup() {
    const username = ref('John');
    const age = ref(30);

    const userInfo = reactive({
      username,
      age,
      address: '123 Main St',
    });

    function incrementAge() {
      age.value++;
    }

    return {
      userInfo,
      incrementAge,
    };
  },
};
```

在这个示例中，我们创建了两个响应式基本类型值 `username` 和 `age`，并将它们作为响应式对象 `userInfo` 的属性。我们可以单独使用 `username` 和 `age`，同时还可以将整个 `userInfo` 对象作为响应式数据使用。

在模板中，我们可以直接访问 `userInfo` 对象的属性，并且 `ref` 创建的属性会自动解包：

```html
<template>
  <p>Username: {{ userInfo.username }}</p> <!-- 自动解包 -->
  <p>Age: {{ userInfo.age }}</p> <!-- 自动解包 -->
  <p>Address: {{ userInfo.address }}</p>
  <button @click="incrementAge">Increment Age</button>
</template>
```

当我们需要更新 `age` 属性时，可以直接修改 `age.value`，这将同时更新 `userInfo` 对象中的 `age` 属性。

这个示例展示了如何在 Vue 3 中混合使用 `ref` 和 `reactive`。通过将 `ref` 创建的响应式数据作为 `reactive` 创建的响应式对象的属性，我们可以轻松地组合和管理这些数据，同时保持响应式特性。

## 对数组的支持

**`ref` 可以用于包装数组，使其具有响应式特性。然后，你可以通过 `.value` 访问和修改数组。**

```javascript
import { ref } from 'vue';

export default {
    setup() {
        const tasksRef = ref([]); // 使用 ref 创建响应式数组

        function addTask(task) {
            tasksRef.value.push(task); // 注意：需要通过 .value 访问和修改数组
        }

        function removeTask(index) {
            tasksRef.value.splice(index, 1); // 注意：需要通过 .value 访问和修改数组
        }

        return {
            tasksRef,
            addTask,
            removeTask,
        };
    },
};
```

在模板中，`ref` 创建的响应式数组会自动解包，无需使用 .value 访问：

```html
<template>
  <ul>
    <li v-for="(task, index) in tasksRef" :key="index">
      {{ task }}
      <button @click="removeTask(index)">Remove</button>
    </li>
  </ul>
</template>
```

**`reactive` 可以用于创建响应式数组，特别是当数组中的元素为基本类型时。对于存储对象的数组，可以使用 `ref` 创建响应式数组以避免递归地将数组中的所有对象转换为响应式，从而节省性能开销。**

```javascript
import { reactive } from 'vue';

export default {
  setup() {
    const tasksReactive = reactive([]); // 使用 reactive 创建响应式数组

    function addTask(task) {
      tasksReactive.push(task); // 直接访问和修改数组，无需 .value
    }

    function removeTask(index) {
      tasksReactive.splice(index, 1); // 直接访问和修改数组，无需 .value
    }

    return {
      tasksReactive,
      addTask,
      removeTask,
    };
  },
};
```

在模板中，直接使用 `reactive` 创建的响应式数组，无需 .value 访问：

```html
<template>
  <ul>
    <li v-for="(task, index) in tasksReactive" :key="index">
      {{ task }}
      <button @click="removeTask(index)">Remove</button>
    </li>
  </ul>
</template>
```

**总结：**

* 使用 `ref` 创建响应式数组时，需要通过 `.value` 访问和修改数组。在模板中，数组会自动解包，无需使用 `.value` 访问。
* 使用 `reactive` 创建响应式数组时，可以直接访问和修改数组，无需使用 `.value`。在模板中也可以直接使用数组。

在实际应用中，可以根据具体需求和场景选择使用 `ref` 或 `reactive` 创建响应式数组。例如，如果数组中包含对象，使用 `ref` 可以避免递归地将数组中的所有对象转换为响应式，从而节省性能开销。而如果数组中的元素是基本类型，可以根据个人喜好和代码风格选择使用 `ref` 或 `reactive`。

## 一个简单的待办事项列表应用

```js
import { ref, reactive } from 'vue';

export default {
  setup() {
    const newTask = ref(''); // 用于输入新任务的 ref
    const tasks = reactive([]); // 用于存储任务的响应式数组

    function addTask() {
      if (newTask.value.trim()) {
        tasks.push(newTask.value); // 将新任务添加到响应式数组中
        newTask.value = ''; // 清空输入框
      }
    }

    function removeTask(index) {
      tasks.splice(index, 1); // 从响应式数组中移除任务
    }

    return {
      newTask,
      tasks,
      addTask,
      removeTask,
    };
  },
};
```

在模板中，我们可以使用 `v-model` 和 `v-for` 指令来展示和操作响应式数据

```html
<template>
  <div>
    <input type="text" v-model="newTask" placeholder="New Task" />
    <button @click="addTask">Add Task</button>

    <ul>
      <li v-for="(task, index) in tasks" :key="index">
        {{ task }}
        <button @click="removeTask(index)">Remove</button>
      </li>
    </ul>
  </div>
</template>
```

在这个例子中，我们使用 `ref` 创建了一个用于输入新任务的响应式数据 `newTask`，并使用 `reactive` 创建了一个存储任务的响应式数组 `tasks`。我们创建了两个函数 `addTask` 和 `removeTask` 来动态添加和删除任务。

当用户输入新任务并点击 "Add Task" 按钮时，`addTask` 函数将新任务添加到 `tasks` 数组中。同时，我们通过 `v-model` 指令将 `newTask` 与输入框进行双向绑定，以便实时更新输入的数据。

使用 `v-for` 指令，我们可以遍历 `tasks` 数组并显示每个任务。当用户点击 "Remove" 按钮时，`removeTask` 函数将根据任务的索引从 `tasks` 数组中移除该任务。

这个例子展示了如何在 Vue 3 中使用响应式数据来动态操作和更新数据。通过结合 `ref`、`reactive` 和 Vue 模板指令，我们可以轻松地创建和管理动态数据。
