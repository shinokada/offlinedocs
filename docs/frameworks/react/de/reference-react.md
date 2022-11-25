
# React Top-Level API


`React` ist der Zugangspunkt zur React-Bibliothek. Falls du React von einem `<script>`-Tag heraus lädst, sind diese Top-Level APIs im globalen `React`-Objekt verfügbar. Solltest du ES6 mit npm verwenden, kannst du `import React from 'react'` schreiben. Wenn du ES5 mit npm verwendest, kannst du `var React = require('react')` schreiben.

## Übersicht {#overview}

### Komponenten {#components}

Mit React-Komponenten kann die Benutzeroberfläche in unabhängige, wiederverwendbare Bestandteile aufgeteilt werden, die jeweils isoliert betrachtet werden können. React-Komponenten können als Unterklassen von `React.Component` oder `React.PureComponent` definiert werden.

 - [`React.Component`](#reactcomponent)
 - [`React.PureComponent`](#reactpurecomponent)

Solltest du keine ES6-Klassen benutzen, kannst du stattdessen das `create-react-class`-Modul verwenden. Siehe [React ohne ES6](./react-without-es6.html) für mehr Informationen.

React-Komponenten können auch als Funktionen definiert werden, die ummantelt werden können:

- [`React.memo`](#reactmemo)

### React-Elemente erstellen {#creating-react-elements}

Um zu beschreiben, wie die Benutzeroberfläche aussehen soll, empfehlen wir, [JSX](./introducing-jsx.html) zu verwenden. Ein JSX-Element ist eine alternative Schreibweise für das Aufrufen von [`React.createElement()`](#createelement). Wenn du JSX verwendest, rufst du die folgenden Methoden normalerweise nicht direkt auf:
- [`createElement()`](#createelement)
- [`createFactory()`](#createfactory)

Siehe [React ohne JSX](./react-without-jsx.html) für mehr Informationen.

### Elemente verändern {#transforming-elements}

`React` stellt mehrere APIs zur Verfügung, um Elemente zu manipulieren:

- [`cloneElement()`](#cloneelement)
- [`isValidElement()`](#isvalidelement)
- [`React.Children`](#reactchildren)

### Fragmente {#fragments}

`React` stellt auch eine Komponente zu Verfügung, mit der mehrere Elemente ohne Wrapper gerendert werden können:

- [`React.Fragment`](#reactfragment)

### Refs {#refs}

- [`React.createRef`](#reactcreateref)
- [`React.forwardRef`](#reactforwardref)

### Suspense {#suspense}

Mit Suspense können Komponenten vor dem Rendern auf etwas "warten". Momentan unterstützt Suspense nur einen Anwendungsfall: [Komponenten dynamisch laden mit `React.lazy`](./code-splitting.html#reactlazy). In Zukunft wird es auch andere Anwendungsfälle wie z.B. das Abrufen von Daten unterstützen.

- [`React.lazy`](#reactlazy)
- [`React.Suspense`](#reactsuspense)

### Transitions {#transitions}

*Transitions* are a new concurrent feature introduced in React 18. They allow you to mark updates as transitions, which tells React that they can be interrupted and avoid going back to Suspense fallbacks for already visible content.

- [`React.startTransition`](#starttransition)
- [`React.useTransition`](./hooks-reference.html#usetransition)

### Hooks {#hooks}

*Hooks* sind neu in React 16.8. Sie erlauben die Verwendung von State und anderen React-Features ohne Klassen. Hooks haben einen [eigenen Bereich in der Dokumentation](./hooks-intro.html) und eine eigene API-Referenz:

- [Grundlegende Hooks](./hooks-reference.html#basic-hooks)
  - [`useState`](./hooks-reference.html#usestate)
  - [`useEffect`](./hooks-reference.html#useeffect)
  - [`useContext`](./hooks-reference.html#usecontext)
- [Weitere Hooks](./hooks-reference.html#additional-hooks)
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

## Referenz {#reference}

### `React.Component` {#reactcomponent}

`React.Component` ist die Basis-Klasse für React-Komponenten, die mit [ES6-Klassen](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) definiert werden:

```javascript
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Unter [React.Component API-Referenz](./react-component.html) gibt es eine Liste der Methoden und Eigenschaften, die sich auf die `React.Component`-Basisklasse beziehen.

* * *

### `React.PureComponent` {#reactpurecomponent}

`React.PureComponent` ähnelt [`React.Component`](#reactcomponent), unterscheidet sich aber dahingehend, dass [`shouldComponentUpdate()`](./react-component.html#shouldcomponentupdate) von  [`React.Component`](#reactcomponent) nicht implementiert wird, während `React.PureComponent` es durch das oberflächliche Vergleichen von Props und State implementiert.

Wenn die `render()`-Funktion einer React-Komponente bei Gleichbleiben von Props und State das gleiche Ergebnis rendert, kann die Nutzung von `React.PureComponent` in manchen Fällen die Performance verbessern.

> Hinweis
>
> `shouldComponentUpdate()` von `React.PureComponent` vergleicht Objekte nur oberflächlich. Falls diese komplexe Datenstrukturen enthalten, könnte es für tiefer gelegene Unterschiede zu falschen Negativbefunden kommen. Benutze `PureComponent` nur dann, wenn Props und State einfach sind, oder verwende [`forceUpdate()`](./react-component.html#forceupdate) wenn du weißt, dass tiefergelegene Datenstrukturen sich verändert haben. Oder überlege dir, [unveränderliche (immutable) Objekte](https://immutable-js.com/) zu verwenden, um das schnelle Vergleichen geschachtelter Daten zu erleichtern.
>
> Darüber hinaus überspringt `shouldComponentUpdate()` von `React.PureComponent` den kompletten der Komponente untergeordneten Teilbaum. Es sollte also sichergestellt werden, dass alle Kind-Komponenten auch "pur" sind.

* * *

### `React.memo` {#reactmemo}

```javascript
const MyComponent = React.memo(function MyComponent(props) {
  /* rendere mit Props */
});
```

`React.memo` ist eine [Higher-Order-Komponente](./higher-order-components.html).

Wenn deine Komponente das gleiche Ergebnis mit den gleichen Props liefert, kannst du es mit `React.memo` umschließen um in einigen Fällen die Performance zu verbessern, in dem sich das Ergebnis gemerkt wird. Das bedeutet, dass React das Rendern der Komponente überspringt und stattdessen das zuletzt gerenderte Ergebnis wiederverwendet.

`React.memo` betrifft nur Änderungen von Props. Wenn deine Funktionskomponente von `React.memo` umschloßen ist, eine [`useState`](./hooks-state.html), [`useReducer`](./hooks-reference.html#usereducer) oder [`useContext`](./hooks-reference.html#usecontext) Hook in ihrer Implementierung hat, wird sie immer noch gerendert, wenn sich der State oder der Context ändert.

Standardmäßig wird es nur oberflächlich komplexe Objekte im Props-Objekt vergleichen. Wenn du die Kontrolle über den Vergleich haben möchtest, kannst du auch eine benutzerdefinierte Vergleichsfunktion als zweites Argument angeben.

```javascript
function MyComponent(props) {
  /* rendere mit Props */
}
function areEqual(prevProps, nextProps) {
  /*
  Gib 'true' zurück, wenn nextProps das gleiche Ergebnis rendern würde 
  wie prevProps, ansonsten gib 'false' zurück
  */
}
export default React.memo(MyComponent, areEqual);
```

Diese Methode existiert nur zur **[Performance-Optimierung](./optimizing-performance.html).** Verlasse dich nicht auf sie, um das Rendern zu "verhindern", da dadurch Bugs entstehen können.

> Hinweis
>
> Im Gegensatz zur [`shouldComponentUpdate()`](./react-component.html#shouldcomponentupdate)-Methode in Klassen-Komponenten gibt die `areEqual`-Funktion `true` zurück, wenn die Props gleich sind und `false`, wenn die Props unterschiedlich sind, also genau andersherum als `shouldComponentUpdate`.

* * *

### `createElement()` {#createelement}

```javascript
React.createElement(
  type,
  [props],
  [...children]
)
```

Erzeugt und gibt ein neues [React-Element](./rendering-elements.html) eines bestimmten Typs zurück. Das Typ-Argument kann entweder ein Tag-Name als String (z.B. `'div'` oder `'span'`), ein  [React-Komponenten](./components-and-props.html)-Typ  (eine Klasse oder eine Funktion) oder ein [React-Fragment](#reactfragment)-Typ sein.

In [JSX](./introducing-jsx.html) geschriebener Code wird konvertiert, um `React.createElement()` zu benutzen. Wenn du JSX verwendest, rufst du `React.createElement()` normalerweise nicht direkt auf. Siehe [React ohne JSX](./react-without-jsx.html) für mehr Informationen.

* * *

### `cloneElement()` {#cloneelement}

```
React.cloneElement(
  element,
  [config],
  [...children]
)
```

Klone und gebe ein neues React-Element mit `element` als Ausgangspunkt zurück. `config` sollte alle neuen Props, `key`s oder `ref`s enthalten. Das resultierende Element hat die Props des ursprünglichen Elements, in die die neuen Props oberflächlich eingefügt werden. Neue Kinder ersetzen die existierenden Kinder. `key` und `ref` des ursprünglichen Elements bleiben erhalten, wenn kein `key` und `ref` in der `config` vorhanden sind.

`React.cloneElement()` ist fast äquivalent zu:

```js
<element.type {...element.props} {...props}>{children}</element.type>
```

Es bewahrt jedoch auch die Refs. Das bedeutet, wenn du ein Kind mit einem Ref erhälst, dieses nicht versehentlich vom Vorgänger stiehlst. Du bekommst das selbe Ref an dein neues Element angehängt. Das neue Ref oder der neue Key ersetzt die alten, falls vorhanden.

Diese API wurde als Ersatz für das veraltete `React.addons.cloneWithProps()` eingeführt.

* * *

### `createFactory()` {#createfactory}

```javascript
React.createFactory(type)
```

Gibt eine Funktion zurück, die React-Elemente eines bestimmten Typs erzeugt. Wie bei [`React.createElement()`](#createelement) kann das Typ-Argument entweder ein Tag-Name als String (z.B. `'div'` oder `'span'`), ein  [React-Komponenten](./components-and-props.html)-Typ (eine Klasse oder eine Funktion) oder ein [React-Fragment](#reactfragment)-Typ sein.

Dieser Helfer gilt als veraltet, und wir empfehlen, entweder JSX oder `React.createElement()` direkt zu verwenden.

Wenn du JSX verwendest, rufst du `React.createFactory()` normalerweise nicht direkt auf. Siehe [React ohne JSX](./react-without-jsx.html) für mehr Informationen.

* * *

### `isValidElement()` {#isvalidelement}

```javascript
React.isValidElement(object)
```

Überprüft, ob das Objekt ein React-Element ist. Gibt `true` oder `false` zurück.

* * *

### `React.Children` {#reactchildren}

`React.Children` bietet Hilfsmittel, um mit der eher undurchschaubaren Datenstruktur von `this.props.children` umzugehen.

#### `React.Children.map` {#reactchildrenmap}

```javascript
React.Children.map(children, function[(thisArg)])
```

Ruft für jedes unmittelbare Kind aus `children` eine Funktion auf, bei der `this` auf `thisArg` gesetzt ist. Wenn `children` ein Array ist, wird dieses durchlaufen und die Funktion wird für jedes Kind im Array aufgerufen. Falls `children` `null` oder `undefined` ist, gibt diese Methode statt einem Array `null` oder `undefined` zurück.

> Hinweis
>
> Falls `children` ein `Fragment` ist, wird es wie ein einziges Kind behandelt und nicht durchlaufen.

#### `React.Children.forEach` {#reactchildrenforeach}

```javascript
React.Children.forEach(children, function[(thisArg)])
```

Wie [`React.Children.map()`](#reactchildrenmap), gibt jedoch kein Array zurück.

#### `React.Children.count` {#reactchildrencount}

```javascript
React.Children.count(children)
```

Gibt die Gesamtzahl der Komponenten in `children` zurück, und gleicht der Anzahl der Callbacks, die mit der `map`- oder `forEach`-Methode aufgerufen werden würden.

#### `React.Children.only` {#reactchildrenonly}

```javascript
React.Children.only(children)
```

Überprüft, ob `children` nur ein Kind (ein React-Element) hat und gibt es zurück. Ansonsten wirft diese Methode einen Fehler.

> Hinweis:
>
>`React.Children.only()` akzeptiert nicht den Rückgabewert von [`React.Children.map()`](#reactchildrenmap), da dieser ein Array ist und kein React-Element.

#### `React.Children.toArray` {#reactchildrentoarray}

```javascript
React.Children.toArray(children)
```

Gibt die eher undurchschaubare `children`-Datenstruktur als flaches Array zurück, wobei jedem Kind ein Key zugeordnet ist. Nützlich, falls in render-Methoden Kinder-Ansammlungen manipuliert werden sollen, insbesondere beim Umordnen oder Slicen von `this.props.children`, bevor diese weitergegeben werden.

> Hinweis:
>
> `React.Children.toArray()` ändert Keys, um die Semantik verschachtelter Arrays zu erhalten, während Listen von Kindern geflattet werden. Das bedeutet, dass `toArray` ein Präfix vor jeden Key des zurückgegebenen Arrays setzt, damit der Key jedes Elements dem Input-Array, dem es angehört, zugeordnet ist.

* * *

### `React.Fragment` {#reactfragment}

Die `React.Fragment`-Komponente erlaubt dir, mehrere Elemente in einer `render()`-Methode zurückzugeben, ohne ein zusätzliches DOM-Element zu kreieren:

```javascript
render() {
  return (
    <React.Fragment>
      Irgendein Text.
      <h2>Eine Überschrift</h2>
    </React.Fragment>
  );
}
```

Du kannst auch die Kurzschreibweise `<></>` verwenden. Für mehr Informationen siehe [React v16.2.0: Verbesserte Unterstützung für Fragmente](/blog/2017/11/28/react-v16.2.0-fragment-support.html).

* * *

### `React.createRef` {#reactcreateref}

`React.createRef` erstellt eine [Referenz](./refs-and-the-dom.html), die über das `ref`-Attribut an React-Elemente angehängt werden kann.
`embed:16-3-release-blog-post/create-ref-example.js`

* * *

### `React.forwardRef` {#reactforwardref}

`React.forwardRef` erstellt eine React-Komponente, die das [ref](./refs-and-the-dom.html)-Attribut, das sie erhält, an eine ihr in der Baumstruktur untergeordnete Komponente weitergibt. Diese Technik ist nicht sehr verbreitet, ist jedoch in zwei Fällen besonders nützlich:

* [Refs an DOM-Komponenten weitergeben](./forwarding-refs.html#forwarding-refs-to-dom-components)
* [Refs and Higher-Order-Komponenten weitergeben](./forwarding-refs.html#forwarding-refs-in-higher-order-components)

`React.forwardRef` akzeptiert eine render-Funktion als Argument. React ruft diese Funktion mit den zwei Argumenten `props` und `ref` auf und sollte einen React-Knoten zurückgeben.

`embed:reference-react-forward-ref.js`

Im obigen Beispiel übergibt React eine `ref`, die dem `<FancyButton ref={ref}>`-Element gegeben wurde, als zweites Argument an die render-Funktion innerhalb des `React.forwardRef`-Aufrufs. Diese render-Funktion gibt die `ref` an das `<button ref={ref}>`-Element weiter.

Dadurch zeigt `ref.current` direkt auf die `<button>`-DOM-Element-Instanz, nachdem React die `ref` eingefügt hat.

Für mehr Informationen siehe [Refs weitergeben](./forwarding-refs.html).

* * *

### `React.lazy` {#reactlazy}

`React.lazy()` lässt dich eine Komponente definieren, die dynamisch geladen wird. Das hilft, die Bundlegröße zu reduzieren, indem das Laden von Komponenten, die im ursprünglichen Render nicht benutzt werden, verzögert wird.

In unserer [Code-Splitting-Dokumentation](./code-splitting.html#reactlazy) kannst du lernen, wie es benutzt wird. Du kannst auch [diesen Artikel](https://medium.com/@pomber/lazy-loading-and-preloading-components-in-react-16-6-804de091c82d) lesen, in dem die Verwendung im Detail erläutert wird.

```js
// Diese Komponente wird dynamisch geladen
const SomeComponent = React.lazy(() => import('./SomeComponent'));
```

Beachte, dass das Rendern von `lazy`-Komponenten ein `<React.Suspense>` weiter oben im Rendering-Baum benötigt. Damit wird ein Lade-Indikator bestimmt.

<<<<<<< HEAD
> **Hinweis**
>
> Die Verwendung von `React.lazy` mit dynamischen Imports setzt voraus, dass in der JS-Umgebung `Promises` verfügbar sind. Hierzu braucht man ein Polyfill für IE11 und darunter.

* * *

### `React.Suspense` {#reactsuspense}

`React.Suspense` lässt dich den Lade-Indikator bestimmen, der angezeigt wird, falls einige Komponenten weiter unten im Rendering-Baum noch nicht render-bereit sind. In the future we plan to let `Suspense` handle more scenarios such as data fetching. You can read about this in [our roadmap](/blog/2018/11/27/react-16-roadmap.html).

Momentan ist das Laden von `lazy`-Komponenten der **einzige** Anwendungsfall, den `<React.Suspense>` unterstützt:

```js
// Diese Komponente wird dynamisch geladen
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // Zeigt <Spinner>, bis OtherComponent geladen ist
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

Das ist in unserem [Code-Splitting-Guide](./code-splitting.html#reactlazy) dokumentiert. Beachte, dass sich `lazy`-Komponenten tief im `Suspense`-Baum befinden können -- es muss nicht jede einzelne davon ummantelt werden. Es wird empfohlen, `<Suspense>` dort zu verwenden, wo ein Lade-Indikator angezeigt werden soll, und `lazy(`) dort zu verwenden, wo Code-Splitting stattfinden soll.

> Note
>
> For content that is already shown to the user, switching back to a loading indicator can be disorienting. It is sometimes better to show the "old" UI while the new UI is being prepared. To do this, you can use the new transition APIs [`startTransition`](#starttransition) and [`useTransition`](./hooks-reference.html#usetransition) to mark updates as transitions and avoid unexpected fallbacks.

#### `React.Suspense` in Server Side Rendering {#reactsuspense-in-server-side-rendering}
During server side rendering Suspense Boundaries allow you to flush your application in smaller chunks by suspending.
When a component suspends we schedule a low priority task to render the closest Suspense boundary's fallback. If the component unsuspends before we flush the fallback then we send down the actual content and throw away the fallback.

#### `React.Suspense` during hydration {#reactsuspense-during-hydration}
Suspense boundaries depend on their parent boundaries being hydrated before they can hydrate, but they can hydrate independently from sibling boundaries. Events on a boundary before its hydrated will cause the boundary to hydrate at
a higher priority than neighboring boundaries. [Read more](https://github.com/reactwg/react-18/discussions/130)

### `React.startTransition` {#starttransition}

```js
React.startTransition(callback)
```
`React.startTransition` lets you mark updates inside the provided callback as transitions. This method is designed to be used when [`React.useTransition`](./hooks-reference.html#usetransition) is not available.

> Note:
>
> Updates in a transition yield to more urgent updates such as clicks.
>
> Updates in a transition will not show a fallback for re-suspended content, allowing the user to continue interacting while rendering the update.
>
> `React.startTransition` does not provide an `isPending` flag. To track the pending status of a transition see [`React.useTransition`](./hooks-reference.html#usetransition).
<span style="float: footnote;"><a href="./index.html#toc">Go to TOC</a></span>
