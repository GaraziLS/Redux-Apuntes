## Conectar Redux y React

Para conectar Redux y React hay que importar un componente de react-redux:

```
import { Provider } from "react-redux"
```

Una vez hecho esto debemos envolver toda la app (el archvo raíz) entre etiquetas <Provider>.

Provider tiene un prop llamado ``store={store}``. También hay que importar el componente store del archivo donde esté. Así, react y redux ya están enlazadas.

## Usando Logger Middleware

Vamos a instalar una extensión llamada Redux Dev Tools en Chrome. Esta extensión necesita una librería proporcionada por el desarrollador de la extensión, que está en github. Pero podemos escribir esto en la terminal:

```
npm install --save redux-devtools-extension
```

Ahora en src > redux > store.js podemos pasar middleware para ver el store en el navegador, pero antes hay que importar:

```
import { composeWithDevTools } from "redux-devtools-extension"
```

Ahora podemos pasar esa librería al store para que se visualice en el navegador:

```
const store = createStore(rootReducers, composeWithDevTools())
```

## Uso de mapStateToProps

El provider proporciona que el store esté disponible en la app, sin embargo hay que conectar los componentes. Para eso copias esto en el componente que quieres conectar, en la sección de imports:

```
import { connect } from "react-redux"
```

> También hay que importar las acciones.

```
import { action1, action2...} from "Carpeta de acciones"
```

Y ahora vamos al export del componente (es un componente de clase con el export abajo del todo) y le pasamos el connect.

La estructura es así:

```
export default connect()(Nombre_Componente)
```

Y así ya estaría conectado. Sin embargo, hay que convertir el estado del componente a props, y para eso usamos la constante ``mapStateToProps``. Esto es igual a la siguiente función (recibe el estado como parámetro):

```
const mapStateToProps = (state) => {
    return {
        Nombre_Store = state.Nombre_Store
    }
}
```

Ahora vamos a pegar el nombre de esa constante en el primer paréntesis del connect:

```
export default connect(mapStateToProps)(Nombre_Store)
```

Ahora ya podemos acceder a esos props, porque la funcón va a devolver otro componente que ya tiene los props integrados.

Así que ya podemos poner ``this.prop.Nombre_Store.key``.

## Uso de mapDispatchToProps

Repetimos el proceso de arriba. Primero importamos:

```
import {connect} from "react-redux"
```

Luego metemos el connect en el componente:

```
export default connect()(Nombre_Componente)
```

Vamos a mapear el dispatch a props, así que escribimos esto. Para usar dispatch hay que importar acciones, así que también las importaremos:

```
import { acciones } from "Carpeta_acción" // Si son varias deben estar en un mismo archivo.

(...)

const mapDispatchToProps = {
    buy_zelda_action,
    return_zelda_action
}

```

(...) // Si en el componente no tenemos nada relacionado con estados, pasamos null como primer parámetro al connect y luego mapDispatchToProps:

```
export default connect(null, mapDispatchToProps)(Nombre_Componente)
```

Y ya con esto podemos llamar a props en los diferentes casos (comprar o devolver):

```
this.props.buy_zelda_action
this.props.return_zelda_actiom
```

## Middleware y acciones asíncronas

Redux usa middleware para extenderse. Como Redux por defecto funciona con acciones síncronas, necesita extenderse para funcionar con acciones asíncronas (como promesas). Redux Thunk sirve para trabajar con acciones asíncronas.

Para instalar esta dependencia escribimos ``npm i redux-thunk`` en la terminal.

## Crear acciones asíncronas en Redux

Empezamos con las promesas. Escribimos esto para tener nuestras constantes listas:

```
export const FETCH_POKEMON_REQUEST = "FETCH_POKEMON_REQUEST"
export const FETCH_POKEMON_SUCCESS = "FETCH_POKEMON_SUCCESS"
export const FETCH_POKEMON_FAIL = "FETCH_POKEMON_FAIL"
```

Ahora creamos las acciones:

```
export const fetchPokemonRequest = () => {
    return {
        type: FETCH_POKEMON_REQUEST
    }
}

export const fetchPokemonRequest = () => {
    return {
        type: FETCH_POKEMON_REQUEST
    }
}

export const fetchPokemonSuccess = (pokemon) => {
    return {
        type: FETCH_POKEMON_SUCCESS
        payload: pokemon // Como la promesa tiene éxito pasamos un payload con el pokémon que se busca
    }
}

export const fetchPokemonFail = (error) => {
    return {
        type: FETCH_POKEMON_FAIL
        
    }
}
```

Estas tres acciones van envueltas en una acción más grande, así que tras eso escribimos lo siguiente:

```
const fetchPokemon = (valor) => {
    return() => {

    }
}

export default fetchPoekmon
```

Esta función (con un valor como argumento que puede ser un error o un pokémon) va a retornar otra función. Thunk hará que envíe el dispatch, así que vamos a pasar el dispatch como argumento y también dentro del return:

```
const fetchPokemon = (valor) => {
    return(dispatch) => {
        dispatch();
    }
}

export default fetchPoekmon
```

Ahora pasamos la primera acción, la petición:

```
const fetchPokemon = (valor) => {
    return(dispatch) => {
        dispatch(fetchPokemonRequest()); 
        
        // Lo pasamos con paréntesis porque las constantes de las acciones son en realidad funciones anónimas
    }
}

export default fetchPoekmon
```

Ahora llamamos a axios (hay que importarlo primero), y dentro de los dispatch the then y catch pasamos la acción correspondiente, con su payload dentro del paréntesis (que va entre corchetes, seguido de un response.data):

```
const fetchPokemon = (valor) => {
    return(dispatch) => {
        dispatch(fetchPokemonRequest());    
        
        // Lo pasamos con paréntesis porque las constantes de las acciones son en realidad funciones anónimas

        axios.get(PokeAPI url)
        .then(response => {
            dispatch(fetchPokemonSuccess([response.data]))
        .catch(error => {
            dispatch(fetchPokemonFail("No se ha encontado el pokémon")

        })
    }
}

export default fetchPoekmon
```

## Creando reducers

Los reducers deben recoger los dispatch, así que crearemos el estado inicial. Por el momento no está cargando, y el pokémon que se retorna está vacío, al igual que los errores porque no hay ninguno. También crearemos el reducer del buscador de pokémons:

```
const initialState = {
    loading: false,
    pokemon: [],
    error: ""
}
```

```
const buscador = (state = initialState, action) =>{
    switch(action.type) {
        case FETCH_POKEMON_REQUEST:
            return {
                ...state,
                loading: true
            }

        case FETCH_POKEMON_SUCCESS:
            return {
                ...state,
                loading: false, 
                pokemon: action.payload
            }
        
        case FETCH_POKEMON_FAIL:
            return {
                loading: false,
                pokemon: [],
                error: action.payload
            }

        default: return state
    }
}
```

Cuando se hace la petición el estado de carga pasa a true porque está cargando. En el caso de que haya éxito el loading pasa a false porque ya ha cargado. En ese caso, en la key pokemon pasamos el action.payload porque lo que nos está devolviendo las acciones es la petición a la API y no la acción en sí. El error estará vacío en el caso de tener éxito.

En el caso de error la carga es falsa porque ya tenemos un resultado. la key pokemon estará vacía porque no se ha encontrado nada y el error tendrá un action.payload.

Ahora solo quedaría exportar el reducer si estuviera en un archivo propio.

## Trabajando con Thunk

Como vamos a trabajar con peticiones hay que configurar Thunk. Dentro del store, y específicamente dentro del ``composeWithDevTools``, vamos a escribir lo siguiente:

```
const store = createStore(rootReducers, composeWithDevTools(
    applyMiddleware(thunk)
))
```

Si los devtools de redux no estuvieran instalados esto se escribiría así:

```
const store = createStore(rootReducers, applyMiddleware(thunk))
```

