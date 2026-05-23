# Plantilla para Preguntas 3 y 4 - Examen de Arquitectura de Software

## Estructura general

Esta plantilla es reutilizable para cualquier caso que requiera modelar un Bounded Context con DDD (Pregunta 3) y aplicar CQRS (Pregunta 4).

---

## Pregunta 3 - Diagrama de Clases del Dominio

### Paso 1: Selección del Bounded Context

Elegir un Bounded Context que cumpla:
- Tener un Aggregate Root claro
- Tener al menos un Value Object
- Tener al menos una regla de negocio identificable
- Preferir BC de soporte (supporting domain) sobre core domain para simplificar

### Paso 2: Descripción del Bounded Context

**Nombre del BC:** [Nombre elegido]

**Propósito:** [Una oración que describa qué hace este BC]

**Responsabilidades:**
- [Responsabilidad 1]
- [Responsabilidad 2]
- [Responsabilidad 3]

**Ubiquitous Language (términos clave):**
| Término | Definición |
|---------|------------|
| [Término 1] | [Definición] |
| [Término 2] | [Definición] |
| [Término 3] | [Definición] |

### Paso 3: Código PlantUML del diagrama de clases

```plantuml
@startuml

class [NombreAggregateRoot] {
    - id: String
    - [atributo1]: [Tipo]
    - [atributo2]: [TipoValorObject]
    - [atributoBooleano]: Boolean
    --
    + create[Nombre]([parametros]): [NombreAggregateRoot]
    + [metodoNegocio1]([parametros]): void
    + [metodoNegocio2](): void
    + [metodoNegocio3](): void
}

class [NombreValueObject] {
    - value: [Tipo]
    --
    + [NombreValueObject](value: [Tipo])
    + isValid(): Boolean
    + getValue(): [Tipo]
}

enum [NombreEnum] {
    [VALOR1]
    [VALOR2]
    [VALOR3]
}

[NombreAggregateRoot] "1" *-- "1" [NombreValueObject] : contains >
[NombreAggregateRoot] --> [NombreEnum] : has >

@enduml
```
Paso 5: Párrafo de sustentación (plantilla)
He seleccionado el bounded context [Nombre del BC] dentro de la arquitectura propuesta. La elección de este BC responde a que, si bien no es el núcleo transaccional más complejo de la plataforma, representa de manera clara los conceptos fundamentales de Domain-Driven Design solicitados: un Aggregate Root bien definido, Value Objects inmutables, y reglas de negocio encapsuladas. Al ser un BC de soporte con menor riesgo de error en su modelado, permite enfocarse en la correcta aplicación de los principios DDD sin complejidad añadida, lo cual es estratégico en un contexto de examen con tiempo limitado.

En el diagrama de clases del dominio, he considerado [NombreAggregateRoot] como el Aggregate Root. Esta clase tiene una relación de composición con [NombreValueObject] , implementado como un Value Object por ser inmutable y carecer de identidad propia fuera del Aggregate Root. La clase [NombreValueObject] encapsula la lógica de validación a través de su método isValid(). Además, [NombreAggregateRoot] se relaciona con la enumeración [NombreEnum] , la cual representa valores fijos y conocidos en tiempo de diseño. El Aggregate Root expone métodos de negocio representativos como create[Nombre](), [metodoNegocio1]() y [metodoNegocio2](), los cuales reflejan operaciones del mundo real dentro del dominio. Se ha aplicado la convención de nomenclatura en inglés con Upper-Camel-Case para clases y Lower-Camel-Case para atributos y métodos.


Pregunta 4 - CQRS (Command y CommandHandler)
Paso 1: Selección del comando
Elegir un comando que:

Represente una operación de escritura significativa

Tenga al menos una regla de negocio asociada

Requiera interacción con un repositorio

Comando seleccionado: [NombreCommand]

Reglas de negocio asociadas:

[Regla 1]

[Regla 2]

Paso 2: Código PlantUML del diagrama CQRS

```plantuml
@startuml

class [NombreCommand] {
    - [parametro1]: String
    - [parametro2]: String
    --
    + [NombreCommand]([parametro1]: String, [parametro2]: String)
    + get[Parametro1](): String
    + get[Parametro2](): String
}

class [NombreCommandHandler] {
    - repository: [NombreRepository]
    --
    + [NombreCommandHandler](repository: [NombreRepository])
    + handle(command: [NombreCommand]): void
}

interface [NombreRepository] {
    + findById(id: String): [NombreAggregateRoot]
    + save(aggregate: [NombreAggregateRoot]): void
}

class [NombreAggregateRoot] {
    - id: String
    - [atributo]: String
    - isActive: Boolean
    --
    + [metodoNegocio]([parametro]: String): void
}

[NombreCommandHandler] --> [NombreRepository] : uses
[NombreCommandHandler] --> [NombreCommand] : handles
[NombreRepository] --> [NombreAggregateRoot] : manages

@enduml
```

Paso 5: Párrafo de sustentación (plantilla)
Para la aplicación de CQRS dentro del mismo bounded context [Nombre del BC] , he seleccionado el comando [NombreCommand] por ser significativo desde el punto de vista del negocio y por ilustrar la separación entre operaciones de escritura (command) y consultas (query). Este comando involucra reglas de negocio no triviales: [enumerar reglas de negocio brevemente]. Se ha elegido este comando para mostrar la riqueza de reglas de negocio que incluso un BC de soporte puede contener.

El diagrama presenta [NombreCommand] como un comando inmutable, con un constructor que recibe los parámetros [parametro1] y [parametro2], y únicamente expone métodos getter. Su correspondiente [NombreCommandHandler] recibe por inyección de dependencias un [NombreRepository] . El método handle() ejecuta la siguiente secuencia: (1) recupera el Aggregate Root usando el repositorio, (2) ejecuta el método de dominio [metodoNegocio]() que contiene las reglas de negocio, y (3) persiste el cambio. Esta separación es fundamental en CQRS: el comando transporta datos, el handler orquesta la ejecución, y el Aggregate Root aplica las reglas de negocio. El repositorio se modela como una interfaz para mantener el desacoplamiento. Se ha respetado la nomenclatura en inglés con sufijos Command y Handler, y Lower-Camel-Case para métodos.
