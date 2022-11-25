
# Composition ou héritage ?


React fournit un puissant modèle de composition, aussi nous recommandons d'utiliser la composition plutôt que l'héritage pour réutiliser du code entre les composants.

Dans cette section, nous examinerons quelques situations pour lesquelles les débutants en React ont tendance à opter pour l'héritage, et montrerons comment les résoudre à l'aide de la composition.

## Délégation de contenu {#containment}

Certains composants ne connaissent pas leurs enfants à l’avance. C’est particulièrement courant pour des composants comme `Sidebar` ou `Dialog`, qui représentent des blocs génériques.

Pour de tels composants, nous vous conseillons d’utiliser la prop spéciale `children`, pour passer directement les éléments enfants dans votre sortie :

```js{4}
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
```

Ça permet aux autres composants de leur passer des enfants quelconques en imbriquant le JSX :

```js{4-9}
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Bienvenue
      </h1>
      <p className="Dialog-message">
        Merci de visiter notre vaisseau spatial !
      </p>
    </FancyBorder>
  );
}
```

**[Essayer sur CodePen](https://codepen.io/gaearon/pen/ozqNOV?editors=0010)**

Tout ce qui se trouve dans la balise JSX `<FancyBorder>` est passé comme prop `children` au composant `FancyBorder`. Puisque `FancyBorder` utilise `{props.children}` dans une balise `<div>`, les éléments passés apparaissent dans la sortie finale.

Bien que cela soit moins courant, vous aurez parfois besoin de plusieurs « trous » dans un composant. Dans ces cas-là, vous pouvez créer votre propre convention au lieu d'utiliser `children` :

```js{5,8,18,21}
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```

[**Essayer sur CodePen**](https://codepen.io/gaearon/pen/gwZOJp?editors=0010)

Des éléments React tels que `<Contacts />` et `<Chat />` sont simplement des objets, vous pouvez les passer comme props au même titre que n'importe quelle autre donnée. Cette approche peut vous rappeler la notion de “slots” présente dans d'autres bibliothèques, mais il n'y a aucune limitation à ce que vous pouvez passer en props avec React.

## Spécialisation {#specialization}

Parfois, nous voyons nos composants comme des « cas particuliers » d'autres composants. Par exemple, nous pourrions dire que `WelcomeDialog` est un cas particulier de `Dialog`.

Avec React, on réalise aussi ça avec la composition ; un composant plus « spécialisé » utilise un composant plus « générique » et le configure grâce aux props : 

```js{5,8,16-18}
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Bienvenue"
      message="Merci de visiter notre vaisseau spatial !" />
  );
}
```

[**Essayer sur CodePen**](https://codepen.io/gaearon/pen/kkEaOZ?editors=0010)

La composition fonctionne tout aussi bien pour les composants à base de classe :

```js{10,27-31}
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
      {props.children}
    </FancyBorder>
  );
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSignUp = this.handleSignUp.bind(this);
    this.state = {login: ''};
  }

  render() {
    return (
      <Dialog title="Programme d'exploration de Mars"
              message="Comment devrions-nous nous adresser à vous ?">
        <input value={this.state.login}
               onChange={this.handleChange} />
        <button onClick={this.handleSignUp}>
          Inscrivez-moi !
        </button>
      </Dialog>
    );
  }

  handleChange(e) {
    this.setState({login: e.target.value});
  }

  handleSignUp() {
    alert(`Bienvenue à bord, ${this.state.login} !`);
  }
}
```

[**Essayer sur CodePen**](https://codepen.io/gaearon/pen/gwZbYa?editors=0010)

## Qu'en est-il de l'héritage ? {#so-what-about-inheritance}

Chez Facebook, nous utilisons React pour des milliers de composants, et nous n'avons pas encore trouvé de cas où nous aurions recommandé de créer des hiérarchies d'héritage de composants.

Les props et la composition vous donnent toute la flexibilité dont vous avez besoin pour personnaliser l'apparence  et le comportement d'un composant de manière explicite et sûre. Souvenez-vous qu’un composant peut accepter tout type de props, y compris des valeurs primitives, des éléments React et des fonctions.

Si vous souhaitez réutiliser des fonctionnalités sans rapport à l'interface utilisateur entre les composants, nous vous suggérons de les extraire dans un module Javascript séparé. Les composants pourront alors importer cette fonction, cet objet ou cette classe sans avoir à l'étendre.
<span style="float: footnote;"><a href="./index.html#toc">Go to TOC</a></span>
