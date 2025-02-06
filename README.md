# Projet Spacex

## 🚀 Contexte du Projet

Ce projet a été réalisé dans le cadre **d’un test technique** pour une candidature à un stage en développement web avec **Vue.js**. L’objectif était de concevoir une application permettant d’afficher dynamiquement les lancements de **SpaceX** en utilisant **l’API officielle SpaceX v5** disponible [**ici**](https://github.com/r-spacex/SpaceX-API).

## 🛠️ **Technologies utilisées**

-   [**Vue.js 3**](https://vuejs.org/) avec [**Composition API**](https://vuejs.org/guide/extras/composition-api-faq.html)
-   [**TypeScript**](https://www.typescriptlang.org/) pour une gestion des types robuste
-   [**Tailwind CSS**](https://tailwindcss.com/) pour le design et la mise en page
-   [**Fetch API**](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) pour la récupération des données SpaceX
-   [**Vite**](https://vitejs.dev/) pour le développement et le build optimisé

## 📥 **Récupération et installation du projet**

1) Cloner le projet depuis GitHub
````bash
git clone https://github.com/FabioEtoile/spacex-app.git
````

````bash
cd spacex-app
````

2) Installer les dépendances
````bash
npm install
````
3) Lancer le projet
````bash
npm run dev
````

Et ouvrir **http://localhost:5173** dans votre navigateur.

4) Lancer le build 
````bash
npm run build
````
## **Sources des données**

Les données des lancements sont récupérées depuis l’API officielle de SpaceX :

-   **Prochain lancement** : [`https://api.spacexdata.com/v5/launches/next`](https://api.spacexdata.com/v5/launches/next)
-   **Lancements passés** : [`https://api.spacexdata.com/v5/launches/past`](https://api.spacexdata.com/v5/launches/past)