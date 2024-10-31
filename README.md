## Pourquoi React Router

Commence par créer un "bac à sable" React/TypeScript pour faire quelques expériences (choisis bien `React` et `TypeScript` à la création du projet)&nbsp;:

```bash
npm create vite@latest my-app-with-router
```

Lance ensuite les commandes indiquées dans ta console sans la commande `npm run dev`&nbsp;:

```bash
cd my-app-with-router
npm install
```

Ouvre le code dans ton IDE (`code .` ?). Prépare ensuite 2 fichiers `src/pages/Home.tsx` et `src/pages/About.tsx` (pense à créer le dossier `pages` dans `src`) avec des composants "Hello World"&nbsp;:

```jsx
// Home.tsx

function Home() {
  return <h1>Hello from Home</h1>;
}

export default Home;
```

Et&nbsp;:

```jsx
// About.tsx

function About() {
  return <h1>Hello from About</h1>;
}

export default About;
```

Enfin, remplace le contenu du fichier `src/App.tsx` avec ces lignes&nbsp;:

```jsx
import { useState } from "react";

import "./App.css";

// page components

import Home from "./pages/Home";
import About from "./pages/About";

// the App

function App() {
  const [currentLocation, setCurrentLocation] = useState("/");

  return (
    <>
      <nav>
        <button onClick={() => setCurrentLocation("/")} type="button">
          Home
        </button>
        <button onClick={() => setCurrentLocation("/about")} type="button">
          About
        </button>
      </nav>
      <main>
        {currentLocation === "/" && <Home />}
        {currentLocation === "/about" && <About />}
      </main>
    </>
  );
}

export default App;
```

Cette mini-application est une démonstration qui utilise React pour créer deux composants de page, `Home` et `About`.
L'application principale, `App`, gère la navigation entre ces deux composants de page à l'aide d'un état local (`currentLocation`) et de deux boutons dans la barre de navigation.

Tu peux la voir tourner sur ta machine avec la commande `npm run dev`.

Voici une explication plus détaillée&nbsp;:

- Composants de Page&nbsp;:

  - `Home` est un composant de page qui rend un élément `<h1>` avec le texte `Hello from Home`.
  - `About` est un autre composant de page qui rend un élément `<h1>` avec le texte `Hello from About`.

- Composant `App`&nbsp;:

  - `App` est le composant racine de l'application. Il utilise l'état local (géré avec `useState`) pour suivre la `currentLocation`, qui représente l'URL actuelle de la page.
  - Le composant `App` contient un élément `<nav>` avec deux boutons&nbsp;: "Home" et "About". Chaque bouton a un gestionnaire d'événements qui met à jour au clic la `currentLocation` en fonction de l'URL de la page correspondante.
  - Dans la section principale, `App` utilise une structure conditionnelle pour afficher le contenu approprié en fonction de la `currentLocation`. Si la `currentLocation` est `/`, le composant `<Home />` est rendu. Si la `currentLocation` est `/about`, le composant `<About />` est rendu.

Cette application simule une navigation très basique entre deux pages en utilisant un état local pour suivre l'URL de la page courante. Lorsque tu cliques sur les boutons "Home" ou "About", l'URL de la page est mise à jour en fonction du bouton sur lequel tu as cliqué, et le contenu de la page change en conséquence.

Cependant, cette approche est limitée et n'équivaut pas à une véritable gestion de la navigation, car elle ne modifie pas réellement l'URL du navigateur.
C'est là qu'intervient React Router, un outil qui facilite la gestion de la navigation dans une application React en synchronisant l'URL du navigateur avec les composants de page et en fournissant des fonctionnalités de routage plus avancées.
{: .alert-warning}

## Agir à la racine

Pour modifier réellement l'url du navigateur et avoir une vraie gestion de la navigation, nous allons mettre de côté `App.tsx` pour l'instant, et nous intéresser à `main.tsx` dont une version allégée ressemble à ceci&nbsp;:

Ce qui suit est une presentation du fonctionnement du fichier `main.tsx`&nbsp;: tu n'as pas besoin de le modifier.
{: .alert-warning}

```jsx
import ReactDOM from "react-dom/client";
import App from "./App.tsx";

ReactDOM.createRoot(document.getElementById("root")).render(<App />);
```

C'est le point de départ de notre application où nous initialisons React et affichons le composant `App`. Voici ce qui se passe dans ce code&nbsp;:

```jsx
import ReactDOM from "react-dom/client";
```

Cette ligne importe le module `react-dom/client` dont nous utiliserons ensuite la méthode `createRoot`. `createRoot` permet de créer une "racine" à partir de laquelle un composant React va pouvoir être monté et affiché dans le DOM.

```jsx
import App from "./App.tsx";
```

Cette ligne importe le composant `App` que nous avons créé dans le fichier `App.tsx`. Ce composant sera rendu dans la suite du code.

```jsx
createRoot(document.getElementById("root")).render(<App />);
```

cette partie du code crée une nouvelle racine pour l'application (généralement un élément HTML avec l'ID `root`) en utilisant `createRoot`. Ensuite, la méthode `.render()` est appelée avec le composant `App` en tant que contenu à afficher. Cela signifie que le composant `App` est la racine de notre application React, et il sera rendu dans l'élément avec l'ID `root` dans le HTML.

En résumé, ce code initialise l'application React en utilisant le composant `App` comme point d'entrée, et il assure que l'application est rendue dans l'élément HTML avec l'ID `root`. C'est une étape importante pour démarrer une application React et lui permettre de gérer ses composants et sa logique. Et c'est exactement ce que nous allons casser 🙂
{: .alert-info}

## Sur la route

Nous allons décomposer ce flux de rendu en utilisant React Router pour gérer la navigation. React Router est une bibliothèque qui nous permet de définir des routes pour notre application React, ce qui signifie que nous pouvons associer des composants spécifiques à des URL particulières.

Avant toute chose, fais un `git init` et un premier commit de l'application&nbsp;: cela te permettra de revenir en arrière au besoin.
{: .alert-warning}

Installe dans ton projet `react-router-dom` (la version de React Router pour le DOM, le web)&nbsp;:

```bash
npm install react-router-dom
```

Et modifie ensuite `main.tsx` comme ceci&nbsp;:

```jsx
import ReactDOM from "react-dom/client";
import { createBrowserRouter, Link, RouterProvider } from "react-router-dom";

// page components

import Home from "./pages/Home";
import About from "./pages/About";

// router creation

const router = createBrowserRouter([
  {
    path: "/",
    element: (
      <>
        <nav>
          <Link to="/">Home</Link>
          <Link to="/about">About</Link>
        </nav>
        <main>
          <Home />
        </main>
      </>
    ),
  },
  {
    path: "/about",
    element: (
      <>
        <nav>
          <Link to="/">Home</Link>
          <Link to="/about">About</Link>
        </nav>
        <main>
          <About />
        </main>
      </>
    ),
  },
]);

// rendering

const rootElement = document.getElementById("root");

if (rootElement != null) {
  ReactDOM.createRoot(rootElement).render(
    <RouterProvider router={router} />
  );
}
```

Ce code illustre l'utilisation de React Router pour configurer des routes dans une application React.

Voici ce qui se passe dans ce code&nbsp;:

- Nous importons les modules nécessaires depuis React et React Router.
- Nous utilisons `createBrowserRouter` pour créer une instance de routeur. Nous lui passons un tableau d'objets, chaque objet représentant l'association d'un affichage spécifique (`element`) avec un chemin d'URL particulier (`path`). Dans notre exemple, il existe deux routes `"/"` et `"/about"`&nbsp;:

```jsx
/* ... */
{
  path: "/",
  element: (/* ... */),
},
{
  path: "/about",
  element: (/* ... */),
},
/* ... */
```

- Pour la racine, nous utilisons la fonction `createRoot` pour créer un point d'ancrage dans le DOM où notre application React sera rendue. C'est là que nous remplaçons l'utilisation du composant `App` par un `RouterProvider`, en passant notre instance de routeur en tant que propriété.

Assure toi de relancer ton serveur avec `npm run dev`, et navigue entre les pages. Tu remarqueras que l'URL change vraiment dans ton navigateur et que les boutons "Page précédente" et "Page suivante" marchent aussi avec React Router.
{: .alert-info}

## Mais... et App ?

Dans notre code actuel, le composant `<App />` n'est plus _nécessaire_ comme racine de tous les composants.
Mais tu as certainement remarqué qu'entre nos 2 routes, beaucoup de choses se répètent dans l'affichage&nbsp;:

```jsx
/* ... */
{
  path: "/",
  element: (
    <>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <main>
        <Home />
      </main>
    </>
  ),
},
{
  path: "/about",
  element: (
    <>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <main>
        <About />
      </main>
    </>
  ),
},
/* ... */
```

Le composant `<App />` peut encore nous être _utile_. Modifions le pour reprendre ce qui est commun à toutes nos routes&nbsp;:

```jsx
import { Link } from "react-router-dom";

import "./App.css";

function App() {
  return (
    <>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <main>???</main>
    </>
  );
}

export default App;
```

Reste ce qui change&nbsp;: le contenu de `<main>` où nous avons pour l'instant des "???".
À ce stade, nous avons configuré les routes principales pour `"/"` et `"/about"`, mais il est possible d'aller plus loin en _imbriquant_ des routes.
Cela signifie que nous pouvons organiser notre application de manière hiérarchique, où chaque élément peut avoir ses propres routes internes.

Pour illustrer cette idée, voici une nouvelle version du code&nbsp;:

```jsx
import ReactDOM from "react-dom/client";
import { createBrowserRouter, RouterProvider } from "react-router-dom";

import App from "./App";

// page components

import Home from "./pages/Home";
import About from "./pages/About";

// router creation

const router = createBrowserRouter([
  {
    element: <App />,
    children: [
      {
        path: "/",
        element: <Home />,
      },
      {
        path: "/about",
        element: <About />,
      },
    ],
  },
]);

// rendering

const rootElement = document.getElementById("root");

if (rootElement != null) {
  ReactDOM.createRoot(rootElement).render(
    <RouterProvider router={router} />
  );
}
```

Nous reprenons ici `App` comme le composant "principal"&nbsp;: sans `path` précisé, l'élément sera toujours affiché. De plus, nous ajoutons des enfants (`children`) à `App` pour gérer les routes spécifiques à `Home` et `About`.

Dans la version d'origine, sans React Router, cette hiérarchie existait déjà. Rappelle toi le contenu initial de `App`&nbsp;:

```jsx
function App() {
  const [currentLocation, setCurrentLocation] = useState("/");

  return (
    <>
      <nav>
        <button onClick={() => setCurrentLocation("/")} type="button">
          Home
        </button>
        <button onClick={() => setCurrentLocation("/about")} type="button">
          About
        </button>
      </nav>
      <main>
        {currentLocation === "/" && <Home />}
        {currentLocation === "/about" && <About />}
      </main>
    </>
  );
}
```

Les composants `Home` et `About` étaient déjà des enfants de l'application&nbsp;: ils étaient imbriqués dans l'affichage de `App` en fonction de l'état courant.
Selon la valeur de cet état (`currentLocation`), nous pouvions choisir explicitement quel composant afficher&nbsp;:

- `<Home />` pour `"/"`
- `<About />` pour `"/about"`

Au lieu de spécifier explicitement quel composant doit être affiché dans `<main>`, nous pouvons utiliser un outil très pratique de React Router&nbsp;: `<Outlet />`.

```jsx
import { Link, Outlet } from "react-router-dom";

import "./App.css";

function App() {
  return (
    <>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <main>
        <Outlet />
      </main>
    </>
  );
}

export default App;
```

L'élément `<Outlet />` va automatiquement être remplacé grâce à React Router par **l'élément de la route active**. Dans notre cas&nbsp;:

- `<Home />` pour `"/"`
- `<About />` pour `"/about"`

Le même résultat qu'à l'origine, mais c'est React Router qui fait le travail 😉

Nous avons ici "recyclé" `App` pour en faire un _layout_&nbsp;: une mise en page réutilisable par différentes routes.
Une application plus complexe pourrait gérer différent layouts pour différentes sections d'un site par exemple.
Mais nous allons nous en tenir dans cet atelier à un layout unique.
{: .alert-info}

## Plus de pages

Jusqu'à présent, nous avons vu comment configurer des routes _statiques_ pour des pages comme `"/"` et `"/about"`.
Cependant, dans de nombreuses applications, les URL contiennent des informations _dynamiques_, comme des identifiants d'utilisateurs, des noms de produits ou d'autres données variables.
Pour gérer ces cas, React Router nous offre une fonctionnalité appelée **"segment dynamique"**.

Les segments dynamiques nous permettent de créer des **routes flexibles** qui acceptent des **valeurs variables dans l'URL**, ce qui rend notre application capable de traiter une grande variété de cas d'utilisation.

Prenons un exemple concret pour comprendre comment fonctionnent les segments dynamiques.
Imaginons que nous ayons une **application de blog** et que nous souhaitions afficher les **détails de chaque article en fonction de son identifiant**.
Plutôt que de créer une route statique pour chaque article, nous pouvons utiliser un segment dynamique pour **capturer l'identifiant de l'article directement depuis l'URL**.

Pense à faire un commit de tes dernières modifs avant de changer ton code.
{: .alert-warning}

Créé un nouveau composant `Article` dans un nouveau fichier `src/pages/Article.tsx`&nbsp;:

```jsx
function Article() {
  return <h1>Hello from Article</h1>;
}

export default Article;
```

Et modifie `src/main.tsx` (les autres routes ont été supprimées pour te faciliter la lecture)&nbsp;:

```jsx
import ReactDOM from "react-dom/client";
import { createBrowserRouter, RouterProvider } from "react-router-dom";

// page components

import Article from "./pages/Article";

// router creation

const router = createBrowserRouter([
  {
    path: "/articles/:id",
    element: <Article />,
  },
]);

// rendering

const rootElement = document.getElementById("root");

if (rootElement != null) {
  ReactDOM.createRoot(rootElement).render(
    <RouterProvider router={router} />
  );
}
```

Dans cet exemple, la route `"/articles/:id"` est configurée avec un segment dynamique `:id`&nbsp;: ce sont les `:` qui indiquent que le segment est dynamique.
Cela signifie que l'URL `/articles/123` correspondra à cette route, où `123` est un exemple d'identifiant d'article.
Grâce à ce segment dynamique, nous pouvons extraire l'identifiant de l'article directement depuis l'URL et l'utiliser pour afficher les détails de l'article correspondant.

React Router nous fournit encore tous les outils nécessaires. Cette fois, c'est le hook `useParams` qui va nous aider dans le composant `Article`&nbsp;:

```jsx
import { useParams } from "react-router-dom";

function Article() {
  const { id } = useParams();

  return <h1>Hello from Article {id}</h1>;
}

export default Article;
```

Le rôle de `useParams` est de nous retourner toutes les valeurs des segments dynamiques depuis l'URL.
Toutes les valeurs&nbsp;: même s'il n'y en a qu'une (c'est notre cas ici), `useParams()` nous renverra toujours un objet avec chaque valeur associée à son nom.

Nous aurions pu écrire&nbsp;:

```jsx
const allValuesForTheDynamicSegments = useParams();
const id = allValuesForTheDynamicSegments.id;
```

La ligne&nbsp;:

```jsx
const { id } = useParams();
```

Est une version plus courte qui utilise la déstructuration de l'objet retourné par `useParams`.

Dans notre cas, il contient `id`, car c'est le nom du segment dynamique dans `"/articles/:id"`.
{: .alert-info}

Tu peux remettre toutes les routes dans `src/main.tsx`&nbsp;:

```jsx
import ReactDOM from "react-dom/client";
import { createBrowserRouter, RouterProvider } from "react-router-dom";

import App from "./App";

// page components

import Home from "./pages/Home";
import About from "./pages/About";
import Article from "./pages/Article";

// router creation

const router = createBrowserRouter([
  {
    element: <App />,
    children: [
      {
        path: "/",
        element: <Home />,
      },
      {
        path: "/about",
        element: <About />,
      },
      {
        path: "/articles/:id",
        element: <Article />,
      },
    ],
  },
]);

// rendering

const rootElement = document.getElementById("root");

if (rootElement != null) {
  ReactDOM.createRoot(rootElement).render(
    <RouterProvider router={router} />
  );
}
```

Et ajouter des liens dans `App`&nbsp;:

```jsx
import { Link, Outlet } from "react-router-dom";

import "./App.css";

function App() {
  return (
    <>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/articles/1">Article 1</Link>
        <Link to="/articles/2">Article 2</Link>
        <Link to="/articles/3">Article 3</Link>
      </nav>
      <main>
        <Outlet />
      </main>
    </>
  );
}

export default App;
```

Tu peux maintenant tester tes nouvelles pages, et faire tes propres expériences avec des segments dynamiques.

# STOP

Cela fait déjà pas mal de choses. Laisse toi le temps de digérer tout ça avant de [passer à la suite](MORE).
