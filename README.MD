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
* En premier lieu, la prop `path` passée à `Route` pour afficher le profil, contient désormais deux "segments" ; un qui est fixe : `profile`, et un variable : `:name`. Les chemins suivants correspondent à ce `path` : `/profile/Homer`, `/profile/Marge`, `/profile/Lisa`, etc. On ne sait pas à l'avance ce qui se trouvera derrière `/profile/`, on spécifie juste qu'on s'attend à trouver un paramètre à cet endroit : c'est pourquoi on indique un nom de paramètre, ici `name`, précédé de `:`. On peut trouver plusieurs paramètres dans un même `path`.
* Il y a deux `Link` pointant vers les profils d'Homer et Marge. Ces liens vont tous les deux correspondre à la route `/profile/:name`.
* Dans le composant `Profile`, on récupère la valeur du "paramètre" `name`. Ce n'est pas à proprement parler une variable : on le récupère via une des props : `props.match.params.name`. Alors, rien que cette ligne donne mal à la tête, on va donc détailler ça un peu plus !

**Les paramètres d'URL**

Le composant placé sous une `Route` de React Router reçoit d'office trois `props` :
* `location`
* `match`
* `history`

`match` est un objet contenant des informations sur la route active. Il contient notamment un attribut `params` qui est lui-même un objet, contenant les valeurs récupérées pour les segments d'URL variables. Par exemple, `props.match.params` vaut `{ name: "Homer" }` si on visite le profil d'Homer, et  `{ name: "Marge" }` si on visite celui de Marge.

On pourrait avoir un `path` plus complexe. Par exemple, voici deux URL d'articles du journal Le Monde :
* https://www.lemonde.fr/pixels/article/2018/06/04/microsoft-acquiert-pour-7-5-milliards-de-dollars-la-plateforme-de-developpement-de-logiciels-github_5309439_4408996.html
* https://www.lemonde.fr/sciences/article/2019/04/04/une-baleine-prehistorique-a-quatre-pattes-proche-de-la-loutre-decouverte-au-perou_5445927_1650684.html

Ces deux URL ont une forme similaire :
* l'URL racine du site (https://lemonde.fr),
* suivie de la catégorie (`pixels` ou `sciences` ici),
* suivie du mot-clé `article` (a priori fixe),
* suivi des année, mois, jour,
* suivis d'une longue chaîne contenant :
    * le titre converti en caractères URL-compatibles : en minuscules, caractères accentués convertis en caractères non-accentués, espaces et caractères spéciaux convertis en `-` voire supprimés - ce qu'on appelle couramment un *slug*,
    * deux numéros, formant probablement un identifiant unique pour l'article dans une base de données

Si on devait écrire une version React de ce journal, on aurait probablement une "route" qui ressemblerait à ceci :

```jsx
<Route path="/:category/article/:year/:month/:day/:slugAndId" component={Article} />
```

Dans le composant `Article`, on pourrait accéder aux paramètres de l'URL via `props.match.params` (si c'est une fonction) ou via `this.props.match.params` (si c'est une classe).

Pour le premier article, cet objet serait le suivant :

```javascript
{
  category: 'pixels',
  year: '2018',
  month: '06',
  day: '04',
  slugAndId: 'une-baleine-prehistorique-a-quatre-pattes-proche-de-la-loutre-decouverte-au-perou_5445927_1650684.html'
}
```

## Exemple 3 - utilisation des paramètres du path pour appeler une API

Le plus souvent, les segments variables du `path` (précédés d'un `:`) vont être utilisés pour faire un appel paramétré à une API.

Pour donner un peu de contexte à notre exemple, on va écrire une application qui permet :
1. de lister des profils GitHub (page d'accueil, composant `ProfileList`),
2. d'avoir des détails sur un profil particulier (composant `SingleProfile`).

### Données renvoyées par l'API

Voici à quoi ressemblent les données qu'on obtient en appelant l'URL https://api.github.com/users qui renvoie des utilisateurs (on a coupé le tableau pour n'en afficher que 2 sur les 30 obtenus) :

```json
[
  {
    "login": "mojombo",
    "id": 1,
    "node_id": "MDQ6VXNlcjE=",
    "avatar_url": "https://avatars0.githubusercontent.com/u/1?v=4",
    "gravatar_id": "",
    "url": "https://api.github.com/users/mojombo",
    "html_url": "https://github.com/mojombo",
    "followers_url": "https://api.github.com/users/mojombo/followers",
    "following_url": "https://api.github.com/users/mojombo/following{/other_user}",
    "gists_url": "https://api.github.com/users/mojombo/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/mojombo/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/mojombo/subscriptions",
    "organizations_url": "https://api.github.com/users/mojombo/orgs",
    "repos_url": "https://api.github.com/users/mojombo/repos",
    "events_url": "https://api.github.com/users/mojombo/events{/privacy}",
    "received_events_url": "https://api.github.com/users/mojombo/received_events",
    "type": "User",
    "site_admin": false
  },
  {
    "login": "defunkt",
    "id": 2,
    "node_id": "MDQ6VXNlcjI=",
    "avatar_url": "https://avatars0.githubusercontent.com/u/2?v=4",
    "gravatar_id": "",
    "url": "https://api.github.com/users/defunkt",
    "html_url": "https://github.com/defunkt",
    "followers_url": "https://api.github.com/users/defunkt/followers",
    "following_url": "https://api.github.com/users/defunkt/following{/other_user}",
    "gists_url": "https://api.github.com/users/defunkt/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/defunkt/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/defunkt/subscriptions",
    "organizations_url": "https://api.github.com/users/defunkt/orgs",
    "repos_url": "https://api.github.com/users/defunkt/repos",
    "events_url": "https://api.github.com/users/defunkt/events{/privacy}",
    "received_events_url": "https://api.github.com/users/defunkt/received_events",
    "type": "User",
    "site_admin": false
  }
]
```

Voici maintenant ce que renvoie l'API GitHub si on interroge les détails d'un utilisateur particulier - pour ce faire, on concatène son login à l'URL précédente, ce qui nous donne par exemple https://api.github.com/users/defunkt :

```json
{
  "login": "defunkt",
  "id": 2,
  "node_id": "MDQ6VXNlcjI=",
  "avatar_url": "https://avatars0.githubusercontent.com/u/2?v=4",
  "gravatar_id": "",
  "url": "https://api.github.com/users/defunkt",
  "html_url": "https://github.com/defunkt",
  "followers_url": "https://api.github.com/users/defunkt/followers",
  "following_url": "https://api.github.com/users/defunkt/following{/other_user}",
  "gists_url": "https://api.github.com/users/defunkt/gists{/gist_id}",
  "starred_url": "https://api.github.com/users/defunkt/starred{/owner}{/repo}",
  "subscriptions_url": "https://api.github.com/users/defunkt/subscriptions",
  "organizations_url": "https://api.github.com/users/defunkt/orgs",
  "repos_url": "https://api.github.com/users/defunkt/repos",
  "events_url": "https://api.github.com/users/defunkt/events{/privacy}",
  "received_events_url": "https://api.github.com/users/defunkt/received_events",
  "type": "User",
  "site_admin": false,
  "name": "Chris Wanstrath",
  "company": null,
  "blog": "http://chriswanstrath.com/",
  "location": null,
  "email": null,
  "hireable": null,
  "bio": "🍔",
  "public_repos": 107,
  "public_gists": 273,
  "followers": 20695,
  "following": 210,
  "created_at": "2007-10-20T05:24:19Z",
"updated_at": "2019-02-15T18:05:47Z"
}
```

Il est important de comprendre :
- Qu'un appel permettant d'obtenir plusieurs éléments d'un certain type nous renvoie un *tableau d'objets*.
- Qu'un appel permettant d'obtenir les détails d'un seul élément nous renvoie un *objet* (souvent avec plus d'informations)

### Composant `App`

Voici comment on pourrait écrire notre application, en commençant par le composant `App`, dans lequel on va définir les routes :

```jsx
// App.js
import React from 'react';
import { Link, Route } from 'react-router-dom';
import ProfileList from './ProfileList';
import SingleProfile from './SingleProfile';
import './App.css';

const App = () => (
  <div className="App">
    <nav>
      <Link to="/">Home</Link>
    </nav>
    
    <Route path="/" exact component={ProfileList} />
    <Route path="/profile/:login" exact component={SingleProfile} />
  </div>
);

export default App;
```

La `Route` permettant d'accéder aux détails d'un utilisateur a pour path `/profile/:login`, on devra donc utiliser des liens comme `/profile/gaearon` ou `/profile/torvalds`.

### Composant `ProfileList`

Voici le composant `ProfileList`, qui va afficher une liste de profils :

```jsx
// ProfileList.js
import React, { Component } from 'react';
import { Link } from 'react-router-dom';

class ProfileList extends Component {
  constructor(props) {
    super(props);
    // Initialize an empty array in the state,
    // to store users fetched from the API
    this.state = {
      users: []
    };
  }

  // This is a lifecycle method. It's part of the
  // methods provided by class React.Component
  // It is run right after the FIRST render only,
  // and is the best place to send API calls from.
  componentDidMount() {
    fetch('https://api.github.com/users')
      .then(res => res.json())
      .then(usersList => this.setState({
        users: usersList
      }));
  }

  render() {
    return (
      <div className="ProfileList">
        {
          this.state.users.map(user => (
            <section>
              <h3>{user.login}</h3>
              <img src={user.avatar_url} alt={user.login} />
              <Link to={`/profile/${user.login}`}>View {user.login}'s details</Link>
            </section>
          ))
        } 
      </div>
    );
  }
}

export default ProfileList;
```

Dans le constructeur, on initialise un tableau d'`users` vide. De cette façon, on est sûr que `this.state.users.map` fonctionnera dans le `render` (sinon, on pourrait avoir des erreurs !).

Des précisions s'imposent, si tu vois `componentDidMount()` pour la première fois (c'est l'objet d'une quête dédiée). Son nom précise son utilité : il s'agit d'une méthode héritée de `React.Component`, appelée juste une fois, après le premier rendu d'un composant. (*component did mount* signifie "le composant vient d'être inséré).

C'est l'endroit habituel d'où on peut effectuer des appels API, lorsqu'on veut que ceux-ci soient lancés de façon automatique.

Dans le `render`, on crée un lien en utilisant le `Link` du routeur, pour chacun des utilisateurs. Son attribut `to` est construit en concaténant `/profile` et l'identifiant d'un utilisateur (`/profile/mojombo`, `/profile/defunkt`).

### Composant `SingleProfile`

Ce composant va beaucoup ressembler à `ProfileList`. C'est ici (précisément dans `componentDidMount`) qu'on va récupérer le paramètre `login` défini dans la deuxième route de `App`. Il nous permet d'appeler l'API GitHub, en insérant ce paramètre dans l'URL qu'on appelle. 

```jsx
// SingleProfile.js
import React, { Component } from 'react';

class SingleProfile extends Component {
  constructor(props) {
    super(props);
    // Initialize a user key as `null`
    // This is a way to tell that we are expecting an object,
    // but we don't have its value yet
    this.state = {
      user: null
    };
  }

  // This is where we access the login parameter,
  // from the this.props.match.params object
  // We append its value to the base URL, in order
  // to call the appropriate URL on the GitHub API
  componentDidMount() {
    const userLogin = this.props.match.params.login;
    const url = `https://api.github.com/users/${userLogin}`;
    fetch(url)
      .then(res => res.json())
      .then(userData => this.setState({
        user: userData
      }));
  }

  render() {
    // In the render, we have to be careful not to try and
    // use attributes from this.state.user before the API calls
    // return and sets its value. This is why we use conditional
    // rendering with &&
    const user = this.state.user;
    return (
      <div className="SingleProfile">
        {
          user && (
            <section>
              <img src={user.avatar_url} alt={user.login} />
              <div>
                <h3>{user.login}</h3>
                <p>Real name: {user.name}</p>
                <p>Blog:
                  <a target="_blank" href={user.blog}>
                    {user.blog}
                  </a>
                </p>
                <p>Location: {user.location}</p>
              </div>
            </section>
          )
        } 
      </div>
    );
  }
}

export default SingleProfile;
```

Voici enfin à quoi ressemblerait `index.js` (rien de nouveau par rapport aux exemples précédents) :

```jsx
// index.js
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter } from "react-router-dom";
import App from './App';

const rootElement = document.getElementById("root");
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  rootElement
);
```

La démo fonctionnelle est accessible sur ce [CodeSandbox](https://codesandbox.io/s/xpxmq2pw5z).