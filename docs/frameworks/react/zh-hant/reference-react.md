
# React 頂層 API


`React` 是 React 函式庫的進入點。 如果你使用 `<script>` 標籤載入 React,這些頂層 API 可以在 `React` 全域變數使用。如果你使用 ES6 撰寫並使用 npm，你可以寫成 `import React from 'react'` 。如果你使用 ES5 撰寫並使用 npm，你可以寫成 `var React = require('react')` 。

## 概觀 {#overview}

### Component {#components}

React component 可以讓你把 UI 切分為獨立並可重複使用的單位，並且每個單位可以抽出來獨立思考。React component 可以透過繼承 `React.Component` 或是 `React.PureComponent` 定義。

 - [`React.Component`](#reactcomponent)
 - [`React.PureComponent`](#reactpurecomponent)

如果你沒有使用 ES6 class，你可以使用 `create-react-class` 模組。請參閱[使用 React 但不使用 ES6](./react-without-es6.html) 取得更多資訊。

React component 也可以定義為 function 並可以被封裝：

- [`React.memo`](#reactmemo)

### 建立 React Element {#creating-react-elements}

我們推薦[使用 JSX](./introducing-jsx.html) 來描述你的 UI 應該長成什麼樣子。 每個 JSX element 都只是呼叫 [`React.createElement()`](#createelement) 的語法糖。當你使用 JSX 的時候你將不需要直接呼叫以下的 method：

- [`createElement()`](#createelement)
- [`createFactory()`](#createfactory)

參閱[使用 React 但不使用 JSX](./react-without-jsx.html) 取得更多資訊。

### 操作 Element {#transforming-elements}

`React` 提供了多種 API 讓你可以操作 element：

- [`cloneElement()`](#cloneelement)
- [`isValidElement()`](#isvalidelement)
- [`React.Children`](#reactchildren)

### Fragment {#fragments}

`React` 也提供了一個 component 讓你一次 render 多個 element 而不需要額外的 wrapper。

- [`React.Fragment`](#reactfragment)

### Refs {#refs}

- [`React.createRef`](#reactcreateref)
- [`React.forwardRef`](#reactforwardref)

### Suspense {#suspense}

Suspense 讓 components 在 render 之前可以「暫停」並等待其他事情。目前 Suspense 只支援一個情境：[使用 `React.lazy` 動態載入 component](./code-splitting.html#reactlazy) 。在未來，我們也會支援像是抓取資料等更多的使用情境。

- [`React.lazy`](#reactlazy)
- [`React.Suspense`](#reactsuspense)

### Transitions {#transitions}

*Transitions* 是 React 18 引入的新 concurrent 功能。它們允許你 mark 更新為 transitions，這是告訴 React 它們可以被中斷，並且對於已經可看見的內容，避免回到 Suspense fallback。

- [`React.startTransition`](#starttransition)
- [`React.useTransition`](./hooks-reference.html#usetransition)

### Hooks {#hooks}

*Hooks* 是 React 16.8 開始的新功能。這讓你可以使用 state 以及其他的 React 功能而不需要撰寫一個 class。Hooks 有一個專屬的[文件專區](./hooks-intro.html) 和分開的 API 參考資料：

- [基本 Hooks](./hooks-reference.html#basic-hooks)
  - [`useState`](./hooks-reference.html#usestate)
  - [`useEffect`](./hooks-reference.html#useeffect)
  - [`useContext`](./hooks-reference.html#usecontext)
- [進階 Hooks](./hooks-reference.html#additional-hooks)
  - [`useReducer`](./hooks-reference.html#usereducer)
  - [`useCallback`](./hooks-reference.html#usecallback)
  - [`useMemo`](./hooks-reference.html#usememo)
  - [`useRef`](./hooks-reference.html#useref)
  - [`useImperativeHandle`](./hooks-reference.html#useimperativehandle)
  - [`useLayoutEffect`](./hooks-reference.html#uselayouteffect)
  - [`useDebugValue`](./hooks-reference.html#usedebugvalue)
  - [`useDeferredValue`](./hooks-reference.html#usedeferredvalue)
  - [`useTransition`](./hooks-reference.html#usetransition)
  - [`useId`](./hooks-reference.html#useid)
- [Library Hooks](./hooks-reference.html#library-hooks)
  - [`useSyncExternalStore`](./hooks-reference.html#usesyncexternalstore)
  - [`useInsertionEffect`](./hooks-reference.html#useinsertioneffect)

* * *

## 參考資料 {#reference}

### `React.Component` {#reactcomponent}

`React.Component` 是當你使用 [ES6 classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 定義 React component 時所用的 base class：

```javascript
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

參閱 [React.Component API 參考資料](./react-component.html) 可查到跟 `React.Component` 相關的 method 及 properties。

* * *

### `React.PureComponent` {#reactpurecomponent}

`React.PureComponent` 跟 [`React.Component`](#reactcomponent) 很相似。他們之間的差別是 [`React.Component`](#reactcomponent) 並沒有實作 [`shouldComponentUpdate()`](./react-component.html#shouldcomponentupdate)，但 `React.PureComponent` 提供了一個實作以對於 prop 及 state 進行 shallow compare。

> 備註
>
> `React.PureComponent` 的 `shouldComponentUpdate()` 只對 object 進行 shallow compare。如果這些 object 包含複雜的資料結構，在深層的資料有所改變的時候將有可能回傳錯誤結果 (false-negative)。繼承 `PureComponent` 的時候，請確保你只有簡單的 prop 跟 state，或在當你知道深層的資料有所改變的時候使用 [`forceUpdate()`](./react-component.html#forceupdate)。你也可以考慮改用 [immutable objects](https://immutable-js.com/) 進行快速的深層資料比較。
>
> 此外，`React.PureComponent` 的 `shouldComponentUpdate()` 將會跳過整個 subtree 的 prop 更新。請確保所有 children component 也是「pure」的。

* * *

### `React.memo` {#reactmemo}

```javascript
const MyComponent = React.memo(function MyComponent(props) {
  /* render using props */
});
```

`React.memo` 是一個 [higher order component](./higher-order-components.html)。

如果你的 function component 每次得到相同 prop 的時候都會 render 相同結果，你可以將其包在 `React.memo` 之中，透過快取 render 結果來在某些情況下加速。這表示 React 會跳過 render 這個 component，並直接重用上次的 render 結果。

`React.memo` 只會確認 props 的改變。如果你的 function component 被 wrap 在 `React.memo` 內，實作中具有一個 [`useState`](./hooks-state.html)、[`useReducer`](./hooks-reference.html#usereducer) 或 [`useContext`](./hooks-reference.html#usecontext) Hook，當 state 或 context 改變時，它仍然會持續 rerender。

這預設只會對 prop 進行 shallow compare 。如果你需要控制比較的方法，你可以提供一個自訂的比較 function 作為第二個參數。

```javascript
function MyComponent(props) {
  /* render using props */
}
function areEqual(prevProps, nextProps) {
  /*
  return true if passing nextProps to render would return
  the same result as passing prevProps to render,
  otherwise return false
  */
}
export default React.memo(MyComponent, areEqual);
```

這個 function 是用來作為**[效能最佳化](./optimizing-performance.html)**所用。請勿依賴這個 function 來「避免」render，這可能會產生 bug。

> 備註
>
> 與 class component 的 [`shouldComponentUpdate()`](./react-component.html#shouldcomponentupdate) method 不同,`areEqual` function 當 prop 相等的時候回傳 `true`，不相等的時候回傳 `false`。 這跟 `shouldComponentUpdate` 剛好相反。

* * *

### `createElement()` {#createelement}

```javascript
React.createElement(
  type,
  [props],
  [...children]
)
```

建立並回傳一個新的 [React element](./rendering-elements.html) 描述指定的 type。 Type 參數可以是一個標籤名稱字串（像是 `'div'` 或是 `'span'`）、一個 [React component](./components-and-props.html) type (class 或是 function) 或者是一個 [React fragment](#reactfragment) type。

使用 [JSX](./introducing-jsx.html) 寫的程式將會被轉換使用 `React.createElement()`。如果使用 JSX，你通常不需要自己呼叫 `React.createElement()`。請參閱[使用 React 但不使用 JSX](./react-without-jsx.html)。

* * *

### `cloneElement()` {#cloneelement}

```
React.cloneElement(
  element,
  [config],
  [...children]
)
```

使用 `element` 作為開始 clone 並回傳一個新 React element。`config` 應該包含所有新的 props、`key`、或 `ref`。產生的 element 將會有原始 element 的 props 以及新 props 的 shallow merge。新的 children 會取代現有的 children。如果 `config` 中不存在 `key` 和 `ref`，則原始的 `key` 和 `ref` 會被保留。

`React.cloneElement()` 幾乎等於：

```js
<element.type {...element.props} {...props}>{children}</element.type>
```

但是，這同時也會保留 `ref`。如果你有一個 child 上有 `ref` 的時候，你將不會不小心從你的上層元件偷走 ref。你的新 element 會保留一樣的 `ref`。如果有存在 `ref` 或 `key` 的話，將會取代舊的。

這個 API 是用來取代目前已經過時的 `React.addons.cloneWithProps()`。

* * *

### `createFactory()` {#createfactory}

```javascript
React.createFactory(type)
```

回傳一個 function 可以產生指定 type 的 React element。跟 [`React.createElement()`](#createelement) 一樣，這個 type 參數可以是一個標籤名稱字串（像是 `'div'` 或是 `'span'`）、一個 [React component](./components-and-props.html) type (class 或是 function) 或者是一個 [React fragment](#reactfragment) type。

這個 helper 已經被認定為過時，我們建議你使用 JSX 或是直接使用 `React.createElement()`。

如果使用 JSX，你通常不需要自己呼叫 `React.createFactory()`。請參閱[使用 React 但不使用 JSX](./react-without-jsx.html)。

* * *

### `isValidElement()` {#isvalidelement}

```javascript
React.isValidElement(object)
```

檢查一個 object 是否為 React element。通常回傳 `true` 或是 `false`。

* * *

### `React.Children` {#reactchildren}

`React.Children` 提供了一些工具可以將 `this.props.children` 作為不透明的資料結構處理。

#### `React.Children.map` {#reactchildrenmap}

```javascript
React.Children.map(children, function[(thisArg)])
```

對每一個列為 `children` 之中的直接 child 呼叫 function 並將 `this` 設定為 `thisArg`。如果 `children` 是一個 array，這將會列舉整個 array 並對每一個 child 呼叫這個 function。如果 children 是 `null` 或是 `undefined`，這個 method 將會回傳 `null` 或是 `undefined` 而不是一個 array。

> 備註
>
> 如果 `children` 是一個 `Fragment`，它將會被視為只有一個 child 而不會繼續深入列舉。

#### `React.Children.forEach` {#reactchildrenforeach}

```javascript
React.Children.forEach(children, function[(thisArg)])
```

跟 [`React.Children.map()`](#reactchildrenmap) 一樣，但不會回傳一個 array 。

#### `React.Children.count` {#reactchildrencount}

```javascript
React.Children.count(children)
```

回傳 `children` 到底有幾個 child，跟傳入 `map` 或是 `forEach` 的 callback 會被呼叫的次數一致。

#### `React.Children.only` {#reactchildrenonly}

```javascript
React.Children.only(children)
```

確認 `children` 只有一個 child (一個 React element）並回傳它，不然這個 method 將會拋出錯誤。

> 備註
>
>`React.Children.only()` 無法接受 [`React.Children.map()`](#reactchildrenmap) 的回傳值，因為那個回傳值將會是一個 array 而不是一個 React element 。

#### `React.Children.toArray` {#reactchildrentoarray}

```javascript
React.Children.toArray(children)
```

將 `children` 這個不透明的資料結構轉為一個扁平的 array 並對每個 child 指定一個 key。如果你想要在你的 render method 中操作 children 的集合時非常有用，特別是當你想要在傳遞它們之前調整順序或是擷取一部份 `this.props.children` 的時候。

> 備註
>
> `React.Children.toArray()` 會改變 key 來保留巢狀 array 的語意。也就是說 `toArray` 將會在每個 key 前面加入前綴，確保每個 element 的 key 都在與它原本輸入的 array 相關。

* * *

### `React.Fragment` {#reactfragment}

`React.Fragment` 讓你可以在一個 `render()` method 中一次回傳多個 element 而不需要建立一個額外的 DOM element：

```javascript
render() {
  return (
    <React.Fragment>
      Some text.
      <h2>A heading</h2>
    </React.Fragment>
  );
}
```

你也可以使用 `<></>` 的精簡表示法。請參閱 [React v16.2.0： 更好的 Fragment 支援](/blog/2017/11/28/react-v16.2.0-fragment-support.html)以獲得更多資訊。

### `React.createRef` {#reactcreateref}

`React.createRef` 會建立一個 [ref](./refs-and-the-dom.html) 以透過 ref attribute 夾帶在一個 React element 之上。
`embed:16-3-release-blog-post/create-ref-example.js`

### `React.forwardRef` {#reactforwardref}

`React.forwardRef` 會建立一個 React component 並將 [ref](./refs-and-the-dom.html) attribute 轉交給旗下的另外一個 component 。這個技巧不是很常被使用，但在以下兩個情況很適合：

* [將 ref 轉交給 DOM component](./forwarding-refs.html#forwarding-refs-to-dom-components)
* [在 higher-order-component 之中轉交 ref](./forwarding-refs.html#forwarding-refs-in-higher-order-components)

`React.forwardRef` 接受一個 render function 作為參數。React 會呼叫這個 function 並傳入兩個參數： `props` 以及 `ref` 。這個 function 應該要回傳一個 React node。

`embed:reference-react-forward-ref.js`

在上面這個範例，React 會把給予 `<FancyButton ref={ref}>` 的 `ref` 轉交給傳入 `React.forwardRef` 的 function 作為其第二個參數。這個 render function 接著將這個 `ref` 轉交給 `<button ref={ref}>` element。

結果，當 React 夾上這個 ref 的時候，`ref.current` 將會直接指到 `<button>` 這個 DOM element 實例。

請參閱[轉交 ref](./forwarding-refs.html) 獲得更多資訊。

### `React.lazy` {#reactlazy}

`React.lazy()` 讓你可以定義一個動態載入的 component。這可以在初始 render 期間延緩載入沒有被用到的 component 來減少 bundle size。

你可以閱讀我們的 [code splitting 文件](./code-splitting.html#reactlazy) 來學習怎麼使用它。你可能也想要閱讀[這篇文章](https://medium.com/@pomber/lazy-loading-and-preloading-components-in-react-16-6-804de091c82d) 更深入了解如何使用這個 function。

```js
// This component is loaded dynamically
const SomeComponent = React.lazy(() => import('./SomeComponent'));
```

請注意當你使用 `lazy` component 的時候，你的 render tree 上層中必須包含一個 `<React.Suspense>` 來指定 loading indicator。

### `React.Suspense` {#reactsuspense}

`React.Suspense` 讓你指定 loading indicator，避免下面 tree 中的某些 component 尚未準備好 render。未來我們計畫讓 `Suspense` 處理更多場景，像是資料的取得。你可以閱讀關於這個在[我們的 roadmap](/blog/2018/11/27/react-16-roadmap.html)。

現在，lazy loading component 是 `<React.Suspense>` **唯一**支援的使用場景：

```js
// This component is loaded dynamically
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // Displays <Spinner> until OtherComponent loads
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

在我們的 [code splitting 文件](./code-splitting.html#reactlazy) 有更多資訊。請注意 `lazy` component 可以在 `Suspense` tree 中底下很多層 ── 你不需要把每一個 `lazy` 元素包起來。最好的方法是將 `<Suspense>` 放在你想看到 loading indicator 的地方，而在所有你想進行 code splitting 的地方使用 `lazy()` 。

> 注意：
>
> 對於已經顯示給使用者的內容，切換回 loading indicator 可能會讓人迷惑。有時候在新的 UI 準備好之前，顯示「舊」的 UI 會來得更好。若要達成這個方式，你可以使用新的 [`startTransition`](#starttransition) 和 [`useTransition`](./hooks-reference.html#usetransition) transition APIs，來 mark 更新為一個 transitions，並且避免不預期的 fallback。

#### `React.Suspense` 是 Server Side Rendering {#reactsuspense-in-server-side-rendering}
在 server side rendering 期間，Suspense Boundaries 允許你透過 suspending 以更小的 chunks 來 flush 應用程式。
當一個 component suspends 時，我們 schedule 一個低優先級的 task 來 render 最近的 Suspense boundary 的 fallback。如果 component 在我們 flush fallback 之前 unsuspends，那麼我們把實際的內容傳送下去，並丟棄 fallback。

#### `React.Suspense` during hydration {#reactsuspense-during-hydration}
Suspense boundary 依賴於它們的 parent boundary 在它們被 hydrate 之前被 hydrate，但它們可以獨立於 sibling boundary hydrate。在被 hydrate 之前發生的 event 將會導致 boundary hydrate 的優先級高於相鄰的 boundary。[閱讀更多](https://github.com/reactwg/react-18/discussions/130)。

### `React.startTransition` {#starttransition}

```js
React.startTransition(callback)
```
這個方法被設計的是被用在當 [`React.useTransition`](./hooks-reference.html#usetransition) 不可使用時。

> 注意：
>
> 在 transition 中的更新會產生更緊急的更新，例如：點擊。
>
> 在 transition 中的更新不會顯示 re-suspended 的 fallback 內容，允許使用者可以在更新期間繼續互動。
>
> `React.startTransition` 不提供一個 `isPending` 的 flag。若要追蹤 transition 的 pending 狀態，參考 [`React.useTransition`](./hooks-reference.html#usetransition)。
<span style="float: footnote;"><a href="./index.html#toc">Go to TOC</a></span>
