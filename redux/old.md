# Redux（传统写法）

## 安装核心包

---

我们在React项目中使用redux需要安装下面几个核心包：

1. `redux`：redux的核心包
2. `react-redux`：React项目中使用redux的核心包，可以使redux的状态在组件中使用，修改等。
3. `redux-thunk`：redux常用中间件，可以使action creator返回一个函数

安装命令：

```powershell
`npm i redux react-redux redux-thunk`
```

## redux目录结构

---

|--redux

    |-- states 用于存放所有的全局状态，每个文件就是一个状态，里面包含该状态的所有内容

            |--counter.ts

            |-- userInfo.ts

    |-- rootReducer.ts 用于结合所有状态的reducer函数，并生成一个总的reducer

    |-- stateType 用于定义整个状态集合的类型，这里引入每个状态的类型并整合成一个对象

    |-- store.ts：redux的核心文件，用于生成store

## 编写state文件内容

---

> 我们这里用两个状态作为案例，一个是counter，一个是userInfo，下面是两个文件完整代码，代码后有各个代码模块的解释。

**文件counter.ts**

```tsx
type CounterType = number

// 定义action类型
type CounterActionType = {
    type: string,
    payload: number
}

// 定义action type常量
const incrementCount = 'INCREMENT_COUNT'
const decrementCount = 'DECREMENT_COUNT'

// 定义action creator
const incrementCountAction = (payload: number) => {
    return { type: incrementCount, payload }
}
const decrementCountAction = (payload: number) => {
    return { type: decrementCount, payload }
}

// 定义reducer
const countReducer = (state = 0, action: CounterActionType) => {
    const { type, payload } = action
    switch (type) {
        case incrementCount:
            return state + payload
        case decrementCount:
            return state - payload
        default:
            return state
    }
}

export {
    incrementCountAction,
    decrementCountAction,
    countReducer,
    CounterType
}
```

**文件userInfo.ts**

```tsx
type UserInfoType = {
    name: string,
    age: number
}

// 定义action类型
type UserInfoActionType = {
    type: string,
    payload: string | number
}

// 定义action type常量
const changeUserName = 'CHANGE_USER_NAME'
const changeUserAge = 'CHANGE_USER_AGE'

// 定义action creator
const changeUserNameAction = (payload: string | number) => {
    return { type: changeUserName, payload }
}
const changeUserAgeAction = (payload: string | number) => {
    return { type: changeUserAge, payload }
}

// 定义reducer
const userInfoReducer = (state = { name: 'jack', age: 18 }, action: UserInfoActionType) => {
    const { type, payload } = action
    switch (type) {
        case changeUserName:
            return { ...state, name: payload }
        case changeUserAge:
            return { ...state, age: payload }
        default:
            return state
    }
}

export {
    changeUserNameAction,
    changeUserAgeAction,
    userInfoReducer,
    UserInfoType
}
```

**文件解析：**

1. **定义state类型**：定义单个state类型，用来导出最后合成一个完整的状态集合的类型
2. **定义action类型**：定义action类型，用于定义reducer函数时给参数声明类型
3. **定义action type常量**：定义action常量的写法是一种redux的标准写法。定义常量可以防止拼写错误和重复定义，有利于代码维护，当我们想要修改某个action type的字符串是，只需要修- 改常量一处就可以，不需要多处修改字符串。
4. **定义action creator**：定义action creator函数也是redux的标准写法，这样可以避免使用dispatch派发某个action时，重复书写action对象。并且用action creator函数还可以处理一些异步action的场景，后续会介绍。
5. **定义reducer**：定义reducer函数，用于根据不同action计算生成最新的状态值，每个state核心的函数
6. **导出函数和类型**：导出其他文件需要用的函数和类型，供其他文件使用

## 生成rootReducer

---

> 当我们有多个状态时，我们需要将所有状态的reducer函数整合成一个根reducer，整合方法用的是redux提供的combineReducers。我们创建一个单独的文件用于整合所有reducer函数，该文件中需要引入所有state的reducer函数和redux的combineReducers方法，最后导出rootReducer，在创建store的时候需要用到。

**文件rootReducer.ts**

```tsx
import { combineReducers } from 'redux'
import { userInfoReducer } from './states/userInfo'
import { countReducer } from './states/counter'

const rootReducer = combineReducers({
    userInfo: userInfoReducer,
    counter: countReducer
})

export default rootReducer
```

## 创建store

---

```tsx
import { createStore, applyMiddleware } from 'redux'
import thunkMiddleware from 'redux-thunk'
import rootReducer from './rootReducer'

const store = createStore(rootReducer, applyMiddleware(thunkMiddleware))

export default store
```

**文件解析**：

1. 引入redux核心apicreateStore, applyMiddleware
2. 引入常用中间件redux-thunk，该中间件可以让你的action creator函数可以返回一个函数，而不是直接返回一个action对象。在返回的函数中可以进行异步操作，从而实现异步修改状态，-
3. 引入rootReducer函数
4. 创建store并导出

模拟异步修改counter值，就可以这样编写action creator函数。

```tsx
const decrementCountAction = (payload: number): any => {
    return (dispatch: Dispatch) => {
        setTimeout(() => {
            dispatch({ type: decrementCount, payload })
        }, 1000)
    }
}
```

## 定义整体state类型

---

> 因为我们是用ts开发项目，在我们创建出store后，**store**中的getState方法将返回一个state对象，该对象包含了所有的state，因此我们需要给这个整体的state对象定义类型，实际就是综合每个state的类型，具体代码如下：我们从每个状态文件获取单个状态类型，整合后导出。

```tsx
import { CounterType } from './states/counter'
import { UserInfoType } from './states/userInfo'

export type StateType = {
    counter: CounterType,
    userInfo: UserInfoType
}
```

## 根组件APP文件中注入store

---

> 想要项目中每个组件都能拿到全局状态，我们需要在跟组件处注入我们的store。这里就需要用到**react-redux**提供的核心组件Provider，我们使用Provider组件包裹APP.tsx文件中的元素，实现store注入。其中的**Demo01**，**Demo02**，**Demo03**是我们的测试组件，后续会用

```tsx
import React from 'react'
import { Provider } from 'react-redux'
import store from '@redux/store'
import Demo01 from '@pages/reduxDemo/demo01'
import Demo02 from '@pages/reduxDemo/demo02'
import Demo03 from '@pages/reduxDemo/demo03'

function ReduxApp() {

    return (
        <Provider store={store}>
            <div>
                <h1>React Redux App</h1>
                <Demo01 />
                <Demo02 />
                <Demo03 />
            </div>
        </Provider>
    )
}

export default ReduxApp
```

## 组件中使用状态、派发action

---

> 下面我们使用三个测试组件介绍在组件中如何使用状态和派发action。其中Demo01用于派发action修改状态。Demo02，Demo03分别用来使用counter，和userInfo两个状态。

**文件 Demo01.tsx**

```
import React from 'react'
import { useDispatch } from 'react-redux'
import { incrementCountAction, decrementCountAction } from '@src/redux/states/counter'
import { changeUserNameAction, changeUserAgeAction } from '@src/redux/states/userInfo'

function Demo01() {
    const dispatch = useDispatch()

    return (
        <div style={{ backgroundColor: 'pink' }}>
            <h1>This is Demo01</h1>
            <button onClick={() => dispatch(incrementCountAction(3))}>AddCount</button>
            <button onClick={() => dispatch(decrementCountAction(2))}>SubCount</button>
            <button onClick={() => dispatch(changeUserNameAction('Tom'))}>ChangeUserName</button>
            <button onClick={() => dispatch(changeUserAgeAction(20))}>ChangeUserAge</button>
        </div>
    )
}

export default Demo01
```

**代码解析：**

1. 我们派发action时，需要从**react-redux**导入核心方法useDispatch，该hook会返回一个方法，就是我们创建store中的核心方法dispatch。
2. 导入需要派发的action creator方法，action creator方法接收一个参数，该参数会结合方法中定义的action type返回一个具体的action对象，用于传入dispatch函数中，进行派发。
3. 创建四个测试按钮，当点击按钮时，派发对应的action，修改对应的状态

**文件Demo02.tsx**

```tsx
import React from 'react'
import { useSelector, shallowEqual } from 'react-redux'
import { StateType } from '@redux/stateType'

type StoreSelector = {
    counter: number
}

function Demo02() {
    const storeSelector = (state: StateType) => ({
        counter: state.counter
    }) as StoreSelector

    const { counter } = useSelector(storeSelector, shallowEqual) as StoreSelector

    return (
        <div style={{ backgroundColor: 'yellow' }}>
            <h1>This is Demo02 Counter: {counter}</h1>
        </div>
    )
}

export default Demo02
```

**文件Demo03.tsx**

```tsx
import React from 'react'
import { useSelector, shallowEqual } from 'react-redux'
import { StateType } from '@redux/stateType'

type StoreSelector = {
    userInfo: {
        name: string,
        age: number
    }
}

function Demo03() {
    const storeSelector = (state: StateType) => ({
        userInfo: state.userInfo
    }) as StoreSelector

    const { userInfo } = useSelector(storeSelector, shallowEqual) as StoreSelector

    return (
        <div style={{ backgroundColor: 'green' }}>
            <h1>This is Demo03 UserName: {userInfo.name} UserAge: {userInfo.age}</h1>
        </div>
    )
}

export default Demo03
```

**文件解析：**

1. 想要使用redux中的状态，首先我们需要从react-redux中引入两个核心方法useSelector, shallowEqual
2. useSelector, shallowEqual详解：**useSelector**方法用来返回过滤后的对象，该方法接收两个参数，第一个参数是必传参数，是一个selector方法。第二个参数是**shallowEqual**，这个函数用于对象浅比较，其作用就是当我们组件引用的state没有发生改变时，不渲染当前组件。因为我们知道，我们每次派发action时，所有的state的reducer函数都会执行，当没有匹配的action时，那些对象类型的state就会返回一个和之前值相等的新的对象引用。若不使用**shallowEqual**，就会导致所有引用redux对象状态的组件都渲染，造成不必要的浪费。所以这里使用**shallowEqual**只进行浅比较，不比较对象引用，只有当对象值发生改变时才会重新渲染组件。（可以在组件中打印console.log('Demo03 render')和console.log('Demo02 render')进行测试，测试使用shallowEqual和不使用的打印情况，这里我就不做测试了。）
3. 定义StoreSelector类型，用于断言**storeSelector**方法和**useSelector**返回值的类型，这里这样断言是因为我们能确定返回值类型，但是ts没有推断出来
4. 定义storeSelector方法是一个过滤state的方法，我们在一个组件中不可能使用所有的redux状态，因此我们用**storeSelector**方法取出我们所需要的部分就可以了。该方法接收一个参数是-- 整体state的对象集合，然后返回一个对象，对象中包含我们需要的state。我们需要将该方法传入useSelector中获取我们所需要的状态。
5. 在dom中使用状态
