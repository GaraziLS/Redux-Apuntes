## Intro

Redux es una librería encargada de gestionar el estado de una aplicación. Almacena el estado y también lo actualiza. Para utilizar redux hay que instalar la dependencia. Abriendo la terminal, escribimos ``npm i redux``. Para conectarlo con React se necesita ``react-redux``, y para esto hay que instalar ``npm i react-redux``

Vamos a construir una app que simule una tienda de videojuegos. Cuando un usuario compre algo se retirará del almacén, y cuando devuelva algo se añadirá al mismo.

En redux hay que tener presentes tres nombres: 

* ***Actions***: Representa un concepto. Por ejemplo, la compra de un producto, el incremento o decremento de un número, etc. La acción de comprar, por ejemplo, se manda al store. Éste "avisa" a los reducers para que actualicen el estado. En este ejemplo, el reducer vende un producto, así que lo resta del store.

* ***Reducers***: Ordenan el estado global. Serían como los trabajadores de la tienda, que van ordenando el almacén. Un reducer podría mantener el orden de unas imágenes, por ejemplo, y otro almacenar algoritmos.

* ***Store***: Funciona como almacén global del estado. Sería como el almacén de la tienda, ya que contiene los datos.

## Crear store y reducers

Para crear el store crearemos una constante, pero antes hay que importar redux/react-redux:

```
@import { createStore } from "redux"
```

Para crear el store, escribimos:

```
const store = createStore()
```

El store necesita trabajadores (reducers), así que vamos a crear uno. El reducer debe tener un estado inicial. Cuando se inicie el programa esos datos se cargarán.

Vamos a crear el estado inicial:

```
const default_games_state = {
    pokemon: 10
}
```

Ahora crearemos el reducer. Son funciones anónimas que devuelven objetos, y reciben como argumento el estado inicial y una acción. Cuando se ejecute la acción se va a enviar al reducer, y un switch case se encargará de *repartir* los datos. Como no tenemos nada, devolveremos el estado actual por defecto.

Dentro de switch colocaremos ``action.type``. Cada acción tiene un tipo, así que switch actuará según cada caso especificado.

```
const games_reducer = (state = default_games_state, action) => {
    switch(action.type) {
        default: return state
    }
}
```

Ahora hay que enviar el reducer al store:

```
const store = createStore(games_reducer);
```

Dentro del store hay una serie de funciones importantes. Una de ellas, ``getState``, nos permite saber el estado:

```
const store = createStore(games_reducer);
console.log("Estado inicial", store.getState())
```

Esto devuelve el estado. Cuando alguien compra un juego, el store envía un evento de actualización, aunque para eso hay que suscribirse al store primero. Por cada cambio que se realice, un console.log se irá actualizando.

```
const store = createStore(games_reducer);
console.log("Estado inicial", store.getState())
store.subscribe() => {
    console.log("Cambio de estado", store.getState())
}
```

El store tiene que repartir esos nuevos datos, y esto lo hace con una función llamada ``dispatch``. Recibe la acción como parámetro, que llega al reducer. Reducer va a saber qué hacer porque tiene todos los casos de acción especificados en la sentencia switch. Cuando el estado se actualice, el subscribe() del store / boletín de la empresa se actualizará.

```
const store = createStore(games_reducer);
console.log("Estado inicial", store.getState())
store.subscribe() => {
    console.log("Cambio de estado", store.getState())
}
store.dispatch()
```

## Crear acciones y dispatch

Para poder pasar la acción por el dispatch hay que crear la acción primero. No es más que una función que regresa un objeto. También debemos crear una constante que guardará el tipo de acción. A esta función le pasamos, en este caso, la cantidad de objetos que compramos, teniendo en cuenta que debemos crear un **return** que devuelva un **objeto**. Y dentro del objeto indicamos el tipo de acción que es, que remite a la constante que hemos creado arriba. También especificamos la cantidad que compramos (payload).

```
const BUY_POKEMON = "BUY_POKEMON"

const buy_pokemon_action = (cantidad) => {
    return {
        type: BUY_POKEMON,
        payload: cantidad
    }
}
```

Esta acción será enviada al store, que pasará la información a  los reducers, que actuarán según el tipo de acción. Así que hay que listarles los casos posibles. Para eso copiaremos el tipo de la acción y el proceso que queremos ejecutar, en este caso retornar el estado actual. Esto se consigue restando al estado (**state, que tiene la constante guardada de default_games_state**) el payload.

```
const games_reducer = (state = default_games_state, action) => {
    switch(action.type) {
        case: BUY_POKEMON {
            return {
                pokemon: state.pokemon - action.payload
            }
        }
        default: return state
    }
}
```

Ahora tenemos que pasar el nombre de la acción por dispatch, y le pasamos la cantidad:

```
store.dispatch(buy_pokemon_action(3))
```

Ahora el estado refleja 8 items.

## Múltiples acciones

Si quisiéramos devolver el juego que hemos comprado necesitaríamos otra constante.

```
const BUY_POKEMON = "BUY_POKEMON"
const RETURN_POKEMON = "RETURN_POKEMON"
```

Como tenemos un nuevo tipo de acción, tenemos que crear su propia acción.

```
const buy_pokemon_action = (cantidad) => {
    return {
        type: BUY_POKEMON,
        payload: cantidad
    }
}

const return_pokemon_action = (cantidad) => {
    return {
        type: RETURN_POKEMON,
        payload: cantidad
    }
}
```

Ahora bajamos al reducer y agregamos otro case en la sentencia switch:

```
const games_reducer = (state = default_games_state, action) => {
    switch(action.type) {
        case: BUY_POKEMON {
            return {
                pokemon: state.pokemon - action.payload
            }
        }

        case: RETURN_POKEMON {
            return {
                pokemon: state.pokemon + action.payload
            }
        }
        default: return state
    }
}
```

Ahora pasamos la acción al dispatch con la acción nueva:

```
store.dispatch(buy_pokemon_action(2))
```

## Agregamos más cosas al store

Vamos a añadir más productos al store:

```
const default_games_state = {
    pokemon: 10,
    zelda: 10
}
```

Cuando se inicie el estado se mostrarán 10 unidades de Zelda, pero al pasar la acción al reducer se mostrará pokémon 2 veces.

Para arreglar esto, en el reducer (en cada caso) tenemos que poner ```...state``:

```
const games_reducer = (state = default_games_state, action) => {
    switch(action.type) {
        case: BUY_POKEMON {
            return {
                ...state,
                pokemon: state.pokemon - action.payload
            }
        }

        case: RETURN_POKEMON {
            return {
                ...state,
                pokemon: state.pokemon + action.payload
            }
        }
        default: return state
    }
}
```

Ahora el problema está arreglado, y podemos crear nuevas acciones para el segundo videojuego:

```
const buy_zelda_action = (cantidad) => {
    return {
        type: BUY_ZELDA,
        payload: cantidad
    }
}

const return_zelda_action = (cantidad) => {
    return {
        type: RETURN_ZELDA,
        payload: cantidad
    }
}
```

Y añadimos más casos al reducer:

```
const games_reducer = (state = default_games_state, action) => {
    switch(action.type) {
        case: BUY_ZELDA {
            return {
                ...state,
                zelda: state.zelda - action.payload

        case: RETURN_ZELDA {
            return {
                ...state,
                zelda: state.zelda + action.payload
```

Ahora pasamos esto al dispatch:

```
store.dispatch(buy_zelda_action(3))
store.dispatch(buy_zelda_action(2))
```

## Crear y combinar múltiples reducers

Aparte de videojuegos ahora también venderemos consolas. Vamos a crear otro reducer, pero antes debemos crear el estado inicial y las constantes:

```
const default_consoles_state = {
    PS5: 30,
    Switch: 30
}

(...)

const BUY_PS5 = "BUY_PS5"
const RETURN_PS5 = "RETURN_PS5"

const BUY_SWITCH = "BUY_SWITCH"
const RETURN_SWITCH = "RETURN_SWITCH"

(...)

const consoles_reducer = (state = default_consoles_state, action) => {
    switch(action.type) {
        case: BUY_PS5 {
            return {
                ...state,
                PS5: state.PS5 - action.payload

        case: RETURN_PS5 {
            return {
                ...state,
                PS5: state.PS5 + action.payload

        case: BUY_SWITCH {
            return {
                ...state,
                Switch: state.Switch - action.payload

        case: RETURN_SWITCH {
            return {
                ...state,
                Switch: state.Switch + action.payload
```

Para colocar los nuevos reducers en el store, no podemos poner uno al lado del otro y separados por coma, hay que llamar a otra función de redux, ``combineReducers``.

```
import { combineReducers } from "redux"
```

Una vez hecho esto, vamos a crear una función, y aquí pondremos los dos reducers:

```
const rootReducers = combineReducers({
    games_reducer, consoles_reducer
})
```

Una vez hecho esto, en el store vamos a pasar esta función que acabamos de crear:

```
const store = createStore(rootReducers)
```

> Los archivos deben tener terminación .js, redux es javascript.