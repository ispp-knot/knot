---
sidebar_position: 6
---

# ✅ Testing

El testing es una parte clave del desarrollo del proyecto. Es muy importante mantener una cobertura de codigo alta.

En esta sección nos centraremos en el testing del backend.

## Convenciones de nombres para tests

Usaremos la convención `shouldExpectedBehavior_WhenState`, nos permite tener nombres de tests muy descriptivos, faciles de leer y estandarizados.

```java
@Test
void shouldReturnZero_whenListIsEmpty() { ... }
```

## Buenas prácticas para testear en Spring

* Aislar lo más posible los tests unitarios.
* Usar mocks para dependencias externas (`Mockito`).
* Separar unitarios de integración y E2E → permite ejecutar rápido los unitarios en builds locales.
* Repetibilidad: cada test debe ser independiente y reproducible.
* Cobertura de casos importantes, no solo happy path → errores, límites, datos nulos.
