# Passage de props aux composants de route

<<<<<<< HEAD
Utiliser `$route` dans vos composants crée un couplage fort à la route qui va limiter la flexibilité du composant qui ne pourra être utilisé que par certains URL.

Pour découpler un composant de son routeur, utilisez les props :

**❌ Couplé avec `$route`**
=======
Using `$route` in your component creates a tight coupling with the route which limits the flexibility of the component as it can only be used on certain URLs.

To decouple this component from the router use option `props`:

**❌ Coupled to `$route`**
>>>>>>> upstream/dev

``` js
const User = {
  template: '<div>Utilisateur {{ $route.params.id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/utilisateur/:id', component: User }
  ]
})
```

<<<<<<< HEAD
**👍 Découplé avec les props**
=======
**👍 Decoupled with `props`**
>>>>>>> upstream/dev

``` js
const User = {
  props: ['id'],
  template: '<div>Utilisateur {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/utilisateur/:id', component: User, props: true }

<<<<<<< HEAD
    // pour les routes avec vues nommées, vous devez définir l'option `props` pour chaque vue nommée :
=======
    // for routes with named views, you have to define the `props` option for each named view:
>>>>>>> upstream/dev
    {
      path: '/utilisateur/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }
    }
  ]
})
```

Cela vous permet d'utiliser le composant n'importe où, ce qui le rend plus facile à réutiliser et à tester.

### Mode booléen

<<<<<<< HEAD
Quand `props` est mis à `true`, le `route.params` est remplis en tant que props du composant.
=======
When `props` is set to `true`, the `route.params` will be set as the component props.
>>>>>>> upstream/dev

### Mode objet

<<<<<<< HEAD
Quand `props` est un objet, cela alimente les props de celui-ci. Utile quand les props sont statiques.
=======
When `props` is an object, this will be set as the component props as-is. Useful for when the props are static.
>>>>>>> upstream/dev

``` js
const router = new VueRouter({
  routes: [
    { path: '/promotion/from-newsletter', component: Promotion, props: { newsletterPopup: false } }
  ]
})
```

### Mode fonction

<<<<<<< HEAD
Vous pouvez créer une fonction qui va retourner les props. Cela vous permet de coercer un paramètre dans un autre type, de combiner les valeurs statiques avec les valeurs des routes, etc.
=======
You can create a function that returns props. This allows you to cast parameters into other types, combine static values with route-based values, etc.
>>>>>>> upstream/dev

``` js
const router = new VueRouter({
  routes: [
    { path: '/search', component: SearchUser, props: (route) => ({ query: route.query.q }) }
  ]
})
```

<<<<<<< HEAD
L'URL `/search?q=vue` passerait `{query: 'vue'}` comme `props` au composant `SearchUser`.

Essayez de garder la fonction de `props` sans état, car il n'est évalué que sur les changements de route. Utilisez un composant englobant si vous avez besoin d'état pour définir les props, ainsi la vue pourra réagir au changement d'état.
=======
The URL `/search?q=vue` would pass `{query: 'vue'}` as props to the `SearchUser` component.

Try to keep the `props` function stateless, as it's only evaluated on route changes. Use a wrapper component if you need state to define the props, that way vue can react to state changes.
>>>>>>> upstream/dev

Pour une utilisation avancée, jettez un œil à cet [exemple](https://github.com/vuejs/vue-router/blob/dev/examples/route-props/app.js).
