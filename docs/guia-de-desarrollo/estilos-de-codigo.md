---
sidebar_position: 1
---
# 🎨 Guía de estilo

## Qué es esta guía

Esta guía **NO** es una guía de estilo de sintaxis, aunque se aborda la sintaxis se espera que el equipo haga uso de herramientas de linting automaticas. Esto nos permite asegurarnos que todos estamos usando las mismas convenciones. El uso de las herramientas de linting se aborda en [Linting Automático](./lint.md).

Esta guía **NO** es una guía de arquitectura del proyecto para eso existe [Arquitectura de Spring](./arquitectura-spring.md)

Esta guía **SI** es una guía de conceptos y buenas practicas a seguir a la hora de desarrollar el código del proyecto. Incumplir cualquiera de las siguientes instrucciones es motivo de rechazar una PR.

Para facilitar el flujo de trabajo y no tener que estar discutiendo las PR sobre este tipo de cuestiones se pide que se lea este documento con detenimiento, se pide que se tenga en cuenta lo leido a la hora de desarrollar y se pide que si algo no queda claro o se tiene cualquier duda se pregunte.

## Principios generales de código limpio

### Claridad sobre brevedad

- Se prefiere código **fácil de entender** antes que conciso.
- Un nombre explícito vale más que un comentario largo.

### Ser explícito

- Evita la ambigüedad en la intención: si algo puede entenderse de dos formas, déjalo claro.
- Ejemplo: en lugar de `var x = getData()`, usa `var userData = fetchUserData()`.

### Evitar magia y efectos ocultos

- Funciones y métodos deben tener efectos claros y predecibles.
- Evita que un método haga demasiadas cosas o modifique estados inesperadamente.
- El código debe ser honesto.

## Tipado

### Uso de tipos

- En TypeScript, declara tipos explícitos siempre que sea posible (`const foo: Foo`).
- En Java usa tipos DTO para input/output.
- Tipos claros **documentan tu intención**, reducen errores y facilitan refactorización.

### Nulos y opcionales

- Maneja nulos de forma explícita (`Optional<T>` en Java, `T | undefined` en TS).

## Nombres: variables, funciones y clases

### Variables y constantes

- Nombres descriptivos: `totalPrice`, `userEmail` > `tp`, `ue`.
- Constantes inmutables con mayúsculas y guiones bajos (`MAX_RETRIES`).

### Funciones

- Usar **verbos** para funciones que realizan acciones: `calculateDiscount()`, `sendEmail()`.
- Funciones deben **hacer una sola cosa**.
- Evita abreviaciones.

### Clases y interfaces

- Sustantivos claros: `UserService`, `OrderRepository`.
- Interfaces describiendo contratos: `PaymentProcessor`, `Logger`.

## Organización del código

### División en funciones y métodos

- Cada función debe responder a **una pregunta**: “¿Qué hace esto?”.
- Mantén funciones **cortas y legibles**.
- Evita funciones que dependan de muchos estados externos.

### Cohesión

- Agrupa métodos relacionados en clases o módulos.
- Evita clases/módulos “comodín” que contengan todo tipo de funciones.

## Herencia y composición

- Prefiere **composición sobre herencia**: crea objetos con responsabilidades claras.

## Comentarios y documentación

- **Casi siempre deben evitarse**. El código debe ser **autoexplicativo**.
- Comenta solo cuando la intención **no pueda deducirse del código mismo** (ej. limitaciones técnicas, referencias externas, decisiones de diseño complejas).
