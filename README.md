# Código limpio en Go

## Sobre la traducción

### Autores

* Documento original: [Clean Go Code](https://github.com/Pungyeon/clean-go-article) por _Lasse Martin Jakobsen_.
* Traducción: [Jorge Fuertes Alfranca](https://jorgefuertes.com/)
* Revisión técnica: [Carlos Ming](https://www.linkedin.com/in/carlos-ming-6a56b737/)

### Por qué una traducción

Quise entender bien el concepto de _código limpio_ en _Go_, y para eso nada mejor que tomar notas mientras lo estudias, las notas fueron creciendo y, en fin, se me fue de las manos. Por otro lado, me gusta mucho poder hacer una contribución a la comunidad _Go de habla hispana_, por pequeña que sea dicha contribución.

Además hacelo me ha dado la oportunidad de introducir algunas notas, actualizaciones y puntualizaciones al texto original, que son muy pocas y breves pero, en mi opinión, necesarias.

En esencia el texto es el mismo y los conceptos están íntegros, pero si en algún caso tienes dudas, por favor, recurre al texto original y te ruego que nos envíes correcciones, _pull-requests_, y los comentarios que creas necesarios.

Gracias a todos y especialmente a _Carlos Ming_ por ayudarme con la revisión.

## Prefacio: ¿Por qué escribir código limpio?

Este documento es una referencia para la comunidad de _Go_ que pretende ayudar a los desarrolladores a escribir un código más limpio. Cuando estés trabajando en un proyecto personal o como parte de un equipo más grande, escribir código limpio es una habilidad que debes tener. Establecer buenos paradigmas y estándares consistentes y accesibles para escribir código limpio puede ayudara prevenir que los desarrolladores malgasten muchas horas sin sentido intentando entender el trabajo ajeno e incluso el suyo propio.

> _No leemos código, lo decodificamos._ – Peter Seibel

Como desarrolladores, estamos tentados a veces de escribir código de una forma rápida, sin reparar en las mejores prácticas, causando que las revisiones y las pruebas del código sean más difíciles. En algún sentido, estamos codificando, y, al hacerlo así, creamos dificultades para que otros pueden decodificar nuestro trabajo. Sin embargo queremos que nuestro código sea utilizable, legible y sostenible. Todo ello requiere codificar de la forma correcta, no de la forma fácil.

Este documento comienza con una breve y simple introducción a los fundamentos de la escritura de código limpio. Más adelante discutiremos ejemplos concretos de refactorización específica para _Go._

### Unas palabras sobre `gofmt`

Me gustaría tomarme unas frases para aclarar mi postura sobre `gofmt` porque hay muchas cosas con las que no estoy de acuerdo cuando se trata de esta herramienta. Prefiero el `snake_case` al `CamelCase`, y me gusta nombrar a mis constantes en mayúsculas. Naturalmente, también tengo muchas opiniones sobre la colocación de los corchetes. Dicho esto, `gofmt` nos permite tener un estándar común para escribir código _Go_, y eso es genial. Como desarrollador, puedo entender que los programadores _Go_ se sientan limitados por `gofmt`, especialmente si no están de acuerdo con algunas de sus reglas. Pero en mi opinión, un código homogéneo es más importante que tener total libertad expresiva.

## Tabla de contenidos

* [Introducción al código limpio](#introducción-al-código-limpio)
  * [Desarrollo dirigido por pruebas](#desarrollo-dirigido-por-pruebas)
  * [Convenciones de nomenclatura](#convenciones-de-nomenclatura)
  * [Comentarios](#comentarios)
    * [Nomenclatura de funciones](#nomenclatura-de-las-funciones)
    * [Nomenclatura de variables](#nomenclatura-de-las-funciones)
  * [Limpiando funciones](#limpiando-funciones)
    * [Longitud de funciones](#longitud-de-las-funciones)
    * [Firma de las funciones](#firma-de-las-funciones)
  * [Ámbito de las variables](#ámbito-de-las-variables)
  * [Declaración de variables](#declaración-de-variables)

* [Go limpio](#go-limpio)
  * [Retorno de valores](#valores-devueltos)
    * [Devolviendo errores definidos](#devolviendo-errores-predefinidos)
    * [Devolviendo errores dinámicos](#devolviendo-errores-dinámicos)
  * [Punteros en Go](#punteros-en-go)
  * [Las clausuras son punteros a funciones](#las-clausuras-son-punteros-a-funciones)
  * [Interfaces en Go](#interfaces-en-go)
  * [La interfaz vacía](#la-interfaz-vacía)
* [Sumario](#sumario)

## Introducción al código limpio

El código limpio es el concepto pragmático de promover un software legible y de fácil mantenimiento. El código limpio establece confianza en la base de código y ayuda a minimizar las posibilidades de introducir errores por descuido. También ayuda a los desarrolladores a conservar su agilidad, la cual suele disminuir a medida que la base de código se expande.

### Desarrollo dirigido por pruebas

El desarrollo basado en pruebas es la práctica de probar el código con frecuencia a lo largo de ciclos de desarrollo cortos o esprints. En última instancia, contribuye a la limpieza del código al invitar a los desarrolladores a cuestionar la funcionalidad y el propósito de su código. Para facilitar las pruebas, se anima a los desarrolladores a escribir funciones cortas que sólo hagan una cosa. Por ejemplo, podría decirse que es mucho más fácil probar (y entender) una función de sólo cuatro líneas que una de cuarenta.

Ciclo de desarrollo dirigido por pruebas:

1. Escribir o ejecutar una prueba.
2. Si la prueba falla, hacer que el código la pase.
3. Refactorizar el código en consecuencia.
4. Repetir.

Las pruebas y la refactorización están entrelazadas en este proceso. Al refactorizar el código para hacerlo más comprensible o fácil de mantener, hay que probar los cambios a fondo para asegurarse de que no se ha alterado el comportamiento de las funciones. Esto puede ser increíblemente útil a medida que crece el código base.

### Convenciones de nomenclatura

#### Comentarios

Comentar el código es una práctica esencial aunque tiende a aplicarse mal. Los comentarios innecesarios pueden señalar problemas con el código al que hacen referencia, como el uso de nomenclaturas pobres. En cualquier caso, si es necesario o no un determinado comentario, es subjetivo y depende de como se ha escrito ese código y de su legibilidad. Por ejemplo la lógica del código bien escrito puede resultar compleja y necesitar de un comentario para aclarar lo que está pasando. En ese caso se podría argumentar que el comentario _ayuda_ y es, por tanto, necesario.

En _Go_, y de acuerdo con `gofmt`, _todas_ las variables públicas y las funciones deben tener una anotación o comentario. Creo que esto es totalmente correcto y nos aporta reglas consistentes para documentar nuestro código. El cualquier caso siempre quiero distinguir entre comentarios que permiten generar documentación automáticamente y _el resto_ de los comentarios. Las anotaciones para documentación deben abstraerse de la implementación lógica del código y tratar un nivel más alto.

Digo esto porque hay otras formas de explicar el código y asegurarse de que se está escribiendo de forma comprensible y expresiva. Si el código no es ni lo uno ni lo otro, algunas personas consideran aceptable introducir un comentario explicando la enrevesada lógica. Por desgracia, eso no ayuda mucho. Por un lado, la mayoría de la gente simplemente no leerá los comentarios, ya que tienden a ser muy intrusivos en la experiencia de revisar el código. Además, como puedes imaginar, un desarrollador no estará muy contento si se ve forzado a revisar código poco claro que ha sido cubierto de comentarios. Cuanto menos tenga que leer la gente para entender lo que hace tu código, mejor para ellos.

Vamos a dar un paso atrás y mirar a algunos ejemplos concretos. Así es como _no deberías_ comentar tu código.

```go
// iterar sobre un rango de 0 a 9
// e invocar la función doSomething
// en cada iteración
for i := 0; i < 10; i++ {
  doSomething(i)
}
```

Esto es lo que me gusta llamar _un comentario tutorial_, ya que es muy común en tutoriales que, muy a menudo, explican la funcionalidad a bajo nivel de un lenguaje (o de la programación en general). Mientras que esos comentarios son de ayuda para los novatos, son absolutamente inútiles en código de producción. Idealmente no estamos colaborando con programadores que no entienden algo tan simple como un bucle cuando empiezan a trabajar con un equipo de desarrollo. Como programadores, no deberíamos tener que leer comentarios para entender que está pasando, ya sabemos que estamos iterando sobre un rango de 0 a 9 porque podemos simplemente leer el código. Hete aquí el proverbio:

> _Documenta el porqué, no el como._ – Venkat Subramaniam

Siguiento esta lógica, podemos cambiar nuestro comentario para explicar _por qué_ estamos iterando sobre un rango de 0 a 9:

```go
// instanciar 10 hilos para manejar la carga de trabajo entrante
for i := 0; i < 10; i++ {
  doSomething(i)
}
```

Ahora entendemos _por qué_ tenemos un bucle y podemos decir _qué_ estamos haciendo, simplemente leyendo el código. Más o menos.

Todavía no es lo que consideraríamos código limpio. El comentario es preocupante porque probablemente no es necesario expresar esta explicación en prosa, asumiendo que el código está bien escrito, que no lo está. Tecnicamente todavía estamos diciento lo que estamos haciendo, no por qué lo estamos haciendo. Podemos expresar más facilmente el _qué_ directamente en nuestro código, usando nombres más significativos:

```go
for workerID := 0; workerID < 10; workerID++ {
  instantiateThread(workerID)
}
```

Con solo un ligero cambio a los nombres de la variable y de la función, hemos conseguido explicar que estamos haciendo directamente en el código. Así es mucho más claro para el lector, poque no tiene que leer el comentario y mapear la prosa en el código. Podemos leer el código para entender lo que está haciendo.

Por supuesto esto ha sido un ejemplo relativamente trivial. Escribir código limpio y expresivo no es, desafortunadamente, siempre tan facil. Puede convertirse en un dificultar creciente mientras que la base de código va aumentando en complejidad. Cuanto más practiques escribiendo comentarios con este esquema mental y evites explicar lo que estás haciendo, más limpio resultará tu código.

#### Nomenclatura de las funciones

La regla general es realmente simple: cuanto más específica sea la función, más general es su nombre. En otras palabras, queremos empezar con un nombre muy general y corto, como `Run` o `Parse`, que describe la funcionalidad general. Imaginemos que estamos creando un _parser_ de configuración. Siguiendo esta convención, nuestro nivel más alto de abstracción debería verse parecido a:

```go
func main() {
    configpath := flag.String("config-path", "", "configuration file path")
    flag.Parse()

    config, err := configuration.Parse(*configpath)

    ...
}
```

Enfocándonos en la nomenclatura de la función `Parse`, quitando que tiene un nombre muy corto y genérico, está muy claro lo que intenta conseguir.

Cuando descendemos una capa, nuestra funcíon podría tener un nombre ligeramente más específico:

```go
func Parse(filepath string) (Config, error) {
    switch fileExtension(filepath) {
    case "json":
        return parseJSON(filepath)
    case "yaml":
        return parseYAML(filepath)
    case "toml":
        return parseTOML(filepath)
    default:
        return Config{}, ErrUnknownFileExtension
    }
}
```

En este caso, hemos distinguido claramente las llamadas a funciones anidadas de su función principal sin ser demasiado específicos. Esto permite que cada llamada a función anidada tenga sentido por sí misma, así como en el contexto de la función padre. Por otro lado, si hubiéramos llamado _json_ a la función _parseJSON_, no tendría sentido por sí misma. La funcionalidad se perdería en el nombre, y ya no seríamos capaces de decir si esta función está parseando, creando o codificando _JSON_.

Notesé que `fileExtension` es en realidad un poco más específica. Sin embargo esto se debe a que, de hecho, su funcionalidad es naturalmente más específica.

```go
func fileExtension(filepath string) string {
    segments := strings.Split(filepath, ".")
    return segments[len(segments)-1]
}
```

Este tipo de progresión lógica en nuestra nomenclatura de funciones, desde un nivel más alto de abstracción a uno más bajo, más específico, hace nuestro código más legible y fácil de seguir. Consideremos la alternativa: Si nuestro nivel de abstracción es demasiado específico, acabaremos con un nombre que intente cubrir todas las bases, algo como: `DetermineFileExtensionAndParseConfigurationFile`. Es horriblemente dificil de leer; estamos intentando ser demasiado específicos demasiado pronto y estamos confundiendo al lector, ¡Aunque estemos intentando ser claros!

#### Nomenclatura de variables

Sorprendentemente, lo contrario es válido para las variabeles. Al contrario que las funciones, nuestras variables deben ser nombradas de más a menos especificamente según profundicemos en ámbitos anidados.

> _No deberías nombrar tus variables según su tipo por la misma razón que no llamarías a tus mascotas 'perro' o 'gato'._ – Dave Cheney

¿Por qué los nombres de las variables deben ser menos específicos a medida que nos adentramos en el ámbito de una función? Sencillamente, a medida que el ámbito de una variable se hace más pequeño, resulta más claro para el lector lo que representa esa variable, eliminando así la necesidad de nombres específicos. En el ejemplo de la función anterior `extensiónArchivo`, podríamos incluso acortar el nombre de la variable `segmentos` a `s` si quisiéramos. El contexto de la variable está tan claro que no es necesario explicarlo con nombres más largos. Otro buen ejemplo de esto son los bucles `for` anidados:

```go
func PrintBrandsInList(brands []BeerBrand) {
    for _, b := range brands {
        fmt.Println(b)
    }
}
```

En el ejemplo anterior, el ámbito de la variable `b` es tan reducido que no necesitamos pensar más para recordar que representa exactamente. Sin embargo, como el ámbito de `brands` es ligeramente más amplio, ayuda que sea más específico. Cuando expandimos el ámbito de la variable se hace más evidente:

```go
func BeerBrandListToBeerList(beerBrands []BeerBrand) []Beer {
    var beerList []Beer
    for _, brand := range beerBrands {
        for _, beer := range brand {
            beerList = append(beerList, beer)
        }
    }
    return beerList
}
```

¡Perfecto! Esta función es facil de leer. Ahora vamos a aplicar la lógica contraria (incorrecta), para nombrar nuestras variables:

```go
func BeerBrandListToBeerList(b []BeerBrand) []Beer {
    var bl []Beer
    for _, beerBrand := range b {
        for _, beerBrandBeerName := range beerBrand {
            bl = append(bl, beerBrandBeerName)
        }
    }
    return bl
}
```

Incluso aunque sea posible imaginar qué está haciendo esta función, la excesiva brevedad de los nombres de las variables hace difcil seguir la lógica según vamos profundizando. Even though it's possible to figure out what this function is doing, the excessive brevity of the variable names makes it difficult to follow the logic as we travel deeper. Podría dar lugar a una confusión total porque mezclamos nombres de variables cortos y largos de forma incoherente.

### Limpiando funciones

Ahora que conocemos algunas de las _buenas prácticas_ para nombrar a nuestras variables y funciones, además de clarificar nuestro código con comentarios, vamos a bucear más especificamente en como podemos refactorizar funciones para hacerlas más limpias.

#### Longitud de las funciones

> _¿Cuan pequeña debe ser una función? ¡Más pequeña que eso!_ – Robert C. Martin

Al escribir código limpio, nuestro objetivo principal es hacer que nuestro código sea fácilmente digerible. La forma más efectiva de hacer esto es hacer que nuestras funciones sean lo más cortas posible. Es importante entender que no necesariamente hacemos esto para evitar la duplicación de código. La razón más importante es mejorar la _comprensión del código_.

Puede ayudar a entenderlo ver la descrición de una función a muy alto nivel:

```plain
fn GetItem:
    - parsear el ID de órden en la entrada json
    - obtener usuario del contexto
    - comprobar si el usuario tiene el rol apropiado
    - recuperar órden de la base de datos
```

Escribiendo funciones pequenas, tipicamente entre 5 y 8 líneas en Go, podemos crear código que se lea casi tan natural como la descripción anterior:

```go
var (
    NullItem = Item{}
    ErrInsufficientPrivileges = errors.New("user does not have sufficient privileges")
)

func GetItem(ctx context.Context, json []bytes) (Item, error) {
    order, err := NewItemFromJSON(json)
    if err != nil {
        return NullItem, err
    }
    if !GetUserFromContext(ctx).IsAdmin() {
       return NullItem, ErrInsufficientPrivileges
    }
    return db.GetItem(order.ItemID)
}
```

Utilizando funciones más pequeñas eliminamos otro hábito horrible al escribir código: el infierno de las sangrías (indentation en Inglés, a veces traducido como _indentación o identación_). Esto ocurre habitualmente en una cadena de sentencias `if` anidadas sin control en una función. Esto hace muy dificil leer el código para los humanos y se debe eliminar cada vez que se identifique. El _infierno de sangrías_ es particularmente común cuando trabajamos con `interface{}` y usamos conversiónd de tipos (type casting):

```go
func GetItem(extension string) (Item, error) {
    if refIface, ok := db.ReferenceCache.Get(extension); ok {
        if ref, ok := refIface.(string); ok {
            if itemIface, ok := db.ItemCache.Get(ref); ok {
                if item, ok := itemIface.(Item); ok {
                    if item.Active {
                        return Item, nil
                    } else {
                      return EmptyItem, errors.New("no active item found in cache")
                    }
                } else {
                  return EmptyItem, errors.New("could not cast cache interface to Item")
                }
            } else {
              return EmptyItem, errors.New("extension was not found in cache reference")
            }
        } else {
          return EmptyItem, errors.New("could not cast cache reference interface to Item")
        }
    }
    return EmptyItem, errors.New("reference not found in cache")
}
```

Primero, el _infierno de sangrías_ hace dicil que otros desarrolladores entiendan el flujo. Segundo, si la lógica de tus sentencias `if` se expande, aumenta exponencialmente la dificultar para imaginar que rama devuelve qué, y asegurar que todas las ramas estén devolviendo algún valor. Otro problema es que, sentencias condicionales con esta profundidad de anidamiento, fuezan al lector a moverse arriba y abajo _(scroll)_, intentando manteter en su cabeza la traza de muchos estados lógicos. También dificulta hacer pruebas de código y cazar gazapos _(bugs)_, porque hay muchas posibilidades anidadas que tienes que tener en cuenta.

El _infierno de sangrías_ puede resultar en la fatiga del lector si un programador tiene que analizar constantemente un código difícil de manejar como el del ejemplo anterior. Esto es algo que queremos evitar a toda costa.

¿Cómo limpiamos esta función? En realidad es bastante simple. En nuestra primera iteración, intentaremos asegurarnos de devolver un error lo antes posible. En lugar de anidar las sentencias `if` y `else`, queremos "empujar nuestro código hacia la izquierda", por así decirlo:

```go
func GetItem(extension string) (Item, error) {
    refIface, ok := db.ReferenceCache.Get(extension)
    if !ok {
        return EmptyItem, errors.New("reference not found in cache")
    }

    ref, ok := refIface.(string)
    if !ok {
        // return cast error on reference
    }

    itemIface, ok := db.ItemCache.Get(ref)
    if !ok {
        // return no item found in cache by reference
    }

    item, ok := itemIface.(Item)
    if !ok {
        // return cast error on item interface
    }

    if !item.Active {
        // return no item active
    }

    return Item, nil
}
```

Una vez que estamos listos con nuetro primer intento de refactorización, podemos proceder a partir la función en otras más pequenas.
Once we're done with our first attempt at refactoring the function, we can proceed to split up the function into smaller functions.
Esta es una buena regla general: si el patrón `value, err :=` se repite más de una vez en una función, es una indicación de que podemos dividir la lógica de nuestro código en partes más pequeñas:

```go
func GetItem(extension string) (Item, error) {
    ref, ok := getReference(extension)
    if !ok {
        return EmptyItem, ErrReferenceNotFound
    }
    return getItemByReference(ref)
}

func getReference(extension string) (string, bool) {
    refIface, ok := db.ReferenceCache.Get(extension)
    if !ok {
        return EmptyItem, false
    }
    return refIface.(string), true
}

func getItemByReference(reference string) (Item, error) {
    item, ok := getItemFromCache(reference)
    if !item.Active || !ok {
        return EmptyItem, ErrItemNotFound
    }
    return Item, nil
}

func getItemFromCache(reference string) (Item, bool) {
    if itemIface, ok := db.ItemCache.Get(ref); ok {
        return EmptyItem, false
    }
    return itemIface.(Item), true
}
```

Como hemos mencionado anteriormente, el _infierno de sangrías_ puede dificultar la prueba de nuestro código. Cuando dividimos nuestra función `GetItem` en varias auxiliares _(helpers)_, facilitamos el seguimiento de errores al probar nuestro código. En lugar de las varias sentencias `if` que, la versión original, tenía en el mismo ámbito, la versión refactorizada de `GetItem` tiene solo dos caminos de bifurcación que debemos considerar. Las funciones auxiliares también son cortas y digeribles, lo que las hace más fáciles de leer.

> Nota: Para código de producción, debemos elaborar esto un poco más para devolver errores en lugar de valores boleanos `bool`. Así será mucho más facil de entender dónde se ha originado el error. Téngase en cuenta que estas son solo funciones de ejemplo, y devolver boleanos es suficiente por ahora. Más adelante veremos ejemplos más detallados sobre como devolver errores.

Nótese que la limpieza de la función `GetItem`, resulta, después de todo, en más líneas de código. Sin embargo, el código es ahora mucho más facil de leer por sí mismo. Escrito en capas, como una cebolla, podemos pelarla e ir ignorando las capas que no nos interesan hasta las que queremos examinar. Esto hace más facil de entender la funcionalidad de bajo nivel, ya que sólo tenemos que leer quizá de tres a cinco líneas cada vez.

Este ejemplo ilustra como no podemos medir la limpieza de nuestro código por el número de líneas que usa. La primera versión del código es ciertamente mucho más corta, pero estaba _artificialmente_ recortada y era muy dificil de leer. En muchos casos, limpiar el código puede incialmente expandir la base existente en cuanto a número de líneas. Pero es preferible a la alternativa de tener uuna desastrosa y complicada lógica. Si alguna vez lo dudas, simplemente considera como te sientes acerca de la siguiente función, que hace exactamente lo mismo que nuestro código código, usando sólo dos líneas:

```go
func GetItemIfActive(extension string) (Item, error) {
    if refIface,ok := db.ReferenceCache.Get(extension); ok {if ref,ok := refIface.(string); ok { if itemIface,ok := db.ItemCache.Get(ref); ok { if item,ok := itemIface.(Item); ok { if item.Active { return Item,nil }}}}} return EmptyItem, errors.New("reference not found in cache")
}
```

#### Firma de las funciones

La creación de una buena estructura de nomenclatura de funciones facilita la lectura y la comprensión de la intención del código. Como vimos anteriormente, acortar nuestras funciones nos ayuda a comprender la lógica de la función. La última parte de la limpieza de nuestras funciones implica comprender el contexto de la entrada de la función. Con esto viene otra regla fácil de seguir: **Las firmas de funciones solo deben contener uno o dos parámetros de entrada**. En ciertos casos excepcionales, tres pueden ser aceptables, pero aquí es donde deberíamos comenzar a considerar un refactor. Al igual que la regla de que nuestras funciones solo deben tener entre 5 y 8 líneas, esto puede parecer bastante extremo al principio. Sin embargo, siento que esta regla es mucho más fácil de justificar.

Por ejemplo veamos la siguiente función del [tutorial de introducción a la biblioteca Go de RabbitMQ](https://www.rabbitmq.com/tutorials/tutorial-one-go.html):

```go
q, err := ch.QueueDeclare(
  "hello", // name
  false,   // durable
  false,   // delete when unused
  false,   // exclusive
  false,   // no-wait
  nil,     // arguments
)
```

La función `QueueDeclare` admite seis parámetros de entrada, que es un montón. Con algún esfuerzo, es posible comprender que hace este código gracias a los comentarios.
Sin embargo, los comentarios son más bien parte del problema, como mencionamos antes, deben ser sustituidos por código descriptivo cuando sea posible. Después de todo, nada evita que invoquemos esta función _sin_ comentarios:

```go
q, err := ch.QueueDeclare("hello", false, false, false, false, nil)
```

Ahora, sin mirar a la versión comentada, intenta recordar qué representan los últimos argumentos `false`. ¿Imposible, verdad? Inevitablemente los habrás olvidado en algún momento. Así podemos llegar a cometer costosos errors y gazapos que son difíciles de corregir. Estos errores pueden venir incluso de comentarios incorrectos, imagina etiquetar incorrectamente un parámetro de entrada. Corregir este error puede ser insoportablemente dificil de arreglar, especialmente cuando nuestra familiaridad con el código se deteriore con el tiempo o sea muy baja porque acabamos de empezar con él. Por lo tanto, es muy recomendable reemplazar esos parámetros de entrada con una estructura de 'Opciones':

```go
type QueueOptions struct {
    Name string
    Durable bool
    DeleteOnExit bool
    Exclusive bool
    NoWait bool
    Arguments []interface{}
}

q, err := ch.QueueDeclare(QueueOptions{
    Name: "hello",
    Durable: false,
    DeleteOnExit: false,
    Exclusive: false,
    NoWait: false,
    Arguments: nil,
})
```

Así solucionamos dos problemas: el mal uso de los comentarios y el etiquetado incorrecto y accidental de las variables. Por supuesto todavía podemos confundir las propiedades y asignarles un valor incorrecto pero, en ese caso, será mucho más facil determinar donde reside nuestro error en el código. Como añadido a esta técnica podemos utilizar nuestra estructura `QueueOptions` para inferir los valores por defecto para los parámetros de entrada de nuestro función. Cuando se declaran las estructuras en Go, todas las propiedades se inicializan a sus valores por defecto. Esto significa que se puede invocar a `QueueDeclare` de la siguiente forma:

```go
q, err := ch.QueueDeclare(QueueOptions{
    Name: "hello",
})
```

El resto de los valores son inicializados a su valor por defecto `false`, excepto los `Arguments`, que es una interfaz con un valor por defecto `nil`. Esta aproximación es no solo más segura, sino que también es mucho más clara acerca de nuestras intenciones. Además en este caso podemos escribir menos código. Una ganancia para todos los involucrados en este proyecto.

Una **nota final** acerca de esto: No siempre es posible cambiar la firma de una función. En este caso, por ejemplo, no tenemos control sobre la firma de la función `QueueDeclare`, porque pertenece a la biblioteca de _RabbitMQ_. No es nuestro código, así que no podemos cambiarla, pero podemos _embolverla_ para que se ajuste a nuestros propósitos:

```go
type RMQChannel struct {
    channel *amqp.Channel
}

func (rmqch *RMQChannel) QueueDeclare(opts QueueOptions) (Queue, error) {
    return rmqch.channel.QueueDeclare(
        opts.Name,
        opts.Durable,
        opts.DeleteOnExit,
        opts.Exclusive,
        opts.NoWait,
        opts.Arguments,
    )
}
```

Basicamente hemos creado una nueva estructura llamada `RMQChannel` que contiene el tipo `amqp.Channel`, el cual tiene el método `QueueDeclare`. Podemos crear nuestra propia version de este método, que esencialmente sólo llama a la vieja versión de la función en la biblioteca de _RabbitMQ_. Nuestro nuevo método tiene todas las ventajas descritas anteriormente, y lo hemos conseguido sin cambiar una sola línea de código en la biblioteca de _RabbitMQ_.

Basically, we create a new structure named `RMQChannel` that contains the `amqp.Channel` type, which has the `QueueDeclare` method. We then create our own version of this method, which essentially just calls the old version of the _RabbitMQ_ library function. Our new method has all the advantages described before, and we achieved this without actually having to change any of the code in the _RabbitMQ_ library.

Utilizaremos esta idea de envolver funciones para introducir código limpio y seguro más adelande, cuando hablemos de `interface{}`.

### Ámbito de las variables

Recordemos la idea de escribir funciones más pequeñas, lo que tiene otro interesante efecto colateral que no hemos cubierto en el capítulo anterior: Escribir funciones más pequeñas puede, casi siempre, eliminar la necesidad de variables mutables que se filtran al ámbito global.

Las variables globales son problemáticas y no son código límpio. Dificultan mucho a los programadores la compresión del estado actual de una variable. Si una variable es mutable y global, por definición, su valor va a ser cambiado por cualquier parte del programa. No puedes garantizar que va a tener un valor específico en algún punto, y es un dolor de cabeza para todos. Es otro ejemplo de un problema trivial que se convertirá en grave según se expanda la base de código.

Observemos en un pequeño ejemplo como variables **no globales** en un ámbito largo pueden causar problemas. También introducimos el problema del **emascaramiento de variables**, como se demuestra en el código extraído de un artículo titulado [Golang scope issue](https://idiallo.com/blog/golang-scopes):

```go
func doComplex() (string, error) {
    return "Success", nil
}

func main() {
    var val string
    num := 32

    switch num {
    case 16:
    // do nothing
    case 32:
        val, err := doComplex()
        if err != nil {
            panic(err)
        }
        if val == "" {
            // do something else
        }
    case 64:
        // do nothing
    }

    fmt.Println(val)
}
```

¿Cual es el problema en este código? En un vistazo parece que el valor de `var val string` debiera ser impreso como `Success` al final de la función `main`. Desafortunadamente, no es el caso, por causa de la siguiente línea:

```go
val, err := doComplex()
```

Aquí se está declarando una nueva variable `val`, en el ámbito de la rama `case 32` del `switch`, que no tiene nada que ver con la declarada en la primera línea de `main`. Por supuesto, se puede argumentar que la sintaxis de Go es un poco complicada, con lo que no estoy necesariamente en desacuerdo, pero hay un problema mucho peor en cuestión. La declaración de `var val string` como una variable mutable de gran alcance es completamente innecesaria. Si hacemos una refactorización **muy simple**, ya no tendremos este problema:

```go
func getStringResult(num int) (string, error) {
    switch num {
    case 16:
    // do nothing
    case 32:
       return doComplex()
    case 64:
        // do nothing
    }
    return "", nil
}

func main() {
    val, err := getStringResult(32)
    if err != nil {
        panic(err)
    }
    if val == "" {
        // do something else
    }
    fmt.Println(val)
}
```

Después de nuestra refactorización, `val` ya no se modifica y el alcance se ha reducido. Tenga en cuenta que estas funciones son muy simples. Una vez que este tipo de estilo de código se convierte en parte de sistemas más grandes y complejos, puede ser imposible averiguar por qué se producen los errores. No queremos que esto suceda, no solo porque generalmente no nos gustan los errores de software, sino también porque es una falta de respeto hacia nuestros colegas y hacia nosotros mismos; potencialmente estamos perdiendo el tiempo mutuamente teniendo que depurar este tipo de código. Los desarrolladores deben asumir la responsabilidad de su propio código en lugar de culpar de estos problemas a la sintaxis de declaración de variables de un lenguaje en particular como _Go_.

En una nota al margen, si la parte `// do something else` es otro intento de mutar la variable `val`, debemos extraer esa lógica como su propia función independiente, así como la parte anterior de la misma. De esta manera, en lugar de expandir el alcance mutable de nuestras variables, podemos devolver un nuevo valor:

```go
func getVal(num int) (string, error) {
    val, err := getStringResult(num)
    if err != nil {
        return "", err
    }
    if val == "" {
        return NewValue() // pretend function
    }
    return val, err
}

func main() {
    val, err := getVal(32)
    if err != nil {
        panic(err)
    }
    fmt.Println(val)
}
```

### Declaración de variables

Además de evitar problemas con el ámbito de la variable y su mutabilidad, también podemos mejorar su legibilidad declarando variables tan cerca de su uso como sea posible. En el lenguaje _C_ es muy común declarar las variables de la siguiente forma:

```go
func main() {
  var err error
  var items []Item
  var sender, receiver chan Item

  items = store.GetItems()
  sender = make(chan Item)
  receiver = make(chan Item)

  for _, item := range items {
    ...
  }
}
```

Lo que adolece de los mismo síntomas descritos cuando hablamos del ámbito de las variables. Aunque es posible que estas variables no se reasignen en ningún momento, este estilo de codificación mantine al lector alerta de una forma incorrecta. Como la memoria de un ordenador, la memoria a corto plazo de nuestro cerebro tiene una capacidad limitada. Tener que recordar la traza de qué variables son mutables y cuando un determinado fragmento de código puede cambiarlas o no, hace muy dificil entender qué está haciendo el código, e imaginar cual va a ser el valor devuelto puede convertirse en una pesadilla. Por tanto, para facilitar la lectura, se recomienda declaras las variables tan cerca de su uso como sea posible:

```go
func main() {
 var sender chan Item
 sender = make(chan Item)

 go func() {
  for {
   select {
   case item := <-sender:
    // do something
   }
  }
 }()
}
```

Todavía podemos mejorarlo invocando la función directamente en la declaración, así estará mucho más claro que la lógica de la función está asociada con la variable declarada:

```go
func main() {
  sender := func() chan Item {
    channel := make(chan Item)
    go func() {
      for {
        select { ... }
      }
    }()
    return channel
  }
}
```

Y, para cerrar el círculo, podemos convertir la función anónima en una función nominal:

```go
func main() {
  sender := NewSenderChannel()
}

func NewSenderChannel() chan Item {
  channel := make(chan Item)
  go func() {
    for {
      select { ... }
    }
  }()
  return channel
}
```

Sigue estando claro que estamos declarando una variable, y la lógica asociada con el canal retornado es simbre, no como en el primer ejemplo. Se facilita la navegación por el código y la comprensión del papel de cada variable.

Por supuesto nada impide que cambiemos nuestra variable `sender`, y no podemos hacer nada para evitarlo al no existir la posibilidad de declarar una estructura constante como `const struct` o `static` en _Go_. Deberemos tener cuidado nosotros mismo de modificar esta variable más adelante en el código.

Of course, this doesn't actually prevent us from mutating our `sender` variable. There is nothing that we can do about this, as there is no way of declaring a `const struct` or `static` variables in Go. This means that we'll have to restrain ourselves from modifying this variable at a later point in the code.

> NOTA: La palabra reservada `const` existe, pero su uso está limitado a los tipos primitivos.

Una forma de evitarlo o, al menos, de limitar la mutabilidad de una variable, es hacerlo a nivel de paquete. El truco consiste en crear una estructura siendo esta variable una propiedad privada. Desde ese momento, la propiedad, sería sólo accesible a través de otros métodos proporcionados por la estructura que la envuelve. Expandiendo nuestro ejemplo, se vería algo como lo siguiente:

```go
type Sender struct {
  sender chan Item
}

func NewSender() *Sender {
  return &Sender{
    sender: NewSenderChannel(),
  }
}

func (s *Sender) Send(item Item) {
  s.sender <- item
}
```

Nos hemos asegurado de que la propiedad `sender` de nuestra estructura `Sender` nunca podrá ser cambiada, al menos no desde fuera del paquete. En el momento de escribir este documento, esta es la única forma de crear variables no primitivas y públicamente inmutables. Un poco verboso, pero realmente merece la pena el esfuerzo para asegurarnos que no acabamos teniendo gazapos extraños resultantes de una modificación accidental de una variable.

```go
func main() {
  sender := NewSender()
  sender.Send(&Item{})
}
```

Mirando el ejemplo precedente, está claro como así también simplificamos el uso de nuestro paquete. Esta forma de ocultar la implementación es beneficiosa, no solo para los mantenedores del paquete, también para los usuarios. Ahora, cuando inicialicemos y usemos la estructura `Sender`, no tenemos que preocuparnos de su implementación. Esto abre las puertas a una arquitectura mucho más flexible y, debido a que nuestros usuarios no deben preocuparse por la implementación, somos libres de cambiarla en cualquier momento, desde que hemos reducido los puntos de contacto que el usuario tiene con el paquete. Si no quisieramos usar la implementación de un canal en nuestro paquete, podríamos facilmente cambiarla sin romper las reglas de uso del método `Send`, siempre que mantengamos la actual firma de la función.

> NOTA: Hay una explicación fantástica sobre como manejar la abstracción en las bibliotecas de cliente, sacada de la charla [AWS re:Invent 2017: Embracing Change without Breaking the World (DEV319)](https://www.youtube.com/watch?v=kJq81Y7OEx4).

## Go limpio

Esta sección se enfoca menos en los aspectos genéricos de escribir código _Go_ limpio y más en los detalles, con énfasis en los principios subyacentes del código limpio.

### Valores devueltos

#### Devolviendo errores predefinidos

Emprezaremos suave y facilmente describiendo una forma más limpia de devolver errores. Como dijimos anteriormente, nuestros objetivos principales al escribir código limpio es asergurar su legibilidad, su testeabilidad, y la mantenibilidad de la base de código. La técnica de devolver errores que veremos aquí puede conseguir esos tres objetivos con muy poco esfuerzo.

Considerando la forma normal de devolver un error a medida, este es un ejemplo hipotético extraido de una implementación de mapa

Consideremos la forma normal de devolver un error personalizado. Este es un ejemplo hipotético tomado de una implementación de mapa segura para subprocesos _(thread-safe)_ que hemos llamado `Store`:

```go
package smelly

func (store *Store) GetItem(id string) (Item, error) {
    store.mtx.Lock()
    defer store.mtx.Unlock()

    item, ok := store.items[id]
    if !ok {
        return Item{}, errors.New("item could not be found in the store")
    }
    return item, nil
}
```

De entrada no notamos que nada huela mal en esta función cuando la vemos aislada. Mira en el mapa `items` de nuestra estructura `Store` para ver si ya tenemos un objeto con el `id` dado, si lo encontramos, lo devolvemos, si no, devolvemos un error. Bastante estándar, así que ¿Cual es el problema de devolver errores personalizados como valores `string`? Bueno, vamos a ver que pasa cuando usarmos esta función dentro de otro paquete:

```go
func GetItemHandler(w http.ReponseWriter, r http.Request) {
    item, err := smelly.GetItem("123")
    if err != nil {
        if err.Error() == "item could not be found in the store" {
            http.Error(w, err.Error(), http.StatusNotFound)
         return
        }
        http.Error(w, errr.Error(), http.StatusInternalServerError)
        return
    }
    json.NewEncoder(w).Encode(item)
}
```

Tampoco es tan malo. Sin embargo hay un problema llamativo: Un error en _Go_ es un simple `interface` que implementa la función (`Error()`), devolviendo un `string`, de este modo estamos escribiendo a fuego, _hardcodeando_, el código de error esperado en nuestro código, y no es lo ideal.

Esta cadena _hardcodeada_ se conoce como **cadena mágica** o _magic string_, y su principal problema es su falta de flexibilidad: Si en algún momento decidimos cambiar el valor de la cadena usada para representar el error, nuestro código se romperá _(suavemente)_ a menos que la actualicemos en, posiblemente, muchos sitios diferentes. Nuestro código está estrechamente acoplado; descansa en una _cadena mágica_ y en la presunción de que esta nunca cambiará mientras crece la base de código.

Una situacieon todavía peor puede surgir si un cliente va a usar nuestro paquete en su propio código. Imagina que decidimos actualizar nuestro paquete y cambiar la cadena que representa un error. El software del cliente se romperá repentinamente. Es bastante obvio que es algo que queremos evitar. Afortunadamente la solución es bastante sencilla:

```go
package clean

var (
    NullItem = Item{}

    ErrItemNotFound = errors.New("item could not be found in the store")
)

func (store *Store) GetItem(id string) (Item, error) {
    store.mtx.Lock()
    defer store.mtx.Unlock()

    item, ok := store.items[id]
    if !ok {
        return NullItem, ErrItemNotFound
    }
    return item, nil
}
```

Simplemente representando el error como una variable (`ErrItemNotFound`), nos hemos asegurado de que cualquiera que use este paquete puede comprobar el error contra la variable en lugar de contra la cadena actual que devuelve:

```go
func GetItemHandler(w http.ReponseWriter, r http.Request) {
    item, err := clean.GetItem("123")
    if err != nil {
        if errors.Is(err, clean.ErrItemNotFound) {
           http.Error(w, err.Error(), http.StatusNotFound)
         return
        }
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    json.NewEncoder(w).Encode(item)
}
```

Parece mucho mejor y mucho más seguro. Hay quién incluso diría que también es más facil de leer. En el caso de de un mensaje de error más largo, es ciertamente preferible para un desarrollador simplemente leer `ErrItemNotFound` en lugar de una novela sobre como se ha devuelto un determinado error.

Esta aproximación no está limitada a errores y puede ser usada para otros valores de retorno. Por ejemplo, también estamos devolviendo `NullItem` en lugar de `Item{}` en el código anterior. Existen muchos escenarios en los que es preferible devolver un objeto predefinido en lugar de inicializarlo en el mismo retorno _(return)_.

Devolver valores `NullItem` como hicimos en los ejemplos previos, también puede ser más seguro en determinados casos. Como ejemplo, un usuario de nuestro paquete, puede olvidarse de comprobar los errores y acabar inicializando una variable que apunte a una estructura conteniendo `nil` como valor de una o más de las propiedades. Cuando intentemos acceder a este valor `nil` más tarde, el software cliente podría entrar en pánico (`panic`). Sin embargo, cuando devolvemos un valor personalizado por defecto, nos aseguramos de que ninguno de los valores sea nulo. De este modo no causaremos un `panic` en el software de nuestros usuarios.

Todo esto también nos beneficia. Ten en cuenta que si queremos conseguir la misma seguridad sin devolver un valor por defecto, tenemos que cambiar nuestro código en todos los puntos en los que devolvamos este tipo de valor vacío. Pero con nuestra aproximación del valor por defecto, solo tenemos que cambiar el código en un punto:

```go
var NullItem = Item{
    itemMap: map[string]Item{},
}
```

> NOTA: En algunos escenarios, invocar un `panic` puede ser preferible para indicar que falta una comprobación de error.

> NOTA: Cada propiedad de interfaz en Go tiene un valor predeterminado de `nil`. Esto es útil para cualquier estructura que tenga una propiedad de interfaz. También es cierto para las estructuras que contienen canales, mapas y sectores, que potencialmente también podrían tener un valor `nil`.

#### Devolviendo errores dinámicos

Existen cietamente algunos escenarios en los que devolver una variable de error no parece viable. En casos en los que la informacón de los errores personalizados es dinámica, si queremos describir los eventos del error más específicamente, no podemos definir y devolver nuestros errores estáticos. Por ejemplo:

```go
func (store *Store) GetItem(id string) (Item, error) {
    store.mtx.Lock()
    defer store.mtx.Unlock()

    item, ok := store.items[id]
    if !ok {
        return NullItem, fmt.Errorf("Could not find item with ID: %s", id)
    }
    return item, nil
}
```

Así que ¿Qué podemos hacer? No hay una forma estándar o bien definida para manejar y devolver este tipo de errores dinámicos. Mi preferencia personal es devolver un nuevo interfaz con alguna funcionalidad añadida:

```go
type ErrorDetails interface {
    Error() string
    Type() string
}

type errDetails struct {
    errtype error
    details interface{}
}

func NewErrorDetails(err error, details ...interface{}) ErrorDetails {
    return &errDetails{
        errtype: err,
        details: details,
    }
}

func (err *errDetails) Error() string {
    return fmt.Sprintf("%v: %v", err.errtype, err.details)
}

func (err *errDetails) Type() error {
    return err.errtype
}
```

Esta nueva estructura todavía funciona como nuestro error estándar. Podemos compararla con `nil`, porque es la implementación de una interfaz, y podemos llamar a `.Error()` desde él, así que no rompe ninguna implementación existente. Sin embargo tiene la ventaja de que ahora podemos comprobar el tipo de error, como podíamos previamente, gracias a nuestro error contiene los detalles dinámicos:

```go
func (store *Store) GetItem(id string) (Item, error) {
    store.mtx.Lock()
    defer store.mtx.Unlock()

    item, ok := store.items[id]
    if !ok {
        return NullItem, NewErrorDetails(
            ErrItemNotFound,
            fmt.Sprintf("could not find item with id: %s", id))
    }
    return item, nil
}
```

Y nuestro _controlador HTTP_ se puede refactorizar para comprobar el tipo concreto de error:

```go
func GetItemHandler(w http.ReponseWriter, r http.Request) {
    item, err := clean.GetItem("123")
    if err != nil {
        if errors.Is(err.Type(), clean.ErrItemNotFound) {
            http.Error(w, err.Error(), http.StatusNotFound)
         return
        }
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    json.NewEncoder(w).Encode(item)
}
```

### Valores nulos

Un aspecto controvertido de _Go_ es la adición de `nil`. Este valor corresponde al valor `NULL` en _C_ y es, esencialmente, un puntero sin inicializar. Ya hemos visto algunos de los problemas que `nil` puede causar, pero para añadir alguno más: Las cosas se pueden romper cuando intentes acceder a métodos o propiedades con valor `nil`. De este modo, se recomienda evitar devolver un `nil` cuando sea posible, así los usuarios de nuestro código evitarán más a menudo acceder accidentalmente a valores nulos.

Hay otros escenarios en los que es común encontrar valores `nil` que pueden causar algún dolor innecesario. Un ejemplo es esta estructura incorrectamente inicializada, que puede llegar a tener propiedades nulas. Si se accede a ellas causarán un `panic`:

```go
type App struct {
   Cache *KVCache
}

type KVCache struct {
  mtx sync.RWMutex
  store map[string]string
}

func (cache *KVCache) Add(key, value string) {
  cache.mtx.Lock()
  defer cache.mtx.Unlock()

  cache.store[key] = value
}
```

Este código está perfectamente bien, pero hay un peligro, y es que `App` puede ser incializada incorrectamente, sin inicializar la propiedad `Cache` internamente. Si se invoca a este código, nuestra aplicación entrará en pánico:

```go
app := App{}
app.Cache.Add("panic", "now")
```

La propiedad `Cache` no ha sido nunca inicializada y es un puntero nulo. Por lo tanto, invocar el método `Add`, como hacemos aquí, causará un `panic`, con el siguiente mensaje:

```bash
panic: runtime error: invalid memory address or nil pointer dereference
```

En su lugar, podemos convertir la propiedad `Cache` en privada, y crear un método de acceso de lectura _"getter"_. Así tendremos más control sobre lo que está volviendo; concretamente aseguramos que no estamos devolviendo un valor `nil`:

```go
type App struct {
   cache *KVCache
}

func (app *App) Cache() *KVCache {
  if app.cache == nil {
      app.cache = NewKVCache()
   }
   return app.cache
}
```

El código que anteriormente entraba en pánico, puede ser refactorizado de la siguiente forma:

```go
app := App{}
app.Cache().Add("panic", "now")
```

Ahora estamos seguros de que los usuarios de nuestro paquete no tienen que preocuparse acerca de la implementación y de si están usuando nuestro paquete de una forma poco segura. Toda su preocupación será escribir su propio código limpio.

> NOTA: Hay otros métodos para conseguir un comportamiento seguro similar. Sin embargo, creo que esta es la aproximación más directa.

### Punteros en _Go_

Los punteros en _Go_ son un tema bastante extenso. Son una parte muy importante del trabajo con el lenguaje; tanto que es esencialmente imposible escribir _Go_ sin algún conocimiento de los punteros y de su funcionamiento en el lenguaje. Por lo tanto, es importante comprender cómo usar punteros sin agregar complejidad innecesaria (y así mantener limpia la base de código). No revisaremos los detalles de cómo se implementan los punteros en _Go_, nos centraremos en las peculiaridades de los punteros _Go_ y en cómo podemos manejarlos.

Los punteros agregan complejidad al código. Si no somos cautelosos, el uso incorrecto de punteros puede generar efectos secundarios desagradables o errores que son particularmente difíciles de depurar. Al apegarnos a los principios básicos de escribir código limpio que cubrimos en la primera parte de este documento, podemos al menos reducir las posibilidades de introducir una complejidad innecesaria en nuestro código.

#### Mutabilidad de punteros

Ya hemos analizado el problema de la mutabilidad en el contexto de variables de ámbito global o amplio. Sin embargo, la mutabilidad no siempre es necesariamente algo malo, y de ninguna manera soy partidario de escribir programas funcionales 100% puros. La mutabilidad es una herramienta poderosa, pero en realidad solo deberíamos usarla cuando sea necesario. Echemos un vistazo a un ejemplo de código que ilustra por qué:

```go
func (store *UserStore) Insert(user *User) error {
    if store.userExists(user.ID) {
        return ErrItemAlreadyExists
    }
    store.users[user.ID] = user
    return nil
}

func (store *UserStore) userExists(id int64) bool {
    _, ok := store.users[id]
    return ok
}
```

A primera vista no parece tan malo. Incluso podría parecer una función simple para insertar un usuario. Aceptamos un puntero y, si no existen otros usuarios con el mismo `ID`, insertamos el puntero de usuario en nuestra lista. Ahora podemos usar esta funcionalidad en un API pública para crear nuevos usuarios:

```go
func CreateUser(w http.ResponseWriter, r *http.Request) {
    user, err := parseUserFromRequest(r)
    if err != nil {
        http.Error(w, err, http.StatusBadRequest)
        return
    }
    if err := insertUser(w, user); err != nil {
      http.Error(w, err, http.StatusInternalServerError)
      return
    }
}

func insertUser(w http.ResponseWriter, user User) error {
   if err := store.Insert(user); err != nil {
        return err
    }
   user.Password = ""
   return json.NewEncoder(w).Encode(user)
}
```

De nuevo a primera vista, todo parece estar correcto. Interpretamos el usuario de la petición recibida, y lo insertamos en nuestro `store`. Cuando lo hemos insertado exitosamente, fijamos la contraseña con una cadena en blanco antes de devolver este usuario como un objeto _JSON_ a nuestro cliente. Es una práctica común, normalmente cuando devolvemos un objeto de usuario la contraseña se ha procesado _(hasheado)_ mediante algún algoritmo, y no queremos devolverla procesada.

Imaginemos que estamos usando un almacenamiento en memoria basado en un `map`. Este código podría producir resultados inesperados. Si revisamos el almacén de usuarios, veremos que el cambio que hicimos a la contraseña en el controlador HTTP también afectó al objeto almacenado. Esto ocurre porque hemos añadido al almacén la dirección del puntero devuelta por `parseUserFromRequest`, en lugar de almacenar el valor al que apunta. Así, cuando hacemos cambios a la contraseña, a su valor _desreferenciado_, acabamos cambiando el valor del objeto apuntado desde nuestro almacén.

Es un buen ejemplo de cómo la mutabilidad y el alcance de la variable pueden causar algunos problemas serios y gazapos cuando se usan incorrectamente. Cuando pasamos punteros con parámetros a una funcíón, estamos expandiendo el ámbito o alcance de las variables a las que apuntan. Todavía más preocupante es el hecho de que lo estamos expandiendo a un nivel indefinido. Estamos _casi_ expandiéndolo al nivel global. Como demostramos en el ejemplo anterior, esto puede llevar a gazapos desastrosos que son particularmente complicados de encontrar y erradicar.

Afortunadamente, la solución es bastante sencilla:

```go
func (store *UserStore) Insert(user User) error {
    if store.userExists(user.ID) {
        return ErrItemAlreadyExists
    }
    store.users[user.ID] = &user
    return nil
}
```

En lugar de pasar el puntero de la estructura `User`, ahora pasamos una copia de `User`. Seguimos guardando un puntero a nuestro almacén, pero en lugar de guardar el puntero de fuera de la función, estamos guardando el puntero del valor copiado, cuyo ámbito es interno. Así arreglamos el primero de los problemas, pero todavía podríamos tener problemas más adelante si no somos cuidadosos. Veamos este código:

```go
func (store *UserStore) Get(id int64) (*User, error) {
    user, ok := store.users[id]
    if !ok {
        return EmptyUser, ErrUserNotFound
    }
    return store.users[id], nil
}
```

De nuevo una implementación muy estándar de una función de lectura o _getter_ en nuestro almacén. Todavía es un mal código porque estamos otra vez expandiendo el alcance de nuestro puntero, que podría terminar causando efectos colaterales inesperados. Al devolver el valor del puntero real, que estamos guardando en nuestro almacén de usuarios, esencialmente estamos dando a otras partes de nuestra aplicación la capacidad de cambiar los valores de dicho almacén. Esto está destinado a causar confusión. Nuestro almacén debe ser la única entidad autorizada a realizar cambios en sus valores. La solución más fácil para esto es devolver un valor de `Usuario` en lugar de devolver un puntero.

> NOTA: Si la aplicación utiliza varios subprocesos o hilos, pasar punteros apuntando a la misma ubicación de memoria, puede resultar en una condición de carrera. En otras palabras, no solo estamos potencialmente corrompiendo nuestros datos, sino que también podríamos causar pánico debido a una condición de carrera de datos _(data race)_.

No hay nada incorrecto en devolver punteros. Sin embargo, el alcance ampliado de las variables (y el número de propietarios que apuntan a esas variables) debe ser la preocupación  más importante cuando se trabaja con punteros. Por eso nuestro ejemplo anterior como una operación _maloliente_, y también es la razón por la cual los constructores comunes de _Go_ están absolutamente bien:

```go
func AddName(user *User, name string) {
    user.Name = name
}
```

Esto es _correcto_ porque el ámbito de la variables, que está definida por quien invoca la función, permanece igual cuando esta vuelve. Combinado con el hecho de que quien invocó a la funcíon permanece como único propietario de la variable, el puntero no puede ser manipulado de ninguna forma inesperada.

### Las clausuras son punteros a funciones

## Qué son las clausuras

En _C_ se conocen como _punteros de función_, y en otros lenguajes de programación se conocen como **clausuras** o **cierres**, en inglés _closures_. Una clausura es simplemente un parámetro de entrada como cualquier otro, excepto que representa o apunta a una función invocable. En _Javascript_ es común usar clausuras como retorno de llamadas, para invocarlas al finalizar alguna operación asíncrona. En _Go_ no tenemos esta noción, pero podemos usar los cierres para superar un obstáculo, la falta de genéricos _(generics)_.

> NOTA: El lenguaje _Go_ dispone de genéricos o _generics_ desde la **versión 1.18**, así que este use podría considerarse obsoleto en alguna parte, aun así resulta interesante conocer esta explicación y podría servirnos en casos en los que el uso de genéricos de _Go_ sea menos útil.

Observe la firma de la siguiente función:

```go
func something(closure func(float64) float64) float64 { ... }
```

La función `something`, acepta otra función, una clausura, como entrada, y devuelve un `float64`. La funcíón de clausura admite un `float64` y también devuelve un `float64`. Este patrón puede ser muy util para crear una arquitectura de acoplamiento flexible, haciendo más facil añadir funcionalidad sin afectar a otras partes del código. Supongamos que tenemos una estructura conteniendo datos que queremos manipular de alguna manera. Usando el método `Do()` de esta estructura, podemos ejecutar operaciones en esos datos.

Here, `something` takes another function (a closure) as input and returns a `float64`. The input function takes a `float64` as input and also returns a `float64`. This pattern can be particularly useful for creating a loosely coupled architecture, making it easier to add functionality without affecting other parts of the code. Suppose we have a struct containing data that we want to manipulate in some form. Through this structure's `Do()` method, we can perform operations on that data. Si conocemos la operación con anticipación podemos manejar esa lógica directamente en nuestro método `Do()`:

```go
func (datastore *Datastore) Do(operation Operation, data []byte) error {
  switch(operation) {
  case COMPARE:
    return datastore.compare(data)
  case CONCAT:
    return datastore.add(data)
  default:
    return ErrUnknownOperation
  }
}
```

Como puede comprobar, esta función es bastante rígida. Realiza una operación predeterminada en los datos contenidos en la estructura `Datastore`. Si en algún momento queremos introducir más operaciones, acabaremos hinchando el método `Do` con un montón de lógica irrelevante que puede ser dificil de mantener. La funcíon tendría que ocuparse siempre de qué operación se está realizando y pasar sobre un cierto número de operaciones anidadas para cada llamada. También podría suponer un problema para los desarrolladores que quisieran usar el objeto `Datastore`, y que no pueden acceder a editar el código de nuestro paquete, ya que no hay forma de extender los métodos de la estructura en _Go_, como se hace en otros lenguages orientados a objetos.

Así que vamos a intentar una aproximación diferente usando clausuras:

```go
func (datastore *Datastore) Do(operation func(data []byte, data []byte) ([]byte, error), data []byte) error {
  result, err := operation(datastore.data, data)
  if err != nil {
    return err
  }
  datastore.data = result
  return nil
}

func concat(a []byte, b []byte) ([]byte, error) {
  ...
}

func main() {
  ...
  datastore.Do(concat, data)
  ...
}
```

Enseguida nos damos cuenta de que la firma de la función `Do` acaba siendo bastante liosa, y además tenemos otro problema: La clausura no es particularmente genérica. ¿Qué pasa si realmente queremos que `concat` pueda admitir más de dos matrices de _bytes_ como entrada? ¿Y si queremos añadir alguna funcionalidad nueva que necesite menos valores de entrada que `(data []byte, data []byte)`?

Una forma de resolverlo es cambiar `concat`. En el siguiente ejemplo la hemos cambiado para que solo admita una matrix de _bytes_, pero podríamos necesitar justo el caso contrario:

```go
func concat(data []byte) func(data []byte) ([]byte, error) {
  return func(concatting []byte) ([]byte, error) {
    return append(data, concatting), nil
  }
}

func (datastore *Datastore) Do(operation func(data []byte) ([]byte, error)) error {
  result, err := operation(datastore.data)
  if err != nil {
    return err
  }
  datastore.data = result
  return nil
}

func main() {
  ...
  datastore.Do(concat(data))
  ...
}
```

Observe cómo hemos movido parte del desorden de `Do` adentro de `concat`. La función `concat` devuelve otra función. En la función devuelta almacenamos los valores de entrada pasados originalmente a nuestra función `concat` y necesita, por tanto, un solo parámetro de entrada. Dentro de la lógica de nuestra función, lo agregaremos a nuestro valor de entrada original. Como concepto nuevo, puede parecer bastante extraño, pero es bueno acostumbrarse a tener esta opción, ya que puede ayudar a flexibilizar el acoplamiento y desahacerse de funciones infladas.

> N. del T.: El propio autor se da cuenta de que esto es lioso y así nos lo hace notar. En mi opinión esto no es, del todo, código limpio y perjudica a la legibilidad. Ruego al lector que las técnicas de código limpio no le impidan tener presente un principio anterior que no por viejo es incorrecto o menos útil: **KISS** o _Keep it simple, stupid!_ Valore siempre qué técnica es mejor, más limpia, más clara, más legible, en cada caso y no se obceque con el _código limpio_ cuando claramente esté afectando a la legibilidad o comprensibilidad de la lógica que está desarrollando.

En la siguiente sección entraremos en los interfaces. Antes de hacerlo, vamos a tomarnos un momento para hablar de la diferencia entre interfaces y clausuras. Vale la pena señalar que interfaces y clausuras pueden, muchas veces, resolver los mismos problemas. La forma en que _Go_ implementa las interfaces puede, a veces, dificultar la decisión de usar interfaces o clausuras para un problema en particular.

Por lo general no es demasiado importante si se usa una interfaz o una clausura. La elección correcta es cualquiera que resulta el problema en cuestión. Las clausuras suelen ser más simples de implementar si la operación es de naturaleza simple. Sin embargo, tan pronto como la lógica que contienen se vuelva compleja, se debe considerar seriamente sustituir la clausura por una interfaz.

Dave Cheney escribió un excelente artículo sobre este particular, así como una charla:

* <https://dave.cheney.net/2016/11/13/do-not-fear-first-class-functions>
* <https://www.youtube.com/watch?v=5buaPyJ0XeQ&t=9s>

Jon Bodner también tiene una charla relacionada:

* <https://www.youtube.com/watch?v=5IKcPMJXkKs>

### Interfaces en _Go_

En general, la aproximación de _Go_ para el manejo de _interfaces_ es bastante diferente de las de otros lenguajes. Las interfaces no se implementan **explicitamente** como en _Java_, por el contrario, se crean implicitamente si cumplen completamente el contrato de la interfaz. Por ejemplo, si cualquier `struct` tiene un método `Error()` que devuelve una cadena, entonces implementa, o cumple, la interfaz `Error` y puede retornarse como un `error`.

Esta forma de implementar interfaces es muy facil y confiere a _Go_ un ritmo más rápido y dinámico.

Sin embargo esta aproximación tiene algunas desventajas. Al no tratarse de una **implementación explícita**, puede ser más dificil ver que interfaces implementa una estructura. Por lo tanto, es común definir interfaces con la menor cantidad de métodos posible, para facilitar la comprensión de si una estructura en particular cumple con el contrato de la interfaz.

> N del T.: Hay extensiones para los IDEs (entornos de desarrollo o editores de código) que facilitan mucho la visualización ya que nos avisan de que interfaces implementa cada estructura o, viendo una interfaz, nos dice que structuras la implementan. Una de estas extensiones es _tooltitude_.

Una alternativa es crear constructores que devuelvan una interfaz en lugar del tipo concreto:

```go
type Writer interface {
 Write(p []byte) (n int, err error)
}

type NullWriter struct {}

func (writer *NullWriter) Write(data []byte) (n int, err error) {
    // do nothing
    return len(data), nil
}

func NewNullWriter() io.Writer {
    return &NullWriter{}
}
```

> N. del T.: Tenga cuidado a la hora de devolver interfaces en lugar de tipos concretos. Esto también rompe, en mi opinión, el principio **KISS**, y dificulta el uso de las estructuras por parte de terceros. Siempre que vaya a hacer algo así, plantéese si es realmente necesario y en como afecta a la legibilidad.

La función anterior asegura que la estructura `NullWriter` implementa la interfaz`Writer`. Si borrásemos el método `Write` de `NullWriter`, tendremos un error de compilación. Es una buena forma de asegurar que nuestro código se comportará como esperamos y que puede contar con ele compilador como red de seguridad en caso de que intentemos escribir código inválido.

En ciertos casos, puede que no sea deseable escribir un constructor, o tal vez nos gustaría que nuestro constructor devuelva el tipo concreto, en lugar de la interfaz. Como ejemplo, la estructura `NullWriter` no tiene propiedades para completar en la inicialización, por lo que escribir un constructor es un poco redundante. Por lo tanto, podemos usar el método menos detallado para verificar la compatibilidad de la interfaz:

```go
type Writer interface {
 Write(p []byte) (n int, err error)
}

type NullWriter struct {}
var _ io.Writer = &NullWriter{}
```

En el código anterior, estamos inicializando una variable con el `identificador en blanco` de _Go_, con la asignación de tipo de `io.Writer`. Esto da como resultado que nuestra variable sea verificada para cumplir con el contrato de interfaz `io.Writer`, antes de ser descartada. Este método de verificación del cumplimiento de la interfaz también permite verificar que se cumplan varios contratos de interfaz:

```go
type NullReaderWriter struct{}
var _ io.Writer = &NullWriter{}
var _ io.Reader = &NullWriter{}
```

Del código anterior, es muy fácil entender qué interfaces deben cumplirse, y el compilador lo revisará en tiempo de compilación. Esta es generalmente la solución preferida para verificar el cumplimiento del contrato de interfaz.

> N. del T.: Actualmente, el compilador de _Go_ nos avisará y se negará a compilar siempre que intentemos utilizar una estructura como argumento cuando se nos pida una determinada interfaz y esta estructura no cumpla el contrato de dicha interfaz. Sin embargo lo explicado podría ser util en un paquete en el que vayamos a devolver esta estructura, que debería implementar una determinada interfaz externa, de otro paquete.

Todavía hay otro método para intentar ser más explícito acerca de que interfaces implementa una determinada estructura. Sin embargo este tercer método consigue realmente lo contrario de lo que queremos. Implica utilizar interfaces embebidas como propiedades de una estructura.

> _Espera, ¿Qué?_ – Seguramente la mayoría de los lectores.

Rebobinemos un poco hasta antes de entrar en profundifad en el bosque del _Go maloliente_. En _Go_, podemos usar estructuras embebidas como una especie de herencia en las definiones de las estructuras. Esto es muy interesante para desacoplar nuestro código utilizando estructuras reutilizables.

```go
type Metadata struct {
    CreatedBy types.User
}

type Document struct {
    *Metadata
    Title string
    Body string
}

type AudioFile struct {
    *Metadata
    Title string
    Body string
}
```

Estamos definiento un objeto `Metadata` que nos proveerá de propiedades, campos, que podemos usar en muchas otras estructuras. Lo mejor de inscrustar esta estructura, en lugar de definir explícitamente las propiedades, es que hemos desacoplado los campos de `Metadata`.  Si queremos actualizar el objeto `Metadata`, pordemos cambiarlo en un solo punto. Por supuesto, queremos que un cambio en un punto de nuestro código no rompa cosas en otros puntos. Mantener estas propiedades centralizadas, deja en claro que las estructuras que embeban `Metadata`, tendrán esas mismas propiedades, al igual que las estructuras que cumplen con las interfaces tienen los mismos métodos.

> N. del T.: Cuando el autor dice las mismas propiedades y los mismos métodos, se refiere al _mínimo común divisor_, es decir, a las propiedades definidas por la estructura embebida y por el contrato de la interfaz. Por supuesto, las estructuras que embeben o las estructuras que cumplen o implementar interfaces, pueden tener propiedades y métodos adicionales.

Veamos un ejemplo de como podemos usar un constructor para prevenir la ruptura de nuestro código cuando hagamos cambios a la estructura `Metadata`:

```go
func NewMetadata(user types.User) Metadata {
    return &Metadata{
        CreatedBy: user,
    }
}

func NewDocument(title string, body string) Document {
    return Document{
        Metadata: NewMetadata(),
        Title: title,
        Body: body,
    }
}
```

> N. del T.: Este código no puede funcionar, falta el argumento `user` en la llamada a `NewMetadata()`, por no mencionar lo poco limpio que resultaría tener un paquete `types` conteniendo nuestras entidades como `User`. Se ruega al lector un pequeño esfuerzo de abstracción para entender la idea que nos quieren transmitir.

Supongamos que queremos, más tarde, añadir un campo `CreatedAt` a nuestro objeto `Metadata`. Ahora podemos hacerlo facilmente actualizando el constructor `NewMetadata`:

```go
func NewMetadata(user types.User) Metadata {
    return &Metadata{
        CreatedBy: user,
        CreatedAt: time.Now(),
    }
}
```

Ahora ambas estructuras, `Document` y `AudioFile`, están actualizadas y rellenan esos campos en su construcción. Este es el principio fundamental detrás del desacoplamiento, y es un ejemplo excelente de como asegurar la mantenibilidad del código. También podemos añadir nuevos métodos sin romper el código existente:

```go
type Metadata struct {
    CreatedBy types.User
    CreatedAt time.Time
    UpdatedBy types.User
    UpdatedAt time.Time
}

func (metadata *Metadata) AddUpdateInfo(user types.User) {
    metadata.UpdatedBy = user
    metadata.UpdatedAt = time.Now()
}
```

De nuevo, sin romper el resto del código, hemos conseguido introducir nueva funcionalidad. Esta clase de programación hace que implementar nuevas características sea muy rápido e indoloro, que es exactamente lo que estamos intentando al escribir código limpio.

Volvamos a la cuestión del cumplimiento de contrato utilizando interfaces incrustadas. Veamos el siguiente ejemplo:

```go
type NullWriter struct {
    Writer
}

func NewNullWriter() io.Writer {
    return &NullWriter{}
}
```

El siguiente código compila. Técnicamente estamos implementando la interfaz `Writter` en nuestro `NullWriter`, debido a que `NullWriter` heredará todas las funciones que están asociadas a esta interfaz. Algunos lo verán como una forma de mostrar que `NullWriter` está implementando la interfaz `Writer`, sin embargo, debemos tener cuidado al usar esta técnica.

```go
func main() {
    w := NewNullWriter()

    w.Write([]byte{1, 2, 3})
}
```

Como dijimos, este código compilará. El `NewNullWriter` devuelve un `Writer`, y todo va como miel sobre hojuelas según el compilador, porque `NullWriter` cumple con el contrato de `io.Writer`, a través de la interfaz incrustada. Sin embargo, correr el código anterior resultará en:

> panic: runtime error: invalid memory address or nil pointer dereference

¿Qué ha ocurrido? Un método de una interfaz en _Go_ es, esencialemte, un puntero a una función. En este caso, como estamos apuntando a la función de una interfaz en lugar de a la implementación real del método, estamos intentando invocar a una función que realmente es un **puntero nulo**. Para evitar que esto suceda, tenemos que proporcionar a `NulllWriter` una estructura que cumpla con el contrato de interfaz, con métodos reales implementados.

```go
func main() {
  w := NullWriter{
    Writer: &bytes.Buffer{},
  }

    w.Write([]byte{1, 2, 3})
}
```

> NOTA: En el ejemplo anterior, `Writer` se refiere a la interfaz `io.Writer` embebida. También es posible invocar el método `Write` accediendo a esta propiedad con: `w.Writer.Write()`.

Ya no estamos disparando un `panic` y ahora podemos usar `NullWriter` como `Writer`. Esta incialización no es muy diferente de tener propiedades que son inicializadas como `nil`, como dijimos previamente. Logicamente, debemos intentar manejarlas de la misma forma. Sin embargo, así es como las interfaces incrustadas se vuelven un poco difíciles de usar. En una sección previa, explicamos que la mejor forma de manejar potenciales valores nulos es la propiedad en cuestión privada, y crear un método _getter_ público. De esta forma podemos asegurarnos de que la propiedad no es `nil`. Desafortunadamente no es posible con interfaces incrustadas, ya que **son públicas** por su propia naturaleza.

Se nos plantea otra preocupación por el uso de interfaces incrustadas, y es la posible confusión causada por métodos de interfaz parcialmente sobreescritos:

```go
type MyReadCloser struct {
  io.ReadCloser
}

func (closer *ReadCloser) Read(data []byte) { ... }

func main() {
  closer := MyReadCloser{}

  closer.Read([]byte{1, 2, 3})  // works fine
  closer.Close()   // causes panic
  closer.ReadCloser.Closer()   // no panic
}
```

Aunque pensemos que parece que estemos sobrecargando métodos, que es común en lenguajes como _C#_ y _Java_, no lo estamos haciendo realmente. _Go_ no soporta herencia, y por tanto no existen las superclases. Podemos imitar el comportamiento, pero no es una parte intrínseca del lenguaje. Si no somos precavidos al incrustar una interfaz, podemos crear código confuso y potencialmente defectuoso, solo para ahorrar algunas líneas de más.

> NOTA: Algunos argumentan que el uso de interfaces incrustadas es una buena forma de crear una estructura simulada _(mock)_, para probar un subconjunto de métodos de interfaz. Al usar una interfaz incrustada, no tendrá que implementar todos los métodos de la interfaz, puede optar por implementar solo los que le gustaría probar. Dentro del contexto de _testing/mocking_, puedo entender este argumento, pero no soy un fanático de su enfoque.

Echemos un vistazo atrás, al uso apropiado de interfaces para codificar limpiamente. Es el momento de hablar sobre usar interfaces como parámetros de una función y como valor de retorno. El proverbio más común para el uso de interfaces con funciones en _Go_ es el siguiente:

> _Sé conservador en lo que haces; sé liberal en lo que aceptas de los demás._ – Jon Postel
>
> DATO DIVERTIDO: En realidad este proverbio no tiene nada que ver con _Go_. Lo hemos tomado prestado de una especificación temprana del protocolo de red _TCP_.

En otras palabras, debes escribir funciones que acepten una interfaz y devuelvan un tipo concreto. Es una buena práctica, generalmente, y es especialmente útil cuando se realizan pruebas simuladas o _mockeadas_. Por ejemplo, podemos crear una función que acepte como entrada una interfaz de escritura e invoque a su método `Write`:

```go
type Pipe struct {
    writer io.Writer
    buffer bytes.Buffer
}

func NewPipe(w io.Writer) *Pipe {
    return &Pipe{
        writer: w,
    }
}

func (pipe *Pipe) Save() error {
    if _, err := pipe.writer.Write(pipe.FlushBuffer()); err != nil {
        return err
    }
    return nil
}
```

Supongamos que estamos escribiendo un archivo, desde nuestra aplicación en ejecución, pero no queremos crear un archivo nuevo para todas las pruebas que invocan esta función. Podemos implementar un _mock_, o simulador, que básicamente no hará nada. Consiste simplemente en una inyectar este _mock_ como dependencia en lugar de la real, y esto es muy sencillo de conseguir en _Go_:

```go
type NullWriter struct {}

func (w *NullWriter) Write(data []byte) (int, error) {
    return len(data), nil
}

func TestFn(t *testing.T) {
    ...
    pipe := NewPipe(NullWriter{})
    ...
}
```

> NOTA: En realidad ya existe una implementación de un _null writer_ llamada `Discard`, dentro del paquete `iuoutil`.

Al construir nuestra estructura `Pipe` con `NullWriter` (en lugar de otro _writer_), no sucederá nada cuando llamemos a `Save()`, silenciosamente descartará la operación. Sólo hemos agregado cuatro líneas de código. Por eso se recomienda que las interfaces sean lo más pequeñas posibles en un _Go_ idiomático: hace que sea más facil implementar patrones como el que acabamos de ver. Sin embargo, esta implementación de interfaces también tiene un _gran inconveniente_.

### La interfaz vacía

En versiones anteriores a la _1.18_, Go no disponía de una implementación de genéricos o _generics_, ahora ya la tiene y debes tenerlo en cuenta antes de leer este punto, que se pensó antes de que existiera dicha implmentación. Aun así la reseñamos a continuación, ya que puede ser util en determinados casos.

Los programadores se volvieron un poco creativos para encontrar alternativas antes de disponer de genéricos, la mayor parte de las veces usando la interfaz vacía. Esta sección describe como, a menudo, estas implementaciones deben ser consideradas una mala práctica y un _código no limpio_. Habrá también ejemplos del uso de la interfaz vacía y como evitar algunos de sus inconvenientes.

Ya mencionamos que _Go_ determina cuando un tipo (`type`) implementa una interface comprobando si dicho tipo **cumple con el contrato de la interfaz**, es decir, que implementa todos los métodos reseñados en dicha interfaz. Así que, ¿Qué ocurre cuando una interface no declara ningún método?

```go
type EmptyInterface interface {}
```

> N. del T.: En versiones modernas de _Go_ hay un nuevo tipo integrado, `any`, que es equivalente a `interface{}`. Si bien muchos programadores están acostumbrados a `interface{}` y lo entenderán perfectamente, podría ser más idiomático utilizar `any`.

Esta declaración equivale al tipo integrado `interface{}` de _Go_. Una consecuencia natural de esto es que podemos escribir funciones genéricas que acepten cualquier tipo de argumentos, lo que es muy útil para algunas clases de funciones, como podrían ser funciones de ayuda a la impresión o _print helpers_, como en la función `Println` del paquete `fmt`:

```go
func Println(v ...interface{}) {
    ...
}
```

En este caso, `Println` incluso acepta más de un sólo parámetro de cualquier tipo, acepta un _slice_ de parámatros que implementen `interface{}`, la interfaz vacía. Como esta interfaz no tiene métodos asociados, se aceptan **todos los tipos**, haciendo también posible alimentar `Println` con un _slice_ de diferentes tipos. Es un patrón muy común al manejar conversión de cadenas (desde y hacia una cadena). En la biblioteca estándar de `json` hay algunos buenos ejemplos:

```go
func InsertItemHandler(w http.ResponseWriter, r *http.Request) {
    var item Item
    if err := json.NewDecoder(r.Body).Decode(&item); err != nil {
        http.Error(w, err.Error(), http.StatusBadRequest)
        return
    }

    if err := db.InsertItem(item); err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    w.WriteHeader(http.StatsOK)
}
```

El código menos elegante es el contenido dentro de la función `Decode`. Por lo tanto, cuando usemos esta funcionalidad no tendremos que preocuparnos por la reflexión o conversión de tipos, sólo tendremos que proporcionar un puntero a un tipo concreto. Así `Decode` rellenará `item`, pasado por referencia, y no tendremos que lidiar con los riesgos potenciales de manejar el valor `interfaz{}` por nosotros mismos.

Sin embargo, aun usando `interface{}` con buenas prácticas, podemos tener algunos problemas. Si pasamos una cadena _JSON_ válida, pero que no tiene nada que ver con el tipo `Item`, no recibiremos ningún error y la variable `item` quedará con los valores predeterminados. Por tanto no tenemos que preocuparnos por errores de reflexión o conversión pero sí de que el mensaje enviado por nuestro cliente sea de un tipo `Item` válido. Desafortunadamente, al momento de escribir este documento, no existe una manera simple o buena de implementar este tipo de decodificadores genéricos sin usar el tipo `interface{}` vacío.

> N. del T.: Para mi esto es una decodificación típica de JSON, y hasta donde alcanza mi experiencia lo lógico es que funcione así. Decodificamos los datos aportados por el cliente sobre la estructura de campos, normalmente para escribir un registro en la base de datos, y **aplicamos las validaciones pertinentes** antes de guardarlo, es así en cada lenguaje o framework. Hay diversos paquetes para _Go_ que pueden hacer una muy buena validación utilizando etiquetas en la estructura, véase por ejemplo [el paquete validator en GitHub](https://github.com/go-playground/validator).

El problema al usar `interface{}` de esta forma es que estamos usando _Go_, un lenguaje de tipado estático, como un lenguaje de tipado dinámico.

> N. del T.: El tipado estático, también conocido como fuerte o duro, consiste en que todas las variables tienen un tipo y este tipo es inamovible. Por el contrario, en un lenguaje de tipado dinámico, débil o blando, las variables pueden cambiar de tipo en cualquier momento, así como los parámetros, que pueden ser de cualquier tipo. El tipado blando es más cómodo para empezar a programar, a veces es más rápido y fácil, pero es muy propenso a errores, especialmente errores en tiempo de ejecución.

El problema es más claro todavía cuando se observan implementaciones deficientes del tipo `interface{}`. Un ejemplo común son las stores genéricas o las listas de algún tipo.

Echemos un vistazo a un ejemplo que intenta implementar un paquete de `HashMap` genérico que pueda almacenar cualquier tipo usando `interface{}`:

```go
type HashMap struct {
    store map[string]interface{}
}

func (hashmap *HashMap) Insert(key string, value interface{}) {
    hashmap.store[key] = value
}

func (hashmap *HashMap) Get(key string) (interface{}, error) {
    value, ok := hashmap.store[key]
    if !ok {
        return nil, ErrKeyNotFoundInHashMap
    }
    return value
}
```

> NOTA: Hemos omitido la seguridad entre procesos _(thread safety)_, para hacerlo más sencillo.

Por favor, tenga en mente que el patrón de implementación mostrado arriba se usa realmente en un montón de paquetes de _Go_. Se usa incluso en la biblioteca estándar `sync`, para el tipo `sync.Map`. Entonces, ¿Cual es el problema con esta implementación? Bueno, echemos un vistazo a un ejemplo de uso de este paquete:

```go
func SomeFunction(id string) (Item, error) {
    itemIface, err := hashmap.Get(id)
    if err != nil {
        return EmptyItem, err
    }
    item, ok := itemIface.(Item)
    if !ok {
        return EmptyItem, ErrCastingItem
    }
    return item, nil
}
```

En un primer vistazo, todo parece correcto. Sin embargo, empezaremos a estar en problemas si añadimos tipos _diferentes_ a nuestro almacén, lo que está actualmente permitido. Nada nos impide añadir cualquier otra cosa además del tipo `Item`, entonces, ¿Qué pasa cuando alguien empiece a añadir otros tipos en nuestro _HashMap_, como un puntero `*Item` en lugar de un `Item`? La función podría devolver un error, y, peor aún, es posible que nuestras pruebas ni siquiera lo detecten. Dependiendo de la complejidad del sistema, esto podría introducir algunos errors particularmente difíciles de depurar.

Esta clase de código nunca debería llegar a producción. **Recuerda:** _Go_ no soporta todavía genéricos (N. del T.: No soportaba genéricos antes de la v 1.18). Es sólo un hecho que los desarrolladores deben aceptar por ahora. Si queremos genéricos, deberíamos usar un lenguaje diferente que soporte genéricos en lugar de utilizar trucos peligrosos.

¿Como evitamos que este código alcance producción? La solución más simple es sencillamente escribir las funciones con los tipos concretos en lugar de usar valores `interface{}`. Por supuesto no es siempre el mejor enfoque, ya que puede haber funcionalidades dentro del paquete que no son triviales de implementar. Otra forma podría ser crear envoltorios que expongan la funcionalidad que necesitamos, garantizando a la vez la seguridad de los tipos:

```go
type ItemCache struct {
  kv tinykv.KV
}

func (cache *ItemCache) Get(id string) (Item, error) {
  value, ok := cache.kv.Get(id)
  if !ok {
    return EmptyItem, ErrItemNotFound
  }
  return interfaceToItem(value)
}

func interfaceToItem(v interface{}) (Item, error) {
  item, ok := v.(Item)
  if !ok {
    return EmptyItem, ErrCouldNotCastItem
  }
  return item, nil
}

func (cache *ItemCache) Put(id string, item Item) error {
  return cache.kv.Put(id, item)
}
```

> NOTA: La implementación de otras funcionalidades del cache `tinykv.KV` se han omitido en pos de la brevedad.

El envoltorio de arriba asegura que estamos usando los tipos reales ya que no estamos pasando un tipo `interface{}`. Ya no es posible poblar nuestro almacén accidentalmente con un valor de tipo incorrecto, y hemos restringido la conversión de tipos tanto como era posible. Es una forma muy sencilla de resolver el problema, aunque sea un poco manual.

## Sumario

En primer lugar, ¡Gracias por leer este artículo de arriba a abajo! Espero
First of all, thank you for making it all the way through this article! Espero que te haya dado información sobre el _código limpio_ y sobre cómo ayuda a garantizar la capacidad de mantenimiento, la legibilidad y la estabilidad en cualquier base de código.

Resumamos brevemente todos los temas que hemos tratado:

* **Funciones**: El nombre de una función debe reflejar su alcance. Cuanto menor sea el alcance de una función, más específico será su nombre. Asegúrese de que todas las funciones tengan un solo propósito y lo hagan en la menor cantidad de líneas posible. Una buena regla general es limitar sus funciones a entre 5 y 8 líneas y aceptar solo 2 ó 3 argumentos.

* **Variables**: A diferencia de las funciones, las variables deben tener nombres más genéricos a medida que su alcance se vuelve más pequeño. También se recomienda limitar el alcance de una variable tanto como sea posible para evitar modificaciones no intencionadas. Su valor se debe modificar lo menos posible, lo que es especialmente importante a medida que crece su alcance.

* **Valores devueltos**: Devolver tipos concretos siempre que sea posible. Haz que los usuarios de tu paquete tengan muy difícil cometer erroes y que les resulte muy facil entender los valores devueltos por sus funciones.

* **Punteros**: Usa los punteros con precaución, y limita su ámbito y mutabilidad al mínimo posible. **Recuerda**: El recolector de basura sólo te ayuda con el manejo de memoria, pero no lo hace con el resto de complejidades asociadas con los punteros.

* **Interfaces**: Utiliza interfaces tanto como sea posible para flexibilizar el acoplamiento de tu código. Oculta cualquier código que utilice `interface{}`, si es posible, de tus usuarios, evitando que quede expuesto.

Como nota final, vale la pena mencionar que las nociones sobre _código limpio_ son **particularmente subjetivas**, y eso probablemente nunca cambiará. Sin embargo, al igual que mi declaración sobre `gofmt`, creo que es más importante encontrar un estándar común que algo con lo que todos estén de acuerdo; esto último es extremadamente difícil de lograr.

> N. del T.: Intenta ser tolerante, y si en algún momento piensas que estás escribiendo código perfecto y que eres una especie de oráculo de la programación, mándame un fichero de ejemplo y encontraré la forma de bajarte los humos, es por tu bien.

También es importante comprender que el fanatismo nunca es el objetivo del _código limpio_. Lo más probable es que una base de código nunca esté completamente _"limpia"_, de la misma manera que el escritorio de su oficina probablemente tampoco lo esté. Sin duda, hay espacio para que te saltes las reglas y los límites cubiertos en este artículo. Sin embargo, recuerda que la razón más importante para escribir un _código limpio_, es ayudarte a tí mismo y a otros desarrolladores. Apoyamos a los ingenieros asegurando la estabilidad en el software que producimos y facilitando la depuración del código defectuoso. Ayudamos a nuestros compañeros desarrolladores asegurándonos de que nuestro código sea legible y fácil de digerir. Ayudamos a **todos** los involucrados en el proyecto mediante el establecimiento de una base de código flexible que nos permite introducir rápidamente nuevas funciones sin romper nuestra plataforma actual. **Avanzamos rápido yendo despacio**, y todos están satisfechos.

Espero que se una a esta discusión para ayudar a la comunidad de Go a definir (y refinar) el concepto de _código limpio_. Establezcamos un terreno común para que podamos mejorar el software, no solo para nosotros sino para el bien de todos.

> N. del T.: El autor no es ningún tonto, en esto estaremos todos de acuerdo, y nos da pistas muy importantes que tendemos a obviar, como **"Avanzamos rápido yendo despacio"**. Escribir código limpio es, a veces, más lento que escribir simplemente el código que escribiríamos naturalmente para cubrir el expediente y sacar el proyecto adelante, y quizá no sería código malo o sucio, simplemente a la hora de necesitar adaptarlo a otros sistemas, hacer más tests o compartirlo con compañeros, necesitaríamos limpiarlo.
>
> Así que, conociendo todo lo expuesto, que es de suma importancia, no seas fanático. Hay cosas que se podrán hacer bien desde el inicio, y hay otras que no, que funcionarán y que se podrán mejorar más adelante, con una refactorización programada o bien cuando sea realmente necesario. Lo interesante es tener el la cabeza el paradigma y tratar de trabajar con él siempre que sea posible.
>
> La sola lectura de este artículo, por otra parte, te habrá hecho crecer como programador. Conmigo lo hizo, y por eso decidí traducirlo al español, por si podía facilitarte su lectura y de paso aportar alguna modesta nota o idea.
