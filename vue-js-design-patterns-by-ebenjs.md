# 1- Container Pattern

Ce patron de conception prône le principle de responsabilité unique. Avec ce pattern le composant qui est chargé d'afficher et styler la vue à l'utilisateur est séparé de celui qui se charge de la logique métier.
Autrement dit les tâches sont déléguées à plusieurs composants.

Pour illustrer ce que nous disons, nous allons prendre un exemple très simple d'un projet minimaliste qui va chercher une liste de pokemons depuis une api externe et afficher cette liste à l'utilisateur.

Pour aboutir à un design découplé, nous allons utiliser deux composants :

- PokemonList.vue

    Ce composant se chargera de faire une requête vers une api externe pour récupérer la liste des pokemons et la passer comme prop à un second composant qui se chargera d'afficher la liste.

    Voici à quoi pourrait ressemble le code de ce composant:

    ```javascript
    
        <template>
            <div>
                <PokemonListRender :pokemons="pokemons" />
            </div>
        </template>

        <script setup>
            import { ref, onMounted } from 'vue';

            const pokemons = ref([]);

            const fetchPokemons = async () => {
                try {
                    const response = await fetch('https://pokeapi.co/api/v2/pokemon');
                    const data = await response.json();
                    pokemons.value = data.results;
                } catch (error) {
                    console.error('Error fetching Pokémon data:', error);
                }
            };

            onMounted(fetchPokemons);
        </script>

     
    ```

- PokemonListRender.vue
    Ce composant quant à lui, se chargera d'afficher la liste des pokemons et d'y appliquer le style voulu. 

    ```javascript
    
        <template>
            <h1>Pokémon List</h1>
            <ul>
                <li v-for="pokemon in pokemons" :key="pokemon.id">
                    {{ pokemon.name }}
                </li>
            </ul>
        </template>

        <script setup>
            import { defineProps } from 'vue';

            const props = defineProps({
                pokemons: {
                    type: Array,
                    required: true
                }
            });
        </script>
        
        <style>
            <!-- Component styles goes here -->
        </style>

    ```

L'avantage principal de ce patron est la séparation des composants. L'un se charge de la logique et l'autre de l'affichage et du design.

Le projet complet et le code source se trouve à l'adresse : https://github.com/ebenjs/pokemon.git

# 2- Provide Inject or Dependency Injection

Dans nos divers projets VueJS, il arrive souvent que nous ayons besoin de partager des données entre un  composant parent et un composant enfant. Pour ce faire, nous avons plusieurs options à notre disposition. Nous pouvons utiliser les props, les events, les slots, les refs, etc. Mais il existe une autre option qui est moins connue et qui est très utile dans certains cas. Il s'agit de l'option provide/inject.

Imaginez que vous vous retrouvez avec une application qui a plusieurs composants imbriqués les uns dans les autres. Et que vous avez besoin de partager des données entre le composant parent et le composant enfant le plus bas dans l'arborescence. Si vous voulez utiliser les props, vous allez devoir passer les données à travers tous les composants intermédiaires ( Ce problème est même connu sous le nom de `props drilling` ). Ce qui n'est pas très pratique. C'est là que l'option provide/inject entre en jeu.

Concrètement le patron de conception provide/inject consiste à déclarer des données dans un composant parent et à les rendre disponibles pour tous les composants enfants. Pour ce faire, il faut utiliser la propriété provide dans le composant parent et la propriété inject dans le composant enfant.

Voici un example d'utilisation tiré de la documentation officielle de VueJS:

```javascript

    // Parent
    <script setup>
        import { provide } from 'vue'

        provide(/* key */ 'message', /* value */ 'hello!')
    </script>

```

Comme vous pouvez le voir, nous avons utilisé la fonction provide pour déclarer une variable message et lui assigner la valeur 'hello!'. Cette variable sera disponible pour tous les composants enfants.


```javascript

    // Child
    <script setup>
        import { inject } from 'vue'

        const message = inject('message')
    </script>

```
Pour accéder à la variable message dans le composant enfant, nous avons utilisé la fonction inject. Cette fonction prend en paramètre la clé de la variable à récupérer. Dans notre cas, la clé est 'message'.

Le patron provide/inject est très utile dans certains cas. Mais il faut faire attention à ne pas l'utiliser à tout bout de champ. Il est recommandé de l'utiliser uniquement pour des données qui sont vraiment partagées entre plusieurs composants.

# 3- Higher Order Component

# 4- Async Components

Dans les applications de grande taille, il peut être nécessaire de diviser l'application en petits morceaux et de ne charger un composant du serveur que lorsqu'il est nécessaire. Pour rendre cela possible dans VueJS, nous avons la possibilité de créer des composants asynchrones.

Voici ci-dessous un exemple de composant asynchrone:

```javascript

    import { defineAsyncComponent } from 'vue'

    const AsyncComponent = defineAsyncComponent(() =>
        import('./components/AsyncComponent.vue')
    )

```

la variable `AsyncComponent` est un composant asynchrone. Elle est définie à l'aide de la fonction defineAsyncComponent. Cette fonction prend en paramètre une fonction qui retourne une promesse. Dans notre cas, la fonction retourne une promesse qui importe le composant AsyncComponent.vue.

Pour utiliser le composant asynchrone, il suffit de l'importer et de l'utiliser comme n'importe quel autre composant.

```javascript

    <template>
        <AsyncComponent />
    </template>

    <script setup>
        import { defineAsyncComponent } from 'vue'

        const AsyncComponent = defineAsyncComponent(() =>
            import('./components/AsyncComponent.vue')
        )
    </script>

```
L'utilisation des composants asynchrones est très utile dans les applications de grande taille. Elle permet de ne charger que les composants nécessaires et d'améliorer ainsi les performances de l'application.
