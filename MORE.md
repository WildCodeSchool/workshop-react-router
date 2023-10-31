[revoir l'épisode précédent](./)

## Au clic

Dans une application React, il est souvent nécessaire d'effectuer des **actions spécifiques** au moment du **chargement initial de la page**. Cette étape peut être cruciale pour initialiser des données, récupérer des informations à partir de services externes...

Repartons sur notre page `Home`&nbsp;:

```jsx
function Home() {
  return <h1>Hello from Home</h1>;
}

export default Home;
```

Modifions le code pour exécuter des instructions (la fonction `getWeatherOfTheDay` dans cet exemple) au clic d'un bouton&nbsp;:

```jsx
import { useState } from "react";

const getWeatherOfTheDay = () => {
  return "sunny";
};

function Home() {
  const [weather, setWeather] = useState(null);

  return (
    <>
      <h1>Hello from Home</h1>

      {weather != null ? (
        <p>Today is a {weather} day</p>
      ) : (
        <button
          type="button"
          onClick={() => {
            const weatherOfTheDay = getWeatherOfTheDay();

            setWeather(weatherOfTheDay);
          }}
        >
          Get Weather
        </button>
      )}
    </>
  );
}

export default Home;
```

La fonction `getWeatherOfTheDay` retourne directement une chaine de caractères.
Elle pourrait faire des opérations plus complexes comme appeler une API par exemple.
Ce n'est pas qui ce nous intéresse aujourd'hui&nbsp;: ce qui nous intéresse à ce stade c'est nous les récupérons les données _au moment du clic_.

Dans cette version, nous sommes limités par les événements du DOM&nbsp;: il ne se passera rien tant que la personne qui visite la page n'aura pas cliqué sur le bouton.
Parfois (voir même souvent...), tu auras besoin de déclencher du code dès le "démarrage" de ta page.
Une approche courante pour effectuer des instructions au démarrage de la page consiste à utiliser le hook `useEffect`.
{: .alert-info}

## Au démarrage

Dans l'exemple ci-dessus, nous avons introduit une fonction `getWeatherOfTheDay` qui peut être utilisée pour récupérer les informations météo.
Plutôt que d'attendre une interaction de l'utilisateur, nous pouvons utiliser `useEffect` pour exécuter cette fonction automatiquement&nbsp;:

```jsx
import { useEffect, useState } from "react";

const getWeatherOfTheDay = () => {
  return "sunny";
};

function Home() {
  const [weather, setWeather] = useState(null);

  useEffect(() => {
    const weatherOfTheDay = getWeatherOfTheDay();

    setWeather(weatherOfTheDay);
  }, []);

  return (
    <>
      <h1>Hello from Home</h1>

      {weather != null && <p>Today is a {weather} day</p>}
    </>
  );
}

export default Home;
```

Voici comment cela fonctionne&nbsp;:

- Nous utilisons le hook `useEffect` en fournissant **une fonction en tant que premier argument**. Cette fonction contient les instructions que nous souhaitons exécuter au démarrage de la page. Ce hook permet ainsi d'effectuer des actions au moment où un composant est monté dans le DOM.

L'action est toujours exécutée **après** un premier rendu "à vide".
{: .alert-warning}

- Nous appelons `getWeatherOfTheDay` à l'intérieur de cette fonction pour récupérer les données souhaitées&nbsp;: dans ce cas, la météo.
- Les données sont ensuite utilisées pour mettre à jour l'état du composant en utilisant `setWeather`. **Cela déclenche un rendu de la page** avec les données mises à jour.
- Le **tableau de dépendances (deuxième argument de `useEffect`)** est vide, ce qui signifie que cette fonction d'effet s'exécute une seule fois au démarrage de la page. Cela garantit que ces instructions ne sont pas exécutées à chaque mise à jour du composant.

L'utilisation de useEffect est idéale pour effectuer des actions au démarrage, mais React Router apporte un concurrent sérieux&nbsp;: les loaders.
{: .alert-info}

## Le plus tôt possible

Une autre approche pour exécuter des instructions au démarrage de la page est d'utiliser React Router avec des **loaders**.
Les loaders sont des fonctions spéciales que tu peux associer à des routes pour charger des données **avant le rendu de la page**.
Cette méthode permet d'obtenir des données dès que possible, ce qui peut être essentiel pour une expérience utilisateur fluide.

Pour illustrer cette approche, reprenons notre route `Home` dans `main.jsx`:

```jsx
// ...

const getWeatherOfTheDay = () => {
  return "sunny";
};

// router creation

const router = createBrowserRouter([
  {
    element: <App />,
    children: [
      {
        path: "/",
        element: <Home />,
        loader: () => {
          return getWeatherOfTheDay();
        },
      },
      // ...
    ],
  },
]);

// ...
```

Dans ce cas, nous utilisons un loader pour charger les données météo avant le rendu de la page. Voici comment cela fonctionne&nbsp;:

- Nous bougeons la fonction `getWeatherOfTheDay` qui, comme précédemment, peut être utilisée pour récupérer les informations météo.
- Lors de la création du routeur de l'application à l'aide de `createBrowserRouter`, nous définissons un loader pour la route `"/"` (la page d'accueil). Ce loader est associé à la fonction `getWeatherOfTheDay`, qui récupère les données météo.

Dans le composant Home&nbsp;:

```jsx
import { useLoaderData } from "react-router-dom";

function Home() {
  const weather = useLoaderData();

  return (
    <>
      <h1>Hello from Home</h1>
      <p>Today is a {weather} day</p>
    </>
  );
}

export default Home;
```

Nous utilisons le hook `useLoaderData` pour récupérer les données du loader.
Cela garantit que les données sont disponibles dès le rendu de la page (plus besoin de rendu conditionnel pour éviter une valeur nulle au premier affichage).

Une alternative intéressante est d'utiliser un loader "global".
Au lieu d'associer le loader directement à la route de la page, nous pouvons définir un loader au niveau de l'application (note aussi l'ajout d'un `id` sur la route de l'application)&nbsp;:

```jsx
// ...

// router creation

const router = createBrowserRouter([
  {
    element: <App />,
    loader: () => {
      const weather = "sunny";

      return weather;
    },
    id: "app",
    children: [
      {
        path: "/",
        element: <Home />,
      },
      // ...
    ],
  },
]);

// ...
```

Cela signifie que les données sont chargées dès le démarrage de l'application et sont disponibles pour **toutes les pages**.
Les données chargées globalement peuvent être accessibles dans n'importe quelle partie de l'application, et nous pouvons les récupérer à l'aide du hook `useRouteLoaderData` en spécifiant l'identifiant du loader ciblé (dans ce cas, `"app"`)&nbsp;:

```jsx
import { useRouteLoaderData } from "react-router-dom";

function Home() {
  const weather = useRouteLoaderData("app");

  return (
    <>
      <h1>Hello from Home</h1>
      <p>Today is a {weather} day</p>
    </>
  );
}

export default Home;
```

Cette approche des loaders est puissante car elle permet de charger des données importantes en amont, ce qui peut améliorer considérablement les performances de l'application et l'expérience utilisateur.
Cela garantit également que les données sont disponibles dès que possible, ce qui est particulièrement important pour les applications nécessitant des données initiales avant tout rendu.

## Recharger la page

Lorsqu'il s'agit de mettre à jour des données ou d'effectuer des actions en réponse à des changements dans l'application, React offre plusieurs approches.
L'utilisation de `useEffect` et les loaders de React Router sont là encore 2 options très intéressantes.

Revenons cette fois sur notre page `Article`&nbsp;:

```jsx
import { useParams } from "react-router-dom";

function Article() {
  const { id } = useParams();

  return <h1>Hello from Article {id}</h1>;
}

export default Article;
```

Un moyen courant de gérer les mises à jour dans un composant React est d'utiliser le hook `useEffect`.
Il permet d'effectuer **des actions en réponse à des changements spécifiques**, tels que des changements d'état ou des modifications de propriétés&nbsp;:

```jsx
import { useEffect, useState } from "react";
import { useParams } from "react-router-dom";

const getSomeData = (id) => {
  const allData = {
    42: {
      title: "Lorem Ipsum",
      content: "Lorem ipsum dolor sit amet",
    },
    123: {
      title: "Schnapsum",
      content: "Lorem Elsass ipsum Salut bisamme",
    },
    666: {
      title: "Cupcake Ipsum",
      content: "Tiramisu pastry wafer brownie soufflé",
    },
  };

  return allData[id];
};

function Article() {
  const { id } = useParams();

  const [data, setData] = useState(null);

  useEffect(() => {
    const someData = getSomeData(id);

    setData(someData);
  }, [id]);

  return (
    data != null && (
      <article>
        <h1>{data.title}</h1>
        <p>{data.content}</p>
      </article>
    )
  );
}

export default Article;
```

Dans cet exemple, le composant `Article` affiche les détails d'un article en fonction de son identifiant.
Le hook `useParams` est utilisé pour récupérer l'identifiant de l'article à partir de l'URL.
Ensuite, nous utilisons le hook `useEffect` pour effectuer une action chaque fois que l'identifiant change.

Le fameux **tableau de dépendances** en deuxième argument de `useEffect` est utilisé pour **surveiller les changements de `id`**.
Lorsque `id` change (par exemple, en naviguant vers un autre article), `useEffect` appelle `getSomeData` pour récupérer les nouvelles données et les affiche dans le composant.

Une autre approche pour gérer les mises à jour consiste à utiliser les loaders de React Router&nbsp;:

```jsx
// ...

const getSomeData = (id) => {
  const allData = {
    42: {
      title: "Lorem Ipsum",
      content: "Lorem ipsum dolor sit amet",
    },
    123: {
      title: "Schnapsum",
      content: "Lorem Elsass ipsum Salut bisamme",
    },
    666: {
      title: "Cupcake Ipsum",
      content: "Tiramisu pastry wafer brownie soufflé",
    },
  };

  return allData[id];
};

// router creation

const router = createBrowserRouter([
  {
    element: <App />,
    children: [
      // ...
      {
        path: "/articles/:id",
        element: <Article />,
        loader: ({ params }) => {
          return getSomeData(params.id);
        },
      },
    ],
  },
]);

// ...
```

Dans cet exemple, un loader est associé à la route `"/articles/:id"`.
Le loader est déclenché automatiquement chaque fois qu'un utilisateur accède à une URL correspondant à cette route.
De manière similaire aux props des composants, `params` est extrait du premier paramètre de la fonction, et nous donne accès à l'id dans l'URL.

Le résultat du loader (les données de l'article) est ensuite disponible pour le composant `Article` via le hook `useLoaderData` comme vu précédemment&nbsp;:

```jsx
import { useLoaderData } from "react-router-dom";

function Article() {
  const data = useLoaderData();

  return (
    <article>
      <h1>{data.title}</h1>
      <p>{data.content}</p>
    </article>
  );
}

export default Article;
```

## useEffect vs loader

Maintenant que nous avons examiné deux approches pour gérer les mises à jour dans une application React, il est important de comprendre les avantages et les inconvénients de chaque méthode.

### useEffect

Avantages&nbsp;:

- **Flexibilité&nbsp;:** useEffect permet de réagir à divers types de changements, tels que les modifications d'état ou les mises à jour de props.
- **Contrôle total&nbsp;:** Vous avez un contrôle total sur les actions effectuées en réponse aux changements.

Inconvénients&nbsp;:

- **Difficulté à gérer le chargement initial&nbsp;:** Pour gérer le chargement initial de manière efficace, des rendus conditionnels ou des états spéciaux sont souvent nécessaires.
- **Peut entraîner des rendus inutiles&nbsp;:** useEffect peut être déclenché plusieurs fois pour un même changement, ce qui peut provoquer des rendus inutiles.

### Loaders de React Router

Avantages&nbsp;:

- **Préchargement des données&nbsp;:** Les loaders de React Router permettent de précharger les données avant le rendu, améliorant ainsi les performances et l'expérience utilisateur.
- **Gestion automatique du chargement initial&nbsp;:** Les loaders sont déclenchés automatiquement lors de l'activation de la route, ce qui facilite la gestion du chargement initial.
- **Structure claire&nbsp;:** Les loaders sont associés aux routes correspondantes, ce qui rend la logique de chargement plus claire et structurée.

Inconvénients&nbsp;:

- **Moins de flexibilité&nbsp;:** Les loaders sont conçus pour le chargement de données lors du changement de route, ce qui les limite à ce contexte. Si vous avez besoin de réagir à des changements plus variés, useEffect peut être plus adapté.

Le choix entre `useEffect` et les loaders dépend de ton cas d'utilisation spécifique. Le mieux reste de demander des conseils pour être sûr de toi.
