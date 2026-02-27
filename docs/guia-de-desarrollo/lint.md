---
sidebar_position: 4
---

# 🤖 Linting Automatico

## Linting del Backend

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

## Linting del Frontend

Al igual que para el backend, es muy recomendable tener instaladas las extensiones de **ESLint** y **Prettier** para VSCode.

Para mantener una base de código consistente, contamos con ESLint, Prettier y lint-staged. Estas herramientas detectan errores de estilo y corrigen automáticamente los más sencillos. Los que requieren intervención manual se muestran por consola.

Para ejecutarlo manualmente:

```bash
npm run lint-staged
```

Tras completarse, las correcciones automáticas ya estarán aplicadas y los problemas que requieran correciones manuales serán listados en consola.

Por último, se dispone de un pre-commit hook en `.husky/`. Cada vez que se haga commit se ejecutará `npm run lint-staged` automáticamente. Si se detectan problemas:

- Las correcciones automáticas se aplicarán pero el commit quedará **bloqueado**.
- Revisa los cambios, haz `git add` y vuelve a hacer `git commit`.

Para activar los hooks de husky ejecute `npm run prepare-husky`.
