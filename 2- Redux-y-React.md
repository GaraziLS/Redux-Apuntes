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



