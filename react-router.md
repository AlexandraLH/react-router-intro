# React Router en bref

## À quoi ça sert ?

> Le React Router permet de gérer la navigation dans une application écrite avec React.

Le *lien hypertexte* est la fondation du web. Toutes les pages en comportent. Par défaut, le lien permet de passer d'une page à une autre (qui éventuellement est hébergée sur un serveur différent).

Dans le contexte des applications modernes, écrites en JavaScript, le comportement par défaut des liens doit être modifié : en effet, si on clique sur un lien, cela nous "sort" de la page actuelle, ce qui fait que tout l'état de l'application est perdu.

Le React Router permet :
- de modifier le comportement des liens, de façon à ne *pas sortir de la page* quand on clique sur un lien
- d'associer un certain composant à une certaine URL ou chemin.

React Router est une librairie tierce, qui ne fait pas partie de React elle-même. La [documentation](https://reacttraining.com/react-router/web/guides/quick-start) est bien fournie, avec de nombreux exemples.

## Exemples

Voici un premier exemple, que tu peux voir en action sur ce [CodeSandbox](https://codesandbox.io/s/nnk63ko7yj).

On va détailler un peu le code. D'abord, les imports :
```javascript
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter, Route, Link } from "react-router-dom";
```

En plus de React (et ReactDOM qui est utilisé habituellement dans `index.js`), on importe des éléments d'un troisième module : `react-router-dom` (qu'on aurait installé avec `npm install react-router-dom` dans une application créée localement avec `create-react-app`).

On va détailler un peu plus loin ce que sont `BrowserRouter`, `Route` et `Link`.

Ensuite, on crée deux composants, qu'on voudra afficher chacun sur une page. Ils sont écrits sous la forme la plus simple - fonction fléchée renvoyant directement du JSX :

```jsx
const Home = props => <h1>Home page</h1>;
const Profile = props => <h1>Profile page</h1>;
```

Ensuite vient le composant `App`, où nous allons utiliser `Link` et `Route`.
```jsx
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
```

D'abord une explication sur `Link` : dans une application React, on va remplacer tous les *liens internes* (liens renvoyant vers une autre page de notre application) par des `Link`. Au lieu de :

    <a href="/link/to/somewhere">Somewhere</a>
    
On aura (note l'emploi de l'attribut `to` au lieu de `href`) :

    <Link to="/link/to/somewhere">Somewhere</Link>

Ensuite, `Route` permet de définir... une route ! C'est à dire l'association entre un chemin/URL (`/`, `/profile`, `/contact-us`) et un composant.

`Route` reçoit au moins deux props :
* `path` qui définit un chemin à reconnaître
* `component` qui indique le composant à afficher lorsque ce chemin est reconnu
* `exact` (booléen, optionnel) qui spécifie que la correspondance entre le chemin dans la barre d'adresse et le chemin donné via `path` doit être *exacte*. Sans ce `exact`, la route `<Route path="/" ... />` correspond à tous les chemins, puisque tous les chemins contiennent le `/`.

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

