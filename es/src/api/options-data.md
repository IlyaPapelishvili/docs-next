# Datos

## datos

- **Tipo:** `Function`

- **Detalles:**

    La función que devuelve un objeto de datos para la instancia del componente. En los `data` , no recomendamos observar objetos con su propio comportamiento con estado, como los objetos de la API del navegador y las propiedades de los prototipos. Una buena idea sería tener aquí solo un objeto simple que represente los datos de los componentes.

    Una vez observado, ya no puede agregar propiedades reactivas al objeto de datos raíz. Por lo tanto, se recomienda declarar todas las propiedades reactivas de nivel raíz por adelantado, antes de crear la instancia.

    Una vez creada la instancia, se puede acceder al objeto de datos original como `vm.$data` . La instancia del componente también representa todas las propiedades que se encuentran en el objeto de datos, por lo que `vm.a` será equivalente a `vm.$data.a`

    Las propiedades que comienzan con `_` o `$` **no se** utilizarán como proxy en la instancia del componente porque pueden entrar en conflicto con las propiedades internas de Vue y los métodos API. Tendrá que acceder a ellos como `vm.$data._property` .

- **Ejemplo:**

    ```js
    // direct instance creation
    const data = { a: 1 }

    // The object is added to a component instance
    const vm = Vue.createApp({
      data() {
        return data
      }
    }).mount('# app')

    console.log(vm.a) // => 1
    ```

    Tenga en cuenta que si usa una función de flecha con la propiedad de `data` , `this` no será la instancia del componente, pero aún puede acceder a la instancia como el primer argumento de la función:

    ```js
    data: vm => ({ a: vm.myProp })
    ```

- **Ver también:** [Reactividad en profundidad](../guide/reactivity.html)

## accesorios

- **Tipo:** `Array<string> | Object`

- **Detalles:**

    Una lista / hash de atributos que están expuestos para aceptar datos del componente principal. Tiene una sintaxis simple basada en matrices y una sintaxis alternativa basada en objetos que permite configuraciones avanzadas como verificación de tipos, validación personalizada y valores predeterminados.

    Con la sintaxis basada en objetos, puede utilizar las siguientes opciones:

    - `type` : puede ser uno de los siguientes constructores nativos: `String` , `Number` , `Boolean` , `Array` , `Object` , `Date` , `Function` , `Symbol` , cualquier función de constructor personalizado o una matriz de esos. Verificará si un accesorio tiene un tipo determinado y lanzará una advertencia si no lo tiene. [Más información] (../ guide / component-props.html # prop-types) sobre tipos de prop.

    - `default` : `any` Especifica un valor predeterminado para la propiedad. Si no se pasa la propiedad, se utilizará este valor en su lugar. Los valores predeterminados de objetos o matrices deben devolverse desde una función de fábrica.

    - `required` : `Boolean` Define si la propiedad es necesaria. En un entorno de no producción, se lanzará una advertencia de consola si este valor es verdadero y no se pasa la propiedad.

    - `validator` : `Function` validación personalizada que toma el valor de prop como único argumento. En un entorno de no producción, se lanzará una advertencia de consola si esta función devuelve un valor falso (es decir, la validación falla). Puede leer más sobre la validación de prop [aquí] (../guía/componente-props.html # prop-validación).

- **Ejemplo:**

    ```js
    const app = Vue.createApp({})

    // simple syntax
    app.component('props-demo-simple', {
      props: ['size', 'myMessage']
    })

    // object syntax with validation
    app.component('props-demo-advanced', {
      props: {
        // type check
        height: Number,
        // type check plus other validations
        age: {
          type: Number,
          default: 0,
          required: true,
          validator: value => {
            return value >= 0
          }
        }
      }
    })
    ```

- **Ver también:** [Props](../guide/component-props.html)

## calculado

- **Tipo:** `{ [key: string]: Function | { get: Function, set: Function } }`

- **Detalles:**

    Propiedades calculadas que se mezclarán en la instancia del componente. Todos los captadores y definidores tienen su `this` contexto obligado automáticamente a la instancia del componente.

    Tenga en cuenta que si usa una función de flecha con una propiedad calculada, `this` no será la instancia del componente, pero aún puede acceder a la instancia como el primer argumento de la función:

    ```js
    computed: {
      aDouble: vm => vm.a * 2
    }
    ```

    Las propiedades calculadas se almacenan en caché y solo se vuelven a calcular en los cambios de dependencia reactivos. Tenga en cuenta que si una determinada dependencia está fuera del alcance de la instancia (es decir, no reactiva), la propiedad calculada **no** se actualizará.

- **Ejemplo:**

    ```js
    const app = Vue.createApp({
      data() {
        return { a: 1 }
      },
      computed: {
        // get only
        aDouble() {
          return this.a * 2
        },
        // both get and set
        aPlus: {
          get() {
            return this.a + 1
          },
          set(v) {
            this.a = v - 1
          }
        }
      }
    })

    const vm = app.mount('# app')

    console.log(vm.aPlus) // => 2
    vm.aPlus = 3
    console.log(vm.a) // => 2
    console.log(vm.aDouble) // => 4
    ```

- **Ver también:** [Propiedades calculadas](../guide/computed.html)

## métodos

- **Tipo:** `{ [key: string]: Function }`

- **Detalles:**

    Métodos que se mezclarán en la instancia del componente. Puede acceder a estos métodos directamente en la instancia de VM o usarlos en expresiones de directiva. Todos los métodos tendrán su contexto `this` vinculado automáticamente a la instancia del componente.

    ::: consejo Nota Tenga en cuenta que **no debe utilizar una función de flecha para definir un método** (por ejemplo, `plus: () => this.a++` ). La razón es que las funciones de flecha enlazan el contexto principal, por lo que `this` no será la instancia del componente como esperabas y `this.a` no estará definida. :::

- **Ejemplo:**

    ```js
    const app = Vue.createApp({
      data() {
        return { a: 1 }
      },
      methods: {
        plus() {
          this.a++
        }
      }
    })

    const vm = app.mount('# app')

    vm.plus()
    console.log(vm.a) // => 2
    ```

- **Ver también:** [Manejo de eventos](../guide/events.html)

## reloj

- **Tipo:** `{ [key: string]: string | Function | Object | Array}`

- **Detalles:**

    Un objeto donde las claves son expresiones para observar y los valores son las devoluciones de llamada correspondientes. El valor también puede ser una cadena de un nombre de método o un objeto que contiene opciones adicionales. La instancia del componente llamará a `$watch()` para cada entrada en el objeto en la instanciación. Consulte [$ watch] (instance-methods.html # watch) para obtener más información acerca de las opciones `deep` , `immediate` y de `flush` .

- **Ejemplo:**

    ```js
    const app = Vue.createApp({
      data() {
        return {
          a: 1,
          b: 2,
          c: {
            d: 4
          },
          e: 'test',
          f: 5
        }
      },
      watch: {
        a(val, oldVal) {
          console.log(`new: ${val}, old: ${oldVal}`)
        },
        // string method name
        b: 'someMethod',
        // the callback will be called whenever any of the watched object properties change regardless of their nested depth
        c: {
          handler(val, oldVal) {
            console.log('c changed')
          },
          deep: true
        },
        // the callback will be called immediately after the start of the observation
        e: {
          handler(val, oldVal) {
            console.log('e changed')
          },
          immediate: true
        },
        // you can pass array of callbacks, they will be called one-by-one
        f: [
          'handle1',
          function handle2(val, oldVal) {
            console.log('handle2 triggered')
          },
          {
            handler: function handle3(val, oldVal) {
              console.log('handle3 triggered')
            }
            /* ... */
          }
        ]
      },
      methods: {
        someMethod() {
          console.log('b changed')
        },
        handle1() {
          console.log('handle 1 triggered')
        }
      }
    })

    const vm = app.mount('# app')

    vm.a = 3 // => new: 3, old: 1
    ```

    ::: consejo Nota Tenga en cuenta que *no debe utilizar una función de flecha para definir un observador* (por ejemplo, `searchQuery: newValue => this.updateAutocomplete(newValue)` ). La razón es que las funciones de flecha enlazan el contexto principal, por lo que `this` no será la instancia del componente como esperabas y `this.updateAutocomplete` no estará definido. :::

- **Ver también:** [Watchers] (../ guide / computed.html # watchers)

## emite

- **Tipo:** `Array<string> | Object`

- **Detalles:**

    Una lista / hash de eventos personalizados que se pueden emitir desde el componente. Tiene una sintaxis simple basada en Array y una sintaxis alternativa basada en Objetos que permite configurar una validación de eventos.

    En la sintaxis basada en objetos, el valor de cada propiedad puede ser `null` o una función de validación. La función de validación recibirá los argumentos adicionales pasados a la llamada `$emit` . Por ejemplo, si se llama a `this.$emit('foo', 1)` , el validador correspondiente para `foo` recibirá el argumento `1` . La función de validación debe devolver un valor booleano para indicar si los argumentos del evento son válidos.

- **Uso:**

    ```js
    const app = Vue.createApp({})

    // Array syntax
    app.component('todo-item', {
      emits: ['check'],
      created() {
        this.$emit('check')
      }
    })

    // Object syntax
    app.component('reply-form', {
      emits: {
        // no validation
        click: null,

        // with validation
        submit: payload => {
          if (payload.email && payload.password) {
            return true
          } else {
            console.warn(`Invalid submit event payload!`)
            return false
          }
        }
      }
    })
    ```

    ::: tip Nota Los eventos listados en la opción `emits` **no** serán heredados por el elemento raíz del componente y también serán excluidos de la propiedad `$attrs` . :::

- **Consulte también:** [Herencia de atributos] (../guía / componente-attrs.html # herencia-atributo)
