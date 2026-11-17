---
title: "React 入门笔记"
slug: "react-native-notes-blog"
date: 2026-11-17T10:00:00+08:00
categories: ["React Native"]
tags: ["React Native", "JavaScript", "Hooks"]
draft: false
---

最近开始系统学 React / React Native，因为自己有 SwiftUI 基础，所以习惯把两者放在一起对照着理解。这篇文章整理自我学习时记的笔记，覆盖 JSX、组件、Props、State 以及 useEffect、useMemo、useCallback 这几个常用 Hook。

> 文中 React Native 示例默认已经引入 `View`、`Text`、`Button` 等组件，完整示例会带上 import。

## 一、React 是什么

React 是一个用于构建用户界面的 JavaScript 库。可以简单这样理解：

- SwiftUI：用 Swift 描述界面。
- React：用 JavaScript 描述界面。
- React Native：用 React 的开发模式，构建 iOS / Android 原生移动端界面。

对照看一个 Hello World。

SwiftUI：

```swift
struct ContentView: View {
    var body: some View {
        VStack {
            Text("Hello World")
            Button("点击我") {
                print("Clicked")
            }
        }
    }
}
```

React：

```jsx
function App() {
    return (
        <div>
            <h1>Hello World</h1>
            <button>点击我</button>
        </div>
    );
}
```

两者的思路非常接近：都是「描述界面结构」，然后让框架负责把界面渲染出来。React 里这段看起来像 HTML 的代码，就是 JSX。

## 二、JSX 是什么

JSX 全称 JavaScript XML，它允许我们在 JavaScript 代码里编写类似 HTML 的标签。

```jsx
const element = <h1>Hello World</h1>;
```

这不是普通的 JavaScript 语法，而是 JSX。构建工具会把 JSX 转成 JavaScript 调用，React 再根据这些调用创建和更新界面。

可以先记住一句话：**JSX = 在 JavaScript 中描述 UI 的一种语法。**

## 三、JSX 的几条基本规则

### 规则 1：必须有一个根元素

错误写法：

```jsx
function App() {
    return (
        <h1>Hello</h1>
        <p>World</p>
    );
}
```

正确写法：

```jsx
function App() {
    return (
        <div>
            <h1>Hello</h1>
            <p>World</p>
        </div>
    );
}
```

如果不想额外包一层 `div`，可以使用 Fragment：

```jsx
function App() {
    return (
        <>
            <h1>Hello</h1>
            <p>World</p>
        </>
    );
}
```

### 规则 2：插入 JavaScript 表达式要用 `{}`

```jsx
function App() {
    const name = "Tom";
    const age = 18;

    return (
        <div>
            <p>{name}</p>
            <p>{age}</p>
            <p>{age + 10}</p>
        </div>
    );
}
```

`{}` 的意思是：告诉 JSX，这里不是普通文本，而是一个 JavaScript 表达式。

对照 SwiftUI：

```swift
Text(name)
Text("\(age)")
```

加不加 `{}` 的区别：

```jsx
<p>{name}</p>  // 加 {}：算出 name 的值再显示 → 显示 Tom
<p>name</p>    // 不加：原样当文字 → 显示 "name"
```

### 规则 3：部分属性名和 HTML 不一样

```jsx
<div className="container">
    <button onClick={handleClick}>点击</button>
</div>
```

比如 HTML 里的 `class`，在 JSX 里要写成 `className`。React Native 里还会有更多不同，后面再展开。

### 规则 4：标签必须闭合

```jsx
<img src="avatar.png" />
<input />
```

即使是单标签，在 JSX 里也要显式闭合。

## 四、深入：如何在 JSX 中使用表达式

这是今天重点中的重点。看一个最简单的例子：

```jsx
function App() {
    const name = "Tom";

    return (
        <div>
            <h1>Hello, {name}</h1>
        </div>
    );
}
```

运行后界面显示：**Hello, Tom**

这里有三个东西：

- `function App()`：定义一个函数。
- `return`：返回 JSX，也就是描述 UI 的内容。
- `{name}`：把 JavaScript 变量的值放进 UI。

**JSX 中的 `{}` 是 JavaScript 和 UI 之间的桥梁。**

### `{}` 里面可以写什么？

只要是 JavaScript 表达式，通常都可以放进去。

```jsx
function App() {
    const name = "Tom";
    const age = 18;

    return (
        <div>
            <p>{name}</p>
            <p>{age + 1}</p>
            <p>{age >= 18 ? "成年" : "未成年"}</p>
        </div>
    );
}
```

渲染结果：

```text
Tom
19
成年
```

对照 SwiftUI：

```swift
Text(name)
Text("\(age + 1)")
Text(age >= 18 ? "成年" : "未成年")
```

### 容易踩坑的地方：表达式和语句

JSX 的 `{}` 里可以放表达式，但不能直接放普通语句。

正确：

```jsx
<p>{age + 1}</p>
<p>{age >= 18 ? "成年" : "未成年"}</p>
```

错误：

```jsx
<p>{if (age >= 18) {
    "成年"
}}</p>
```

为什么？因为：

- 表达式有计算结果。
- `if` 是语句，本身不产生一个可以直接嵌入 JSX 的值。

如果需要条件判断，可以用三元表达式：

```jsx
{age >= 18 ? <p>成年</p> : <p>未成年</p>}
```

### 放到 React Native 里是什么样？

前面用的 `div`、`p`、`h1` 是 React Web 的标签，RN 中不能直接这么用，而是使用原生组件：

```jsx
import { View, Text } from 'react-native';

function App() {
    const name = "Tom";
    const age = 18;

    return (
        <View>
            <Text>Hello, {name}</Text>
            <Text>{age + 1}</Text>
        </View>
    );
}
```

注意：JSX 语法是一样的，只是最终使用的 UI 组件不同。

## 五、Components（组件）

今天从 JSX 继续往下：**如何把一整块 UI 拆成多个可复用的组件。**

### 为什么需要组件？

假设你在开发一个云相册页面，每张照片都要展示：照片、照片名称、文件大小、下载按钮。

如果把所有 UI 都写在一个页面里，代码很快就会变得臃肿，而且相同的照片卡片还需要重复编写。React 的解决办法是：**把一块独立的 UI 封装成组件，需要时重复使用。**

### 如何定义一个 React 组件？

React 中最常见的组件写法是 JavaScript 函数组件：

```jsx
function PhotoCard() {
  return (
    <View>
      <Text>我的照片</Text>
      <Text>大小：2MB</Text>
    </View>
  );
}
```

这里有三个重点：

1. `function PhotoCard()`：定义一个 JavaScript 函数。
2. `return (...)`：返回 JSX，描述这个组件要展示的 UI。
3. `PhotoCard`：组件名称，首字母必须大写。

注意：React Native 中的 `View` 和 `Text` 是原生 UI 组件，不是 HTML 标签。

### 如何使用组件？

定义好组件后，可以像使用 JSX 标签一样使用它：

```jsx
function PhotoCard() {
  return (
    <View>
      <Text>我的照片</Text>
      <Text>大小：2MB</Text>
    </View>
  );
}

function App() {
  return (
    <View>
      <Text>云相册</Text>
      <PhotoCard />
      <PhotoCard />
    </View>
  );
}
```

这里发生了什么？

- `PhotoCard` 定义了一份照片卡片 UI。
- `App` 是页面组件。
- `<PhotoCard />` 表示在页面中使用这个组件。
- 使用两次，就会渲染两份照片卡片。

**组件的核心价值：封装、复用、组合。**

### 和 SwiftUI 有什么区别？

可以把 React 函数组件与 SwiftUI 的 `View` 结构体对照着理解。

SwiftUI：

```swift
struct PhotoCard: View {
    var body: some View {
        VStack {
            Text("我的照片")
            Text("大小：2MB")
        }
    }
}
```

React Native：

```jsx
function PhotoCard() {
  return (
    <View>
      <Text>我的照片</Text>
      <Text>大小：2MB</Text>
    </View>
  );
}
```

一个重要的区别：SwiftUI 通常通过遵循 `View` 协议的类型描述 UI；React 函数组件本质上是由 React 调用的函数，返回 React 能够渲染的内容。

另外，**不要直接通过 `PhotoCard()` 调用组件来代替 `<PhotoCard />`**。让 React 通过 JSX 管理组件的渲染，才能正确使用它的组件状态和其他机制。

练习题：实现一个 `UserCard` 组件，使用 `View` 作为容器，展示用户名 `Alice` 和职位 `iOS Developer`，并在 `App` 中使用两次。

```jsx
function UserCard() {
  return (
    <View>
      <Text>Alice</Text>
      <Text>iOS Developer</Text>
    </View>
  );
}

function App() {
  return (
    <View>
      <UserCard />
      <UserCard />
    </View>
  );
}
```

## 六、Props（组件属性）

上一节学了 Components，知道可以通过 `<UserCard />` 重复使用组件。但问题是：如果两张用户卡片需要展示不同的信息，难道要定义两个不同的组件吗？不需要，今天学习 **Props**。

### Props 是什么？

Props 是父组件传递给子组件的数据，可以理解为组件的输入参数。

SwiftUI 中我们可以这样写：

```swift
struct UserCard: View {
    let name: String
    let job: String

    var body: some View {
        VStack {
            Text(name)
            Text(job)
        }
    }
}

// 使用
UserCard(name: "Alice", job: "iOS Developer")
UserCard(name: "Bob", job: "C++ Developer")
```

React Native 中的写法：

```jsx
function UserCard(props) {
  return (
    <View>
      <Text>{props.name}</Text>
      <Text>{props.job}</Text>
    </View>
  );
}

function App() {
  return (
    <View>
      <UserCard name="Alice" job="iOS Developer" />
      <UserCard name="Bob" job="C++ Developer" />
    </View>
  );
}
```

同一个 `UserCard` 组件，因为接收的 Props 不同，所以展示了不同内容。

### Props 的简写方式

上面的 `props.name`、`props.job` 写起来比较长。JavaScript 支持解构赋值，可以直接提取属性：

```jsx
function UserCard({ name, job }) {
  return (
    <View>
      <Text>{name}</Text>
      <Text>{job}</Text>
    </View>
  );
}
```

注意：这里的 `{ name, job }` 是 JavaScript 的对象解构语法，不是 JSX 中用于插入表达式的 `{}`。实际开发中这种写法非常常见。

### Props 可以传递哪些数据？

不仅可以传字符串，还可以传数字、布尔值、对象、数组，甚至函数：

```jsx
<UserCard
  name="Alice"
  age={22}
  isVip={true}
  user={{ id: 1, city: "Shanghai" }}
/>
```

注意：

- 字符串可以直接写：`name="Alice"`。
- 数字和布尔值需要用 `{}`：`age={22}`、`isVip={true}`。
- 对象也通过 `{}` 传递：`user={{ id: 1 }}`。

### 一个非常重要的规则：Props 不应该被子组件修改

Props 是父组件传递给子组件的数据，子组件应该把它当作只读数据。

```jsx
function UserCard({ name }) {
  // 错误：不要这样修改 Props
  name = "Bob";

  return <Text>{name}</Text>;
}
```

如果需要修改组件内部的数据，应该使用 State（下一节会讲）。

动手写代码：实现一个 `PhotoCard` 组件，接收三个 Props——`name`（照片名称）、`size`（照片大小）、`isDownloaded`（是否已下载）。要求展示名称和大小，已下载显示「已下载」、否则显示「未下载」，并在 `App` 中使用两次传入不同数据。

```jsx
function PhotoCard({ name, size, isDownloaded }) {
  return (
    <View>
      <Text>{name}</Text>
      <Text>{size}</Text>
      <Text>{isDownloaded ? "已下载" : "未下载"}</Text>
    </View>
  );
}

function App() {
  return (
    <View>
      <PhotoCard name="haha" size={10} isDownloaded={true} />
      <PhotoCard name="aaa" size={1} isDownloaded={false} />
    </View>
  );
}
```

## 七、State 和 useState

Props 是父组件传给子组件的数据，而 State 是组件自己管理的数据。

### ① State 是什么？

State 是组件内部的数据。当 State 发生变化时，React 会重新渲染组件，让界面反映最新状态。

SwiftUI 对照：

```swift
struct DownloadView: View {
    @State private var downloaded = false

    var body: some View {
        Text(downloaded ? "已下载" : "未下载")
    }
}
```

React Native：

```jsx
function DownloadView() {
    const [downloaded, setDownloaded] = useState(false);

    return (
        <Text>
            {downloaded ? "已下载" : "未下载"}
        </Text>
    );
}
```

### ② useState 怎么用？

```jsx
const [downloaded, setDownloaded] = useState(false);
```

可以把它理解成 SwiftUI 的：

```swift
@State private var downloaded = false
```

不过 React 中要通过 `setDownloaded` 更新状态，而不是直接给 `downloaded` 赋值。

### ③ 如何修改 State？

```jsx
function DownloadView() {
    const [downloaded, setDownloaded] = useState(false);

    return (
        <View>
            <Text>{downloaded ? "已下载" : "未下载"}</Text>
            <Button
                title="下载完成"
                onPress={() => setDownloaded(true)}
            />
        </View>
    );
}
```

点击按钮后：

1. 执行 `setDownloaded(true)`。
2. React 更新 State。
3. React 重新渲染组件。
4. 页面显示「已下载」。

注意：React Native 的 `Button` 使用 `onPress`，不是网页 React 常见的 `onClick`。

练习题：实现一个计数器组件，初始数字为 0，页面展示当前数字，点击按钮数字加 1，并用 `useState` 管理数字。

```jsx
import { useState } from 'react';
import { View, Text, Button } from 'react-native';

function Count() {
    const [num, setNum] = useState(0);

    return (
        <View>
            <Text>{num}</Text>
            <Button
                title="增加"
                onPress={() => setNum(num + 1)}
            />
        </View>
    );
}
```

### 值更新 vs 函数式更新

这是最容易踩坑的地方。假设当前 `num = 0`，连续执行三次：

```jsx
setNum(num + 1);
setNum(num + 1);
setNum(num + 1);
```

最终 `num` 是多少？答案是 **1**。

方式一：直接传值（基于快照）

```jsx
setNum(num + 1);
setNum(num + 1);
setNum(num + 1);
// 结果：1
```

三次都基于同一个快照 `num = 0` 计算，等价于执行三次 `setNum(1)`，合并后结果还是 1。

方式二：函数式更新（基于上次结果）

```jsx
setNum(prev => prev + 1);
setNum(prev => prev + 1);
setNum(prev => prev + 1);
// 结果：3
```

每次更新都基于上一次的结果，所以是 3。需要基于上一次状态连续更新时，用函数式写法。

## 八、useEffect

### 为什么需要 useEffect？

先看一个场景。假设你在开发云相册页面：

- 页面打开时，需要请求云相册列表。
- 用户切换页面时，需要取消某些监听。
- 用户退出页面时，需要清理定时器。

这些操作都不是单纯地描述 UI，而是需要和外部系统交互。React 把这类操作称为**副作用（Side Effect）**。`useEffect` 就是用来在组件渲染后同步这些外部系统的。

### useEffect 基本语法

```jsx
import { useEffect } from 'react';

function App() {
    useEffect(() => {
        console.log("组件渲染后执行");
    });

    return (
        <View>
            <Text>Hello React Native</Text>
        </View>
    );
}
```

语法结构：

```jsx
useEffect(() => {
    // 副作用逻辑

    return () => {
        // 清理逻辑
    };
}, [依赖项]);
```

先记住两个东西：

- 第一个参数：执行的副作用函数。
- 第二个参数：依赖数组，决定什么时候重新执行 Effect。

### 三种依赖数组

情况一：不传依赖数组——每次渲染后都执行

```jsx
useEffect(() => {
    console.log("每次渲染后执行");
});
```

情况二：依赖数组为空——仅在首次挂载后执行一次

```jsx
useEffect(() => {
    console.log("组件挂载后执行");
}, []);
```

通常在组件首次挂载后执行一次，适合初始化操作，例如建立某些监听、初始化资源。

注意：React Strict Mode 在开发环境下可能额外执行 Effect 的建立和清理，因此不要把它绝对理解为「任何环境下都只执行一次」。

一个具体例子：

```jsx
useEffect(() => {
    console.log("effect 建立");
    return () => console.log("effect 清理");
}, []);
```

生产环境控制台：

```text
effect 建立
```

开发环境 + Strict Mode 控制台：

```text
effect 建立
effect 清理
effect 建立
```

注意：这不是 bug，而是 React 在帮你验证清理逻辑是否正确。

情况三：依赖数组里有变量——依赖变化时重新执行

```jsx
function UserInfo() {
    const [userId, setUserId] = useState(1);

    useEffect(() => {
        console.log("userId 发生变化，重新请求用户信息");
    }, [userId]);

    return (
        <View>
            <Text>{userId}</Text>
            <Button
                title="切换用户"
                onPress={() => setUserId(userId + 1)}
            />
        </View>
    );
}
```

当 `userId` 发生变化，Effect 会重新执行。

### useEffect 和 SwiftUI 对比

有 SwiftUI 基础的话，可以这样建立直觉。

SwiftUI：

```swift
struct UserView: View {
    @State private var userId = 1

    var body: some View {
        Text("\(userId)")
            .onAppear {
                print("页面出现")
            }
            .onChange(of: userId) { oldValue, newValue in
                print("userId 发生变化")
            }
    }
}
```

React：

```jsx
function UserView() {
    const [userId, setUserId] = useState(1);

    useEffect(() => {
        console.log("userId 发生变化");
    }, [userId]);

    return <Text>{userId}</Text>;
}
```

它们不是完全等价的，但可以先这样理解：

- SwiftUI `.onAppear`：帮助理解「首次出现时的操作」。
- SwiftUI `.onChange`：帮助理解「依赖变化时的操作」。
- React `useEffect`：通过依赖数组控制 Effect 的重新同步。

### 清理函数：非常重要

例如在组件里创建了一个定时器：

```jsx
function Timer() {
    useEffect(() => {
        const timer = setInterval(() => {
            console.log("每秒执行一次");
        }, 1000);

        return () => {
            clearInterval(timer);
        };
    }, []);

    return <Text>Timer</Text>;
}
```

这里 `return () => { clearInterval(timer); }` 就是清理函数。当组件卸载，或者 Effect 需要重新执行前，React 会调用清理函数。

为什么需要清理？如果组件已经离开页面，但定时器还在运行，就可能造成资源浪费，甚至产生重复监听、重复请求等问题。

React Native 中，监听器也需要类似的处理：

```jsx
useEffect(() => {
    const subscription = someEventEmitter.addListener(
        "deviceConnected",
        () => {
            console.log("设备已连接");
        }
    );

    return () => {
        subscription.remove();
    };
}, []);
```

这里的 `someEventEmitter` 是示意对象，实际项目中要替换为具体的事件监听 API。

实践题：实现一个组件，初始 `count = 0`，点击按钮 `count` 加 1，用 `useEffect` 监听 `count`，每次 `count` 变化时在控制台打印 `count changed: 当前数字`。

```jsx
import { useState, useEffect } from 'react';
import { View, Text, Button } from 'react-native';

function Count() {
    const [num, setNum] = useState(0);

    useEffect(() => {
        console.log(`count changed: ${num}`);
    }, [num]);

    return (
        <View>
            <Text>{num}</Text>
            <Button
                title="增加"
                onPress={() => setNum(num + 1)}
            />
        </View>
    );
}
```

## 九、useMemo

### 为什么需要 useMemo？

假设你在开发云相册，有 1000 张照片，需要根据大小筛选出大于 10MB 的照片：

```jsx
function PhotoList({ photos }) {
    const bigPhotos = photos.filter(photo => photo.size > 10);

    return (
        <View>
            <Text>大文件数量：{bigPhotos.length}</Text>
        </View>
    );
}
```

这段代码有一个潜在问题：每次 `PhotoList` 组件重新渲染时，都会重新执行 `filter`，即使 `photos` 没有变化。如果计算比较复杂，就可能浪费性能。`useMemo` 就是为了解决这类问题。

### useMemo 的基本语法

```jsx
const result = useMemo(() => {
    return 计算逻辑;
}, [依赖项]);
```

它有两个参数：

1. 第一个参数：计算函数。
2. 第二个参数：依赖数组。

React 会缓存计算结果。当依赖项没有变化时，直接复用上一次的结果。

修改刚才的代码：

```jsx
import { useMemo } from 'react';

function PhotoList({ photos }) {
    const bigPhotos = useMemo(() => {
        return photos.filter(photo => photo.size > 10);
    }, [photos]);

    return (
        <View>
            <Text>大文件数量：{bigPhotos.length}</Text>
        </View>
    );
}
```

现在：

- `photos` 没变化：复用上次的筛选结果。
- `photos` 变化：重新执行筛选。

### useMemo 和 useEffect 有什么区别？

这是面试中经常问的问题。最重要的区别：**useMemo 用来计算数据，useEffect 用来执行副作用。**

```jsx
// 计算数据
const bigPhotos = useMemo(() => {
    return photos.filter(photo => photo.size > 10);
}, [photos]);

// 请求数据
useEffect(() => {
    fetchPhotos();
}, []);
```

不要用 `useMemo` 发网络请求，也不要为了计算一个简单数字就随意使用 `useEffect`。

### useMemo 和 Swift 的对比

SwiftUI 中你可能会写：

```swift
var bigPhotos: [Photo] {
    photos.filter { $0.size > 10 }
}
```

这属于计算属性，每次访问时都可能重新计算。React 的 `useMemo` 可以在依赖不变时缓存计算结果。

但注意：Swift 的计算属性和 React 的 `useMemo` 并不完全等价，前者是语言特性，后者是 React 的性能优化机制。

### 一个重要注意事项

不要把 `useMemo` 当成保证业务正确性的工具。例如：

```jsx
const total = useMemo(() => {
    return price * count;
}, [price]);
```

这段代码有问题：计算依赖了 `price` 和 `count`，却只监听了 `price`。正确写法：

```jsx
const total = useMemo(() => {
    return price * count;
}, [price, count]);
```

**依赖数组必须包含计算中使用到的响应式数据。** 另外，`useMemo` 是性能优化手段，不是语义保证，React 在特定情况下可以丢弃缓存，因此不要依赖它保存必须长期存在的状态。

实践题：用 `useState` 定义 `count`（初始 1）和 `other`（初始 0），用 `useMemo` 计算 `count * 100` 并展示；一个按钮让 `count + 1`，另一个让 `other + 1`。思考：点击 `other + 1` 时，`useMemo` 是否需要重新计算？

```jsx
import { useState, useMemo } from 'react';
import { View, Text, Button } from 'react-native';

function Count() {
    const [count, setCount] = useState(1);
    const [other, setOther] = useState(0);

    const countTimes100 = useMemo(() => {
        return count * 100;
    }, [count]);

    return (
        <View>
            <Text>计算结果：{countTimes100}</Text>
            <Text>other：{other}</Text>
            <Button title="count + 1" onPress={() => setCount(count + 1)} />
            <Button title="other + 1" onPress={() => setOther(other + 1)} />
        </View>
    );
}
```

答案：不会。因为依赖数组是 `[count]`，`other` 变化不会触发 `useMemo` 重新计算。

## 十、useCallback

### 它解决什么问题？

先看一个场景：父组件里有一个计数器，子组件是一个照片按钮。

```jsx
function PhotoItem({ onSelect }) {
  console.log('PhotoItem render');

  return <Button title="选择照片" onPress={onSelect} />;
}

function PhotoList() {
  const [count, setCount] = useState(0);

  const handleSelect = () => {
    console.log('选择了照片');
  };

  return (
    <View>
      <PhotoItem onSelect={handleSelect} />
      <Button title="增加计数" onPress={() => setCount(count + 1)} />
    </View>
  );
}
```

当你点击「增加计数」时：

1. `count` 改变，`PhotoList` 重新渲染。
2. 重新执行 `PhotoList` 函数。
3. `handleSelect` 也会重新创建，成为一个新的函数对象。
4. `PhotoItem` 收到的 `onSelect` 引用发生变化。

如果子组件使用了 `React.memo`，它原本可以在 props 没变化时跳过渲染，但现在函数引用变了，就可能导致它重新渲染。

### useCallback 怎么解决？

`useCallback` 可以缓存函数引用：

```jsx
const handleSelect = useCallback(() => {
  console.log('选择了照片');
}, []);
```

完整写法：

```jsx
import React, { useCallback, useState } from 'react';
import { View, Button } from 'react-native';

const PhotoItem = React.memo(function PhotoItem({ onSelect }) {
  console.log('PhotoItem render');

  return <Button title="选择照片" onPress={onSelect} />;
});

function PhotoList() {
  const [count, setCount] = useState(0);

  const handleSelect = useCallback(() => {
    console.log('选择了照片');
  }, []);

  return (
    <View>
      <PhotoItem onSelect={handleSelect} />
      <Button title="增加计数" onPress={() => setCount(count + 1)} />
    </View>
  );
}
```

现在点击「增加计数」：

- `PhotoList` 仍然会重新渲染。
- `handleSelect` 的函数引用保持稳定（依赖项没变化时）。
- `PhotoItem` 的 props 没变化，配合 `React.memo`，可以跳过这次渲染。

注意：**useCallback 不会阻止父组件重新渲染，也不保证子组件绝对不渲染**，它只是让函数引用在依赖项不变时保持稳定。

### 和 useMemo 的区别

可以这样记：

```jsx
// 缓存计算结果
const result = useMemo(() => num * 100, [num]);

// 缓存函数引用
const handlePress = useCallback(() => {
  console.log(num);
}, [num]);
```

`useCallback` 的依赖数组也很重要：函数内部使用了会变化的状态或 props，通常就需要把它们放进依赖数组。

代码练习：补全下面的代码，点击按钮时打印 `"选择了照片"`，并使用 `useCallback` 缓存这个函数。

```jsx
function PhotoList() {
  const handleSelect = useCallback(() => {
    console.log("选择了照片");
  }, []);

  return (
    <View>
      <Button title="选择照片" onPress={handleSelect} />
    </View>
  );
}
```

## 十一、小结

这篇文章从 React 是什么出发，一路整理了 JSX、组件、Props、State 和四个常用 Hook。用 SwiftUI 的视角对照记忆会更轻松：

| Hook | 用途 | 一句话记忆 |
| --- | --- | --- |
| useState | 管理组件内部状态 | 数据变了，重新渲染 |
| useEffect | 执行副作用 | 渲染后同步外部系统 |
| useMemo | 缓存计算结果 | 依赖不变就复用 |
| useCallback | 缓存函数引用 | 函数引用保持稳定 |

最核心的一条主线：**JSX 描述 UI，组件拆分 UI，Props 传入数据，State 管理内部状态，Hook 处理副作用和性能。**
