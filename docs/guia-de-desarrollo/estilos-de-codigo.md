---
sidebar_position: 1
---

# Guia de estilo

### Seguimiento del estilo para Java en el desarrollo del Backend

Lo primero de todo, es extremadamente recomendable contar con el _**Extension Pack for Java**_ si se usa VSCode para contar con todas las ayudas posibles a la hora del desarrollo y provee una extensión que apoya con linting automático.

Además, para asegurar que el estilo es lo mejor posible contamos con el plugin Spotless para detectar errores de linting y corregirlos automáticamente.

Para usarlo manualmente se tiene que ejecutar lo siguiente:

```bash
mvnw spotless:check
```

Una vez ejecutado, se mostrará en la terminal un diff donde las líneas con "-" indican eliminaciones y las líneas con "+" indican adiciones necesarias para cumplir el estilo.

Para arreglar los problemas de linting automáticamente ejecutar:

```bash
mvnw spotless:apply
```

> Recordar que en Windows es .\mvnw o bien .\mvnw.cmd

Tras completarse, las correcciones ya estarán aplicadas en los archivos afectados. Se recomienda revisar las modificaciones realizadas de forma automática antes de subir los cambios.

Por último, se dispone de un pre-commit.sample hook que se encuentra en la carpeta _.github/githooks/_. Para hacer uso de el hay que copiarlo en la carpeta .git/hooks/ quitando la extension .sample.

```bash
cp ./.github/githooks/pre-commit.sample ./.git/hooks/pre-commit
```

> En caso de error en linux usar `chmod +x .git/hooks/pre-commit`

Ahora, cada vez que se haga commit y algún archivo no siga los estilos correctos le dará un error y no permitirá completar el commit hasta que se soluciones los problemas de estilo.
