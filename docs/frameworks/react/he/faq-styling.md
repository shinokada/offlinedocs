
# עיצוב ו-CSS


### איך אני מוסיף CSS classes לקומפוננטה שלי? {#how-do-i-add-css-classes-to-components}

העבר מחרוזת ל-prop `className`:

```jsx
render() {
  return <span className="menu navigation-menu">תפריט</span>
}
```

זה נפוץ ש-CSS classes תלויים ב-props או state של הקומפוננטה:

```jsx
render() {
  let className = 'menu';
  if (this.props.isActive) {
    className += ' menu-active';
  }
  return <span className={className}>תפריט</span>
}
```

>טיפ
>
>אם לעיתים קרובות אתה מוצא את עצמך כותב קוד כזה, ספריית [classnames](https://www.npmjs.com/package/classnames#usage-with-reactjs) יכולה לפשט זאת.

### האם אני יכול לכתוב inline עיצוב? {#can-i-use-inline-styles}

כן, ראה את התיעוד על עיצוב [כאן](./dom-elements.html#style).

### inline עיצוב הוא רע? {#are-inline-styles-bad}

CSS classes בדרך כלל טובים יותר לביצועים מאשר inline עיצוב.

### מה זה CSS ב-JS? {#what-is-css-in-js}

“CSS ב-JS” מתייחס לתבנית שבה CSS מורכב בעזרת JavaScript במקום להיות מוגדר בקובץ חיצוני.

_שים לב שהפונקציונאליות הזאת היא לא חלק מ-React, אך מסופקת מספריות צד שלישי._ ל-React אין דיעה על איך עיצוב מוגדר; אם יש ספק, נקודת התחלה טובה היא להגדיר את העיצוב שלך קובץ `*.css` חיצוני כרגיל לפנות אלייהם באמצעות [`className`](./dom-elements.html#classname).

### האם אני יכול לעשות אנימציות ב-React? {#can-i-do-animations-in-react}
ניתן להשתמש ב-React להנפשת אנימציות. ראה [React Transition Group](https://reactcommunity.org/react-transition-group/), [React Motion](https://github.com/chenglou/react-motion), [React Spring](https://github.com/react-spring/react-spring), או [Framer Motion](https://framer.com/motion) לדוגמא.
<span style="float: footnote;"><a href="./index.html#toc">Go to TOC</a></span>
