
# דרישות סביבת JavaScript


<<<<<<< HEAD
React 16 תלוי באוסף הטיפוסים [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) ו-[Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set). אם אתה תומך בדפדפנים ישנים יותר ומכשירים אשר עדיין לא מספקים אותם (לדוגמה, IE < 11) או אשר אין להם מימושים תואמים (IE 11), שקול להוסיף polyfill גלובלי לאפליקציה הארוזה שלך, כמו [core-js](https://github.com/zloirock/core-js).

סביבת polyfilled ל-React 16 עם שימוש ב-core-js כדי לתמוך בדפדפנים ישנים עשוייה להראות כך:
=======
React 18 supports all modern browsers (Edge, Firefox, Chrome, Safari, etc).

If you support older browsers and devices such as Internet Explorer which do not provide modern browser features natively or have non-compliant implementations, consider including a global polyfill in your bundled application.
>>>>>>> 84ad3308338e2bb819f4f24fa8e9dfeeffaa970b

Here is a list of the modern features React 18 uses:
- [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [`Symbol`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
- [`Object.assign`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

<<<<<<< HEAD
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```

React גם תלוי ב-`requestAnimationFrame` (אפילו בסביבות בדיקה).  
אתה יכול להשתמש בספריית [raf](https://www.npmjs.com/package/raf) כדי לתמוך ב-`requestAnimationFrame`:

```js
import 'raf/polyfill';
```
=======
The correct polyfill for these features depend on your environment. For many users, you can configure your [Browserlist](https://github.com/browserslist/browserslist) settings. For others, you may need to import polyfills like [`core-js`](https://github.com/zloirock/core-js) directly.
>>>>>>> 84ad3308338e2bb819f4f24fa8e9dfeeffaa970b
<span style="float: footnote;"><a href="./index.html#toc">Go to TOC</a></span>
