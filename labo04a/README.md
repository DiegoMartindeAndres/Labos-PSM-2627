# Funciones **Lambda** en Kotlin

# Fuentes
Adaptado del curso de Android Developers Codelabs: 

[Cómo usar tipos de funciones y expresiones lambda en Kotlin](https://developer.android.com/codelabs/basic-android-kotlin-compose-function-types-and-lambda)

# 📘 1. Introducción

Bienvenido al mundo de las funciones lambda en Kotlin. En este laboratorio, aprenderás sobre los tipos de funciones, cómo usarlas y la sintaxis específica de las expresiones lambda.

En Kotlin, las funciones se consideran construcciones de primera clase. Esto significa que las funciones se pueden tratar como un tipo de dato. Puedes almacenar funciones en variables, pasarlas a otras funciones como argumentos e incluso retornarlas desde otras funciones.

Al igual que otros tipos de datos que puedes expresar con valores literales, como el tipo `Int` con el valor `10` o el tipo `String` con el valor `"Hello"`, también puedes declarar literales de funciones, que se llaman **expresiones lambda**, o simplemente, **lambdas**. Las lambdas se usan mucho en el desarrollo de Android y, en general, en la programación en Kotlin. Así como otros leguajes de programación modernos, Kotlin permite que las lambdas sean más concisas y fáciles de leer.

## 📚 Qué aprenderás

- Cómo definir una función con sintaxis lambda.
- Cómo almacenar funciones en variables.
- Cómo pasar funciones como argumentos a otras funciones.
- Cómo devolver funciones desde otras funciones.
- Cómo usar tipos de funciones anulables.
- Cómo hacer que las expresiones lambda sean más concisas.
- Qué es una función de **orden superior**.
- Cómo usar la función `repeat()`.

## 🔧 Requisitos

- Android Studio ó
- Un navegador web con acceso a [**Playground de Kotlin**](https://play.kotlinlang.org/)

# 2. Programando un “Truco o Trato” con Lambdas 🎃🍬

En esta sección aprenderás a utilizar **tipos de función** y **expresiones lambda** en Kotlin para crear un pequeño programa de _truco o trato_.  
Este ejemplo te ayudará a comprender cómo las funciones pueden tratarse como valores y pasarse entre variables o incluso a otras funciones.

---

## 🎃 Definamos nuestra primera función: `trick()`

Hasta ahora, las funciones que has visto en Kotlin probablemente eran llamadas directamente desde `main()`.  
Ahora daremos un paso más: **vamos a declarar una función** usando la palabra clave `fun`.

Crea una función llamada `trick` que muestre un mensaje en consola cuando alguien no recibe caramelos:

```kotlin
fun trick() {
    println("¡No hay golosinas!")
}
````

Esta función no recibe parámetros ni devuelve valores: simplemente imprime un mensaje.

---

## ⚙️ Asignando una función a una variable

En Kotlin, **las funciones también son valores**.
Esto significa que puedes almacenarlas en variables, pasarlas como argumentos o devolverlas desde otras funciones, igual que harías con números o cadenas de texto.

Por ejemplo, intentemos guardar la función `trick` dentro de una variable llamada `trickFunction`:

```kotlin
fun main() {
    val trickFunction = trick // 🚫 Esto generará un error
}
```

Si pruebas este código, obtendrás un **error de compilación**.
¿Por qué? Porque Kotlin no sabe que quieres **referirte a la función en sí** y no ejecutarla.
Para eso, existe el **operador de referencia de función `::`**, que indica al compilador que estás pasando la función como valor.

---

## ✅ La forma correcta con el operador `::`

Así es como debería quedar el código:

```kotlin
fun main() {
    val trickFunction = ::trick
}
```

Ahora `trickFunction` es una **referencia a la función `trick`**, y puedes usarla más adelante para ejecutarla, pasarla a otra función, o incluso cambiarla por otra que cumpla el mismo tipo de función.

---

### 💡 En resumen

* `fun` se usa para **declarar funciones**.
* El operador `::` permite **referirse a una función como valor**, sin ejecutarla.
* Las funciones en Kotlin **pueden almacenarse en variables**, lo que permite construir programas más flexibles y expresivos.

## 💡 Convertir una función en una expresión lambda

Ahora haremos que el código sea más conciso transformando la función `trick()` en una **expresión lambda**.

Una **lambda** es una forma más compacta de definir una función sin necesidad de usar la palabra clave `fun` ni asignarle un nombre. En lugar de declarar la función con `fun`, podemos crear una variable (`val`) y asignarle directamente una lambda.

Para convertir `trick()` en una lambda, basta con:
1. Eliminar la palabra clave `fun`.
2. Sustituirla por `val` (ya que ahora será una variable que guarda la función).
3. Eliminar los paréntesis del nombre.
4. Añadir el operador `=` para asignar el bloque de código.

Así quedará:

```kotlin
val trick = {
    println("¡No hay golosinas!")
}
````

En este caso, `trick` es una **variable que contiene una función anónima**.
Ya no necesitamos usar el operador de referencia `::`, porque `trick` **ya representa directamente una función**.

Podemos ejecutarla simplemente usando paréntesis:

```kotlin
fun main() {
    val trickFunction = trick
    trickFunction()
}
```

Al ejecutar el código, verás en la consola el mensaje:

```
¡No hay golosinas!
```


### 🧠 En resumen

* Las **expresiones lambda** te permiten definir funciones sin nombre de forma más breve.
* Al usar `val`, la lambda se comporta como una variable que contiene una función.
* Ya no necesitas el operador `::` para referirte a ella.
* Se ejecutan añadiendo `()` al final, igual que cualquier función.

---

## 🍬 ¿Y qué pasa si la función necesita un parámetro o devuelve un valor?

Hasta ahora, nuestras funciones eran muy simples: no recibían datos ni devolvían resultados.  
Pero, ¿qué hacemos si queremos que la función reciba un **parámetro** o devuelva un **valor**?

Vamos a verlo creando una nueva función llamada `treat`, que representará el _trato_ del programa “Truco o Trato” 🎃🍬.  
Esta función devolverá un mensaje con la cantidad de golosinas ofrecidas.


## 🍬 Añadiendo la función `treat`

Ahora que la función `trick` está lista, vamos a crear su contraparte: **`treat`**, una función que representa el _trato_ y que devolverá un mensaje con la cantidad de golosinas ofrecidas.

Esta vez definiremos `treat` directamente como una **expresión lambda con parámetro y valor de retorno**.

---

### 🎯 Definición con tipo de función

Podemos declarar explícitamente el tipo de la función lambda indicando:
- Los **parámetros** entre paréntesis `()`.
- Una **flecha** `->`.
- El **tipo de valor devuelto**.

Así definimos que `treat` recibe un `Int` y devuelve un `String`:

```kotlin
val treat: (Int) -> String = { cantidad ->
    "Aquí tienes $cantidad golosinas!"
}
````

📘 En este ejemplo:

* `(Int) -> String` es el **tipo de la función**.
* `cantidad` es el **parámetro de entrada**.
* La cadena de texto que devuelve es el **resultado**.

---

### 🧩 Funciones sin parámetros o sin retorno

Si una función **no recibe ningún parámetro**, se usan paréntesis vacíos `()`:

```kotlin
val trick: () -> Unit = { 
    println("¡No hay golosinas!") 
}
```

En este caso:

* `()` indica que **no hay parámetros**.
* `Unit` indica que **no devuelve ningún valor** (similar a `void` en otros lenguajes).

Esto significa que `trick` es una función que simplemente **ejecuta una acción**, sin devolver nada.

---

### 🚀 Resumen rápido

| Caso                          | Sintaxis          | Descripción                          |
| ----------------------------- | ----------------- | ------------------------------------ |
| Sin parámetros, sin retorno   | `() -> Unit`      | No recibe ni devuelve valores        |
| Con un parámetro, con retorno | `(Int) -> String` | Recibe un entero y devuelve un texto |

---

✅ Ya tienes dos funciones (`trick` y `treat`) declaradas como lambdas, listas para usarse en tu programa de *Truco o Trato*.

---

## 🔄 Creando una función de orden superior: `trickOrTreat`

Hasta ahora, hemos visto que las funciones pueden **almacenarse en variables** y **recibir parámetros**.  
Pero en Kotlin podemos ir aún más lejos: también es posible **devolver funciones** desde otras funciones.  
A este tipo de funciones se les llama **funciones de orden superior**.

💡 **Definición:**  
Una función de orden superior es aquella que **recibe o devuelve otra función**.

---

### 🎃 Nuestra función `trickOrTreat`

Vamos a crear una función llamada `trickOrTreat` que devolverá **una función lambda** (`() -> Unit`).  
Esta nueva función tomará un argumento booleano y decidirá si devuelve `trick` o `treat`, según el valor del argumento.

```kotlin
// Esta función devuelve otra función
fun trickOrTreat(esTruco: Boolean): () -> Unit {
    if (esTruco) {
        // Si es un truco, devolvemos la función trick
        return trick
    } else {
        // Si no, devolvemos un bloque que ejecuta treat(8)
        return {
            println(treat(8))
        }
    }
}
````

📘 En este ejemplo:

* `trickOrTreat` **recibe** un parámetro `esTruco` de tipo `Boolean`.
* **Devuelve** una función que no tiene parámetros y no devuelve nada (`() -> Unit`).
* Si `esTruco` es `true`, devuelve `trick`.
* Si `esTruco` es `false`, devuelve una lambda que ejecuta `treat(8)` e imprime el resultado.

---

### 🧪 Probando en `main()`

Podemos usar `trickOrTreat` para obtener una función, guardarla en una variable y luego llamarla con `()`.

```kotlin
fun main() {
    val treatFunction = trickOrTreat(false)
    val trickFunction = trickOrTreat(true)

    treatFunction()
    trickFunction()
}
```

---

### 🧩 Resultado esperado

Al ejecutar el código, la salida será:

```
Aquí tienes 8 golosinas!
¡No hay golosinas!
```

Dependiendo del valor del parámetro, la función `trickOrTreat` **decide qué comportamiento devolver**:
un *truco* 🎃 o un *trato* 🍬.

---

### 🚀 En resumen

* Las **funciones de orden superior** pueden **devolver o recibir otras funciones**.
* En este caso, `trickOrTreat` devuelve una función de tipo `() -> Unit`.
* Esto permite construir código **modular, reutilizable y flexible**, ideal para escenarios donde el comportamiento depende de una condición.


## 🎁 Añadiendo un extra de golosinas

¿Y si quisiéramos personalizar nuestro programa para dar **golosinas adicionales**? 🍬  
Podemos hacerlo añadiendo un **segundo parámetro** a la función `trickOrTreat`, que también será una función.

---

### 🎯 Pasar una función como argumento

Vamos a ampliar `trickOrTreat` para que reciba un parámetro llamado `extraTreat`.  
Este parámetro será una **función** que toma un `Int` (la cantidad de golosinas extra) y devuelve un `String` con el mensaje correspondiente.

Así definimos su firma:

```kotlin
fun trickOrTreat(
    esTruco: Boolean,
    extraTreat: (Int) -> String //Se recibe una función como parámetro
): () -> Unit {
    if (esTruco) {
        return trick
    } else {
        // Ejecutamos la función extraTreat con un valor de 5
        println(extraTreat(5))
        println(treat(8))
        return {}
    }
}

```

---

### 🧠 ¿Qué está pasando aquí?

* `extraTreat` es un **parámetro funcional**.
  Es decir, en lugar de pasarle un número o texto, le pasamos **otra función**.
* Esa función (`extraTreat`) recibe un entero y devuelve un mensaje.
* Dentro de `trickOrTreat`, la llamamos con `extraTreat(5)` para imprimir su resultado antes de devolver `treat`.

---

💡 **Ejemplo de llamada:**

```kotlin
fun main() {
    val customTreat = { cantidad: Int -> "¡Bonus de $cantidad caramelos!" }

    val treatFunction = trickOrTreat(false, customTreat)
    treatFunction()
}
```

Salida esperada:

```
¡Bonus de 5 caramelos!
Aquí tienes 8 golosinas!
```
---

### ⚙️ Hacer que el parámetro sea opcional (opcional real)

Si queremos que `extraTreat` sea **realmente opcional**, podemos darle un **valor por defecto**.
Por ejemplo, si el usuario no pasa ninguna función, se usará una que no haga nada:

```kotlin
fun trickOrTreat(
    esTruco: Boolean,
    extraTreat: (Int) -> String = { "" }
): () -> Unit {
    if (esTruco) {
        return trick
    } else {
        println(extraTreat(5))
        return treat
    }
}
```

De este modo, `extraTreat` es **opcional**:
si se pasa, se ejecuta y muestra el mensaje extra;
si no se pasa, no añade nada adicional.

---

### 🚀 En resumen

* Puedes **pasar funciones como parámetros** a otras funciones.
* El tipo del parámetro funcional se escribe igual que cualquier tipo de función, por ejemplo: `(Int) -> String`.
* Los parámetros de función también pueden tener **valores por defecto**, igual que los parámetros normales.
* Esto permite crear funciones muy flexibles y personalizables.

---

## ✨ Sintaxis más concisa con `it`

Hasta ahora, cuando escribíamos una lambda con un parámetro, teníamos que **nombrarlo explícitamente**, por ejemplo `cantidad`.  
Pero Kotlin nos ofrece una forma más corta de hacerlo usando la palabra clave **`it`**, que representa **el único parámetro** de una lambda.

---

### 💰 Primera versión: con nombre de parámetro

Vamos a crear una función lambda dentro de `main` que devuelva un mensaje personalizado con la cantidad de monedas recibidas como argumento:

```kotlin
fun main() {
    val coins: (Int) -> String = { cantidad ->
        "Aquí tienes $cantidad monedas."
    }
}
````

🔹 Observa que **no usamos la palabra clave `return`**:
una lambda **devuelve automáticamente el último valor evaluado**.

Esta función toma un parámetro `Int` (`cantidad`) y devuelve un `String`.

---

### ⚡ Versión simplificada con `it`

Cuando una lambda tiene **un solo parámetro**, puedes omitir su nombre y usar directamente `it`:

```kotlin
fun main() {
    val coins: (Int) -> String = {
        "Aquí tienes $it monedas."
    }
}
```

Esto hace el código más breve y legible, manteniendo el mismo comportamiento.

📘 `it` es una palabra clave especial que representa **el único argumento** que recibe la lambda.

---

### 🧁 Otra lambda de ejemplo

Vamos a crear otra lambda llamada `cupcake`, que recibe un número (aunque no lo use) y devuelve siempre el mensaje:

```kotlin
val cupcake: (Int) -> String = {
    "¡Aquí tienes una magdalena!"
}
```

---

### 🎭 Usando las lambdas con `trickOrTreat`

Podemos pasar directamente estas lambdas como argumentos al llamar a `trickOrTreat`.
Recuerda que la función devuelve otra función, por lo que añadimos `()` al final para ejecutarla:

```kotlin
val treatFunction = trickOrTreat(false, coins)()
val trickFunction = trickOrTreat(true, cupcake)()
```

---

### 🧩 Resultado esperado

Al ejecutar el código, veremos en consola:

```
Aquí tienes 5 monedas.
Aquí tienes 8 golosinas!
¡No hay golosinas!
¡Aquí tienes una magdalena!
```

Cada llamada a `trickOrTreat` usa una lambda distinta para personalizar el mensaje del “trato” 🍬 o del “truco” 🎃.

---

### 🚀 En resumen

* Si una lambda tiene **un único parámetro**, puedes usar `it` sin nombrarlo.
* No hace falta escribir `return` dentro de una lambda: **devuelve el último valor**.
* Las lambdas pueden pasarse **directamente como argumentos** a otras funciones, lo que permite construir comportamientos dinámicos y expresivos.

### ❓ ¿Se puede hacer que el parámetro sea opcional?

Sí 😄. En Kotlin, puedes hacer que un parámetro sea **opcional** añadiéndole un **valor por defecto**.  
Esto también funciona con parámetros que son **funciones**, pero en ese caso hay que tener cuidado con los valores nulos (`null`).

---

### 💡 Haciendo que `extraTreat` sea opcional

Supongamos que queremos que el parámetro `extraTreat` de nuestra función `trickOrTreat` sea opcional.  
Podemos lograrlo de dos maneras:

1. Dándole una **lambda por defecto** (por ejemplo, una que no haga nada).  
2. O permitiendo que su valor sea **`null`**, usando el tipo **nullable** `((Int) -> String)?`.

Aquí aplicaremos la segunda opción, para que `extraTreat` pueda omitirse o pasarse como `null`:

```kotlin
fun trickOrTreat(
    esTruco: Boolean,
    extraTreat: ((Int) -> String)? = null
): () -> Unit {
    if (esTruco) {
        return trick
    } else {
        // ⚠️ extraTreat puede ser null, así que debemos comprobarlo
        if (extraTreat != null) {
            println(extraTreat(5))
        }
        return treat
    }
}
````

---

### 🧪 Probando el comportamiento en `main()`

Podemos probar ambos casos:
uno pasando una función como `extraTreat`, y otro dejando el parámetro vacío (usando `null`).

```kotlin
fun main() {
    val treatFunction = trickOrTreat(false, coins)
    val trickFunction = trickOrTreat(true, null)

    treatFunction()
    trickFunction()
}
```

---

### 🧩 Resultado esperado

```
Aquí tienes 5 monedas.
Aquí tienes 8 golosinas!
¡No hay golosinas!
```

En el primer caso, se usa la función `coins` como *extra*,
y en el segundo caso, como `extraTreat` es `null`, simplemente se omite esa parte.

---

### 🚀 En resumen

* Puedes hacer que un parámetro sea **opcional** asignándole un **valor por defecto**.
* Si el tipo es **nullable**, recuerda comprobar que no sea `null` antes de usarlo.
* Esto te permite hacer tus funciones más **flexibles** sin perder seguridad en tiempo de compilación.

### 🎯 ¿Podemos hacerlo aún más preciso?

Sí. Podemos hacer que la declaración de la función `coins` sea más **precisa y concisa** usando la palabra clave `it`.  
Esto simplifica el código cuando la lambda tiene un solo parámetro.

```kotlin
val coins: (Int) -> String = {
    "Aquí tienes $it monedas."
}
````

En este caso, `it` representa el único parámetro entero que recibe la función, y el resultado sigue siendo el mismo.

---

### ⚡ Pasando la función directamente como argumento

Si solo vamos a usar la función `coins` **una vez**, no es necesario declararla como variable.
Podemos pasar la lambda **directamente como argumento** a la función `trickOrTreat`:

```kotlin
val treatFunction = trickOrTreat(false, { "Aquí tienes $it monedas." })
```

Esto funciona exactamente igual que antes, solo que sin crear una variable intermedia.
Pruébalo dentro de tu `main()` y verás el mismo resultado en consola.

---

### ✨ Usando la sintaxis de lambda final (Trailing Lambda Syntax)

Kotlin ofrece una forma todavía más limpia y legible cuando la **lambda es el último parámetro** de una función.
En ese caso, puedes **sacarla fuera de los paréntesis**.
A esto se le llama ***trailing lambda syntax***.

Por ejemplo, el código anterior se puede escribir así:

```kotlin
val treatFunction = trickOrTreat(false) { 
    "Aquí tienes $it monedas." 
}
```

Esta forma es más natural cuando la lambda es corta o cuando quieres que se lea como un bloque de código.

---

### 🧠 En resumen

* Puedes usar `it` para hacer tus lambdas **más concisas** cuando tienen un solo parámetro.
* Si la lambda **solo se usa una vez**, puedes pasarla directamente como argumento sin declararla antes.
* Si la lambda es el **último argumento**, puedes usar la **sintaxis de lambda final** y colocarla **fuera de los paréntesis**, haciendo el código más limpio y expresivo.



## 🔄 Repitiendo acciones con la función `repeat()`

Kotlin incluye varias **funciones de orden superior** integradas en su biblioteca estándar.  
Una de las más útiles es `repeat()`, que te permite **ejecutar un bloque de código varias veces** de forma sencilla.

---

### 🎯 ¿Cómo funciona?

La función `repeat()` recibe dos argumentos:

1. Un número entero que indica **cuántas veces** se repetirá la acción.  
2. Una **lambda** con el código que se ejecutará en cada repetición.

Por ejemplo, si queremos ejecutar `treatFunction()` cuatro veces seguidas, podemos hacerlo así:

```kotlin
repeat(4) {
    treatFunction()
}
````

Cada vez que `repeat()` se ejecuta, llama a la lambda que contiene `treatFunction()`.
Así que el mensaje del “trato” se mostrará cuatro veces.

---

### 🧩 Resultado esperado

```
Aquí tienes 5 monedas.
Aquí tienes 8 golosinas!
Aquí tienes 5 monedas.
Aquí tienes 8 golosinas!
Aquí tienes 5 monedas.
Aquí tienes 8 golosinas!
Aquí tienes 5 monedas.
Aquí tienes 8 golosinas!
```

---

### 💡 En resumen

* `repeat(n) { ... }` ejecuta el bloque de código dentro de las llaves **n veces**.
* Es una **función de orden superior** porque recibe una lambda como argumento.
* Es perfecta para repetir acciones de forma compacta y legible.


## 🏁 Conclusión

¡Enhorabuena! 🎉  
Has aprendido los conceptos fundamentales sobre **tipos de funciones** y **expresiones lambda** en Kotlin.  
Dominar estas ideas te permitirá escribir código más limpio, flexible y expresivo, además de comprender mejor muchas de las funciones avanzadas que ofrece el lenguaje.

El uso de **funciones de orden superior**, **lambdas** y **sintaxis abreviada** te ayudará a desarrollar programas más concisos, reutilizables y fáciles de mantener.

---

### 🧠 Resumen de lo aprendido

- 🔹 Las **funciones** en Kotlin son **ciudadanos de primera clase**: puedes almacenarlas en variables, pasarlas como argumentos y devolverlas desde otras funciones.  
- 🔹 Las **expresiones lambda** ofrecen una **forma abreviada** de declarar funciones sin nombre.  
- 🔹 Puedes **pasar funciones** como parámetros a otras funciones.  
- 🔹 También puedes **devolver funciones** desde una función.  
- 🔹 Una lambda **devuelve automáticamente el valor** de su última expresión.  
- 🔹 Si la lambda tiene un único parámetro, puedes usar el identificador **`it`** en lugar de nombrarlo.  
- 🔹 Las lambdas pueden escribirse **directamente dentro de una llamada de función**, sin necesidad de una variable intermedia.  
- 🔹 Cuando una lambda es el **último parámetro**, puedes usar la **sintaxis de lambda final (trailing lambda)** y colocarla **fuera de los paréntesis**.  
- 🔹 Las **funciones de orden superior** son aquellas que **reciben o devuelven** otras funciones.  
- 🔹 La función estándar **`repeat()`** es un ejemplo de función de orden superior que ejecuta un bloque de código varias veces.

---

### 🚀 Próximos pasos

Ahora que entiendes cómo funcionan las funciones y lambdas en Kotlin, estás preparado para:
- Usar **callbacks** y **event handlers** en tus aplicaciones Android.  
- Trabajar con funciones como `map`, `filter`, `sumOf`, `flatMap` o `forEach`, que también usan lambdas.  
- Crear código más **expresivo y elegante** en tus futuros proyectos con Jetpack Compose.

¡Excelente trabajo! 👏


---

# 🏆 4. Retos: ¡Pon a prueba tus conocimientos!

Ahora que conoces las funciones lambda en Kotlin, es el momento de poner en práctica lo aprendido.

Los siguientes retos tienen una dificultad progresiva. Intenta resolverlos por tu cuenta antes de consultar la solución.

---

## 🟢 Reto 1. Una calculadora con lambdas 🧮

**Objetivo:** Practicar la declaración de lambdas, los tipos de función y los parámetros.

Crea una calculadora sencilla utilizando **funciones lambda** en lugar de funciones tradicionales.

### 📋 Requisitos

1. Declara una lambda llamada `sumar` que reciba dos números enteros y devuelva su suma.
2. Declara otra lambda llamada `multiplicar` que reciba dos números enteros y devuelva su producto.
3. Ambas deben tener explícitamente el tipo `(Int, Int) -> Int`.
4. Desde `main()`, ejecuta ambas lambdas con los valores 5 y 3 y muestra los resultados.

### 🖥️ Salida esperada

```text
5 + 3 = 8
5 * 3 = 15
```

**💡 Pista:** Recuerda que una lambda devuelve automáticamente el valor de su última expresión.

<details>
  <summary>Pulsa aquí para ver la solución</summary>

```kotlin
fun main() {
    val sumar: (Int, Int) -> Int = { a, b ->
        a + b
    }

    val multiplicar: (Int, Int) -> Int = { a, b ->
        a * b
    }

    println("5 + 3 = ${sumar(5, 3)}")
    println("5 * 3 = ${multiplicar(5, 3)}")
}
```

</details>
<br>

---

## 🟡 Reto 2. Personalizando mensajes 📢

**Objetivo:** Practicar las funciones de orden superior, el uso de `it` y la sintaxis *trailing lambda*.

Queremos crear una función que permita mostrar mensajes personalizados en la consola.

### 📋 Requisitos

1. Crea una función llamada `mostrarMensaje` que reciba:
   - Un parámetro `nombre` de tipo `String`.
   - Un parámetro `formato` de tipo `(String) -> String`.
2. La función debe ejecutar la lambda recibida y mostrar el resultado en pantalla.
3. Desde `main()`, llama tres veces a `mostrarMensaje`:
   - La primera debe mostrar un saludo.
   - La segunda debe mostrar el nombre en mayúsculas.
   - La tercera debe mostrar un mensaje de despedida.
4. Utiliza `it` y la sintaxis *trailing lambda* en las llamadas.

### 🖥️ Salida esperada

```text
Hola, Ana!
ANA
Adiós, Ana. Hasta pronto!
```

**💡 Pista:** Puedes utilizar `it.uppercase()` para convertir un texto a mayúsculas.

<details>
  <summary>Pulsa aquí para ver la solución</summary>

```kotlin
fun mostrarMensaje(
    nombre: String,
    formato: (String) -> String
) {
    println(formato(nombre))
}

fun main() {
    mostrarMensaje("Ana") {
        "Hola, $it!"
    }

    mostrarMensaje("Ana") {
        it.uppercase()
    }

    mostrarMensaje("Ana") {
        "Adiós, $it. Hasta pronto!"
    }
}
```

</details>
<br>

---

## 🟠 Reto 3. Una fábrica de descuentos 🛒

**Objetivo:** Practicar las funciones de orden superior que devuelven otras funciones.

Una tienda quiere aplicar diferentes descuentos a sus productos. En lugar de crear una función para cada descuento, vamos a crear una función que **genere otras funciones**.

### 📋 Requisitos

1. Crea una función llamada `crearDescuento` que reciba un porcentaje de descuento de tipo `Int`.
2. Esta función debe devolver una lambda de tipo `(Double) -> Double`.
3. La lambda devuelta debe recibir el precio original de un producto y calcular su precio después de aplicar el descuento.
4. Desde `main()`, crea dos funciones de descuento:
   - Una que aplique un 10%.
   - Otra que aplique un 25%.
5. Aplica ambos descuentos a un producto de 100 €.

### 🖥️ Salida esperada

```text
Precio original: 100.0 €
Con descuento del 10%: 90.0 €
Con descuento del 25%: 75.0 €
```

**💡 Pista:** La función `crearDescuento()` no debe devolver directamente un precio, sino una función que pueda calcularlo posteriormente.

<details>
  <summary>Pulsa aquí para ver la solución</summary>

```kotlin
fun crearDescuento(porcentaje: Int): (Double) -> Double {
    return { precio ->
        precio - precio * porcentaje / 100.0
    }
}

fun main() {
    val descuento10 = crearDescuento(10)
    val descuento25 = crearDescuento(25)

    val precio = 100.0

    println("Precio original: $precio €")
    println("Con descuento del 10%: ${descuento10(precio)} €")
    println("Con descuento del 25%: ${descuento25(precio)} €")
}
```

**🧠 Reflexiona:** ¿Por qué podemos seguir utilizando el valor de `porcentaje` dentro de la lambda después de que `crearDescuento()` haya terminado?

La lambda conserva el acceso al valor de `porcentaje` con el que fue creada. Este comportamiento se conoce como **captura de variables** (*closure*).

</details>
<br>

---

## 🔴 Reto 4. Un sistema de notificaciones 🔔

**Objetivo:** Combinar funciones de orden superior, lambdas opcionales, tipos anulables, `repeat()` y sintaxis *trailing lambda*.

Vamos a desarrollar un pequeño sistema que permita enviar notificaciones personalizadas a un usuario.

### 📋 Requisitos

Crea una función llamada `enviarNotificaciones` que reciba los siguientes parámetros:

1. `veces: Int`: número de veces que se enviará la notificación.
2. `mensaje: String`: texto de la notificación.
3. `prefijo: ((String) -> String)? = null`: lambda opcional que permita modificar el mensaje antes de mostrarlo.
4. `accion: (String) -> Unit`: lambda que indique qué hacer con el mensaje.

La función debe cumplir las siguientes condiciones:

- Utilizar `repeat()` para ejecutar la acción tantas veces como indique `veces`.
- Si se proporciona la lambda `prefijo`, debe utilizarse para modificar el mensaje.
- Si `prefijo` es `null`, debe utilizarse el mensaje original.
- La lambda `accion` debe recibir el mensaje final.
- La lambda `accion` debe ser el último parámetro para poder utilizar la sintaxis *trailing lambda*.

### 🧪 Prueba tu función

Desde `main()`, realiza dos llamadas:

**Primera llamada:** Envía tres veces el mensaje `"Tienes un mensaje nuevo"`, sin utilizar ningún prefijo.

**Segunda llamada:** Envía dos veces el mensaje `"Batería baja"`, utilizando una lambda que añada el prefijo `"URGENTE: "`.

### 🖥️ Salida esperada

```text
Tienes un mensaje nuevo
Tienes un mensaje nuevo
Tienes un mensaje nuevo
URGENTE: Batería baja
URGENTE: Batería baja
```

**💡 Pista:** Comprueba si `prefijo` es distinto de `null` antes de ejecutarlo. Recuerda que `repeat()` también recibe una lambda.

<details>
  <summary>Pulsa aquí para ver la solución</summary>

```kotlin
fun enviarNotificaciones(
    veces: Int,
    mensaje: String,
    prefijo: ((String) -> String)? = null,
    accion: (String) -> Unit
) {
    repeat(veces) {
        val mensajeFinal = if (prefijo != null) {
            prefijo(mensaje)
        } else {
            mensaje
        }

        accion(mensajeFinal)
    }
}

fun main() {
    // Notificación sin prefijo
    enviarNotificaciones(3, "Tienes un mensaje nuevo") {
        println(it)
    }

    // Notificación con prefijo personalizado
    enviarNotificaciones(
        2,
        "Batería baja",
        { "URGENTE: $it" }
    ) {
        println(it)
    }
}
```

### 🧠 Preguntas para reflexionar

1. ¿Por qué `prefijo` tiene un signo de interrogación `?` en su tipo?
2. ¿Por qué `accion` devuelve `Unit`?
3. ¿Qué ocurriría si llamamos a `enviarNotificaciones` con `veces = 0`?
4. ¿Por qué podemos escribir la lambda `accion` fuera de los paréntesis?

</details>
<br>

---

## 🎉 ¡Retos completados!

Si has conseguido resolver los cuatro retos, ya sabes:

- Definir y ejecutar expresiones lambda con distintos tipos de parámetros y valores de retorno.
- Utilizar `it` para simplificar lambdas con un único parámetro.
- Pasar lambdas como argumentos a funciones de orden superior.
- Crear funciones que devuelven otras funciones.
- Utilizar parámetros funcionales opcionales y anulables.
- Aplicar la sintaxis *trailing lambda*.
- Repetir acciones mediante `repeat()`.

**🚀 ¡Ya estás preparado para utilizar funciones lambda en aplicaciones Kotlin más complejas!**


