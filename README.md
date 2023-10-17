## Pourquoi React Router

Commence par créer un "bac à sable" React/JavaScript pour faire quelques expériences :

```bash
npm create vite@latest my-app-with-router
```

Lance ensuite les commandes indiquées dans ta console :

```bash
cd my-app-with-router
npm install
npm run dev
```

Ouvre le code dans ton IDE (`code .` ?). Prépare ensuite 2 fichiers `src/pages/Home.jsx` et `src/pages/About.jsx` (pense à créer le dossier `pages` dans `src`) avec des composants "Hello World" :

```jsx
// Home.jsx

function Home() {
  return <h1>Hello from Home</h1>;
}

export default Home;
```

Et :

```jsx
// About.jsx

function About() {
  return <h1>Hello from About</h1>;
}

export default About;
```

Enfin, remplace le contenu du fichier `src/App.jsx` avec ces lignes :

```jsx
import { useState } from "react";

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

Voici une explication plus détaillée :

- Composants de Page :

  - `Home` est un composant de page qui rend un élément `<h1>` avec le texte `Hello from Home`.
  - `About` est un autre composant de page qui rend un élément `<h1>` avec le texte `Hello from About`.

- Composant `App` :

  - `App` est le composant racine de l'application. Il utilise l'état local (géré avec `useState`) pour suivre la `currentLocation`, qui représente l'URL actuelle de la page.
  - Le composant `App` contient un élément `<nav>` avec deux boutons : "Home" et "About". Chaque bouton a un gestionnaire d'événements qui met à jour au clic la `currentLocation` en fonction de l'URL de la page correspondante.
  - Dans la section principale, `App` utilise une structure conditionnelle pour afficher le contenu approprié en fonction de la `currentLocation`. Si la `currentLocation` est `/`, le composant `<Home />` est rendu. Si la `currentLocation` est `/about`, le composant `<About />` est rendu.

Cette application simule une navigation très basique entre deux pages en utilisant un état local pour suivre l'URL de la page courante. Lorsque tu cliques sur les boutons "Home" ou "About", l'URL de la page est mise à jour en fonction du bouton sur lequel tu as cliqué, et le contenu de la page change en conséquence.

Cependant, cette approche est limitée et n'équivaut pas à une véritable gestion de la navigation, car elle ne modifie pas réellement l'URL du navigateur.
C'est là qu'intervient React Router, un outil qui facilite la gestion de la navigation dans une application React en synchronisant l'URL du navigateur avec les composants de page et en fournissant des fonctionnalités de routage plus avancées.
{: .alert-warning}

## Agir à la racine

Pour modifier réellement l'url du navigateur et avoir une vraie gestion de la navigation, nous allons mettre de côté `App.jsx` pour l'instant, et nous intéresser à `main.jsx` dont une version courte ressemble à ceci :

```jsx
import ReactDOM from "react-dom/client";
import App from "./App.jsx";

ReactDOM.createRoot(document.getElementById("root")).render(<App />);
```

C'est le point de départ de notre application où nous initialisons React et affichons le composant `App`. Voici ce qui se passe dans ce code :

```jsx
import ReactDOM from "react-dom/client";
```

Cette ligne importe le module `react-dom/client` dont nous utiliserons ensuite la méthode `createRoot`. `createRoot` permet de créer une "racine" à partir de laquelle un composant React va pouvoir être monté et affiché dans le DOM.

```jsx
import App from "./App.jsx";
```

Cette ligne importe le composant `App` que nous avons créé dans le fichier `App.jsx`. Ce composant sera rendu dans la suite du code.

```jsx
createRoot(document.getElementById("root")).render(<App />);
```

cette partie du code crée une nouvelle racine pour l'application (généralement un élément HTML avec l'ID `root`) en utilisant `createRoot`. Ensuite, la méthode `.render()` est appelée avec le composant `App` en tant que contenu à afficher. Cela signifie que le composant `App` est la racine de notre application React, et il sera rendu dans l'élément avec l'ID `root` dans le HTML.

En résumé, ce code initialise l'application React en utilisant le composant `App` comme point d'entrée, et il assure que l'application est rendue dans l'élément HTML avec l'ID `root`. C'est une étape importante pour démarrer une application React et lui permettre de gérer ses composants et sa logique. Et c'est exactement ce que nous allons casser 🙂
{: .alert-info}

## Sur la route

Nous allons décomposer ce flux de rendu en utilisant React Router pour gérer la navigation. React Router est une bibliothèque qui nous permet de définir des routes pour notre application React, ce qui signifie que nous pouvons associer des composants spécifiques à des URL particulières.

Avant toute chose, fais un `git init` et un premier commit de l'application : cela te permettra de revenir en arrière au besoin.
{: .alert-warning}

Installe dans ton projet `react-router-dom` (la version de React Router pour le DOM, le web) :

```bash
npm install react-router-dom
```

Et modifie ensuite `main.jsx` comme ceci :

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
          <Home />,
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
          <About />,
        </main>
      </>
    ),
  },
]);

// rendering

ReactDOM.createRoot(document.getElementById("root")).render(
  <RouterProvider router={router} />
);
```

Ce code illustre l'utilisation de React Router pour configurer des routes dans une application React.

Voici ce qui se passe dans ce code :

- Nous importons les modules nécessaires depuis React et React Router.
- Nous utilisons `createBrowserRouter` pour créer une instance de routeur. Nous lui passons un tableau d'objets, chaque objet représentant l'association d'un affichage spécifique (`element`) avec un chemin d'URL particulier (`path`). Dans notre exemple, il existe deux routes : `"/"` et `"/about"`.
- Pour la racine, nous utilisons la fonction `createRoot` pour créer un point d'ancrage dans le DOM où notre application React sera rendue. C'est là que nous remplaçons l'utilisation du composant `App` par un `RouterProvider`, en passant notre instance de routeur en tant que propriété.

Assure toi de relancer ton serveur avec `npm run dev`, et navigue entre les pages. Tu remarqueras que l'URL change vraiment dans ton navigateur et que les boutons "Page précédente" et "Page suivante" marchent aussi avec React Router.
{: .alert-info}

## Mais... et App ?

Dans notre code actuel, le composant `<App />` n'est _plus nécessaire_ comme racine de tous les composants.
Mais tu as certainement remarqué qu'entre nos 2 routes, beaucoup de choses se répètent dans l'affichage :

```jsx
// ...
element: (
  <>
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
    </nav>
    <main>
      <Home />,
    </main>
  </>
),
// ...
element: (
  <>
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
    </nav>
    <main>
      <About />,
    </main>
  </>
),
// ...
```

Le composant `<App />` peut encore nous être _utile_. Modifions le pour reprendre tout ce qui se répète entre nos routes :

```jsx
import { Link } from "react-router-dom";

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

Reste ce qui change : le contenu de `<main>` où nous avons pour l'instant des "???".

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
        path: "about",
        element: <About />,
      },
    ],
  },
]);

// rendering

ReactDOM.createRoot(document.getElementById("root")).render(
  <RouterProvider router={router} />
);
```

```jsx
import { Link, Outlet } from "react-router-dom";

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

## Plus de pages

segments dynamiques

## Au démarrage

useEffect / loader
