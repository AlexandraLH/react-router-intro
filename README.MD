# React Router en bref

## À quoi ça sert ?

> Le React Router permet de gérer la navigation dans une application écrite avec React.

Le *lien hypertexte* est la fondation du web. Toutes les pages en comportent. Par défaut, le lien permet de passer d'une page à une autre (qui éventuellement est hébergée sur un serveur différent).

Dans le contexte des applications modernes, écrites en JavaScript, le comportement par défaut des liens doit être modifié : en effet, si on clique sur un lien, cela nous "sort" de la page actuelle, ce qui fait que tout l'état de l'application est perdu.

Le React Router permet :
- de modifier le comportement des liens, de façon à ne *pas sortir de la page* quand on clique sur un lien
- d'associer un certain composant à une certaine URL ou chemin.

React Router est une librairie tierce, qui ne fait pas partie de React elle-même. La [documentation](https://reacttraining.com/react-router/web/guides/quick-start) est bien fournie, avec de nombreux exemples.

## Exemple 1 - routage simple

Voici un premier exemple, que tu peux voir en action sur ce [CodeSandbox](https://codesandbox.io/s/nnk63ko7yj).

```jsx
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter, Route, Link } from "react-router-dom";

import "./styles.css";

const Home = props => <h1>Home page</h1>;

const Profile = props => <h1>Profile page</h1>;

function App() {
  return (
    <div className="App">
      <Link to="/">Home</Link>
      <Link to="/profile">Profile</Link>
      <Route path="/" exact component={Home} />
      <Route path="/profile" exact component={Profile} />
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  rootElement
);
```

On va détailler le code, partie par partie.

**Les imports**

```javascript
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter, Route, Link } from "react-router-dom";
```

En plus de React (et ReactDOM qui est utilisé habituellement dans `index.js`), on importe des éléments d'un troisième module : `react-router-dom` (qu'on aurait installé avec `npm install react-router-dom` dans une application créée localement avec `create-react-app`).

On va détailler un peu plus loin ce que sont `BrowserRouter`, `Route` et `Link`.

**Deux pages, deux composants**

Ensuite, on crée deux composants, qu'on affichera sur des pages distinctes. Ils sont écrits sous la forme la plus simple - fonction fléchée renvoyant directement du JSX :

```jsx
const Home = props => <h1>Home page</h1>;
const Profile = props => <h1>Profile page</h1>;
```

**App, les routes et les liens**

Ensuite vient le composant `App`, où nous allons utiliser `Link` et `Route`.
```jsx
function App() {
  return (
    <div className="App">
      <nav>
        <Link to="/">Home</Link>
        <Link to="/profile">Profile</Link>
      </nav>
      <Route path="/" exact component={Home} />
      <Route path="/profile" exact component={Profile} />
    </div>
  );
}
```

D'abord une explication sur les `Link` qui se trouvent sous la `nav` : dans une application React, on va remplacer tous les *liens internes* (liens renvoyant vers une autre page de notre application) par des `Link`. Au lieu de :

    <a href="/link/to/somewhere">Somewhere</a>
    
On aura (note l'emploi de l'attribut `to` au lieu de `href`) :

    <Link to="/link/to/somewhere">Somewhere</Link>

> L'utilisation de `Link` au lieu d'un lien "classique" est **indispensable**. C'est en effet le `Link` qui permet de modifier le comportement par défaut du lien, de façon à ne *pas sortir de la page*.

Ensuite, `Route` permet de définir... une route ! C'est à dire l'association entre un chemin/URL (`/`, `/profile`, `/contact-us`) et un composant.

`Route` reçoit au moins deux props :
* `path` qui définit un chemin à reconnaître
* `component` qui indique le composant à afficher lorsque ce chemin est reconnu
* `exact` (booléen, optionnel) qui spécifie que la correspondance entre le chemin dans la barre d'adresse et le chemin donné via `path` doit être *exacte*. Sans ce `exact`, la route `<Route path="/" ... />` correspond à tous les chemins, puisque tous les chemins contiennent le `/`.

**`BrowserRouter`**

Enfin, on va voir comment et où utiliser `BrowserRouter` : cela se passe tout à la fin, pour afficher l'application React. Dans une "vraie" application, on importerait `BrowserRouter` depuis `index.js` uniquement.

```jsx
const rootElement = document.getElementById("root");
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  rootElement
);
```

Dans l'appel à `ReactDOM.render`, on entoure `<App />` avec une paire de balises `<BrowserRouter>...</BrowserRouter>`. Sans cela, on ne peut pas utiliser le routeur.

## Exemple 2 - routage avec paramètres

Dans une application ou un site web, chaque URL renvoie vers une ressource unique. Mais on peut distinguer certaines différences entre :
* d'une part, des pages dont l'URL est "fixe", par exemple : `/contact-us`, `/about`, `/our-mission`.
* d'autre part, des pages dont l'URL a une partie variable. Exemples :
  * Sur un journal, l'URL d'un article peut comporter la catégorie à laquelle il appartient, ainsi qu'une chaîne identifiant l'article de façon unique : `/articles/fun-science/how-to-make-your-own-dynamite`, `/articles/politics/debate-trump-vs-sponge-bob`
  * Sur un site de covoiturage, l'URL peut comporter une chaîne identifiant un trajet publié de façon unique : `/rides/198727-rennes-nantes`, `/rides/236045-lyon-toulouse`.

Dans une URL, on appelle *segment* chaque partie se trouvant entre deux `/`. On peut donc avoir des segments "fixes" (comme `articles` et `rides` dans les exemples ci-dessus), et des segments variables (la catégorie et le titre/identifiant de l'article, ou l'identifiant d'un trajet).

Voici un deuxième exemple, que tu peux voir en action sur ce [CodeSandbox](https://codesandbox.io/s/k0rox64kov).

```jsx
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter, Route, Link } from "react-router-dom";

import "./styles.css";

const Home = props => <h1>Home page</h1>;
const Profile = props => <h1>{props.match.params.name}'s profile page</h1>;

function App() {
  return (
    <div className="App">
      <nav>
        <Link to="/">Home</Link>
        <Link to="/profile/Homer">Homer's profile</Link>
        <Link to="/profile/Marge">Marge's profile</Link>
      </nav>
      <Route path="/" exact component={Home} />
      <Route path="/profile/:name" exact component={Profile} />
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  rootElement
);
```

On va détailler le code, en mettant évidence les points communs et les différences avec l'exemple précédent.

**Points communs**
* Les imports restent identiques
* La partie qui effectue le rendu dans le DOM, tout à la fin, reste également identique

**Changements**

Les changements se situent à trois endroits :
* En premier lieu, la prop `path` passée à `Route` pour afficher le profil, contient désormais deux "segments" ; un qui est fixe : `profile`, et un variable : `:name`. Les chemins suivants correspondent à ce `path` : `/profile/Homer`, `/profile/Marge`, `/profile/Lisa`, etc. On ne sait simplement pas à l'avance ce qui se trouvera derrière `/profile/`, on spécifie donc qu'on s'attend à trouver "quelque chose" à cet endroit, et qu'on veut le stocker dans un "paramètre" `name`.
* Il y a deux `Link` pointant vers les profils d'Homer et Marge. Ces liens vont tous les deux correspondre à la route `/profile/:name`.
* Dans le composant `Profile`, on récupère la valeur du "paramètre" `name`. Ce n'est pas à proprement parler une variable : on le récupère via une des props : `props.match.params.name`. Alors, rien que cette ligne donne mal à la tête, on va donc détailler ça un peu plus !

**Les paramètres d'URL**

Le composant placé sous une `Route` de React Router reçoit d'office trois `props` :
* `location`
* `match`
* `history`

`match` est un objet contenant des informations sur la route active. Il contient notamment un attribut `params` qui est lui-même un objet, contenant les valeurs récupérées pour les segments d'URL variables. Par exemple, `props.match.params` vaut `{ name: "Homer" }` si on visite le profil d'Homer, et  `{ name: "Marge" }` si on visite celui de Marge.
