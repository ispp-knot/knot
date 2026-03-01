---
sidebar_position: 5
---

# 🕊️ Migraciones

**Todos somos responsables de las migraciones que creamos**. Una migración mal hecha puede **borrar datos permanentemente**. Las migraciones **serán revisadas en los PRs** y **pueden generar conflictos** que **han de ser resueltos manualmente**. Esto es parte de la responsabilidad de desarrollar en el backend.

## Cambios en el modelo

Antes de crear cualquier migración, se recomienda implementar los cambios necesarios en el modelo **usando el perfil `dev`**, corriendo la aplicación con:

`./mvnw spring-boot:run -Dspring-boot.run.profiles=dev`

o

`./mvnw spring-boot:run "-Dspring-boot.run.profiles=dev"`

Si queremos hacer un cambio en el modelo, es importante **asegurarse de que los cambios realizados son los definitivos antes de crear las migraciones** correspondientes. Por el contrario, perderemos tiempo modificando el modelo y las migraciones hasta encontrar el modelo definitivo.

Puede ser interesante seguir la siguiente estructura a la hora de realizar estos cambios, sobre todo en cambios grandes:

1. Hacer los cambios necesarios en el modelo
2. Crear una PR con los cambios, para obtener feedback sobre los cambios realizados y determinar si será o no la versión definitiva
3. En el caso de ser aprobados, crear las migraciones correspondientes (y esperar a su revisión antes del merge)
4. En el caso de ser denegados, modificar el modelo de nuevo

## Estructura de las migraciones

### Estructura de carpetas

Las migraciones se organizan **una carpeta por tarea**, para evitar que todo quede en un único directorio plano, conservando la **atomicidad** de cada archivo de migración. La estructura es la siguiente:

```
src/main/resources/db/changelog/
├── db.changelog-master.yaml          <- Master changelog
├── 001-base-models/
│   ├── base-models.changelog.yaml    <- Changelog de la tarea 001
│   └── 001-create-clients.yaml       <- Una migración de la tarea 001
│   └── ...
├── 002-maps/
│   ├── maps.changelog.yaml           <- Changelog de la tarea 002
│   ├── 001-create-map-entity.yaml    <- Una migración de la tarea 002
│   └── ...
```

Cada **subcarpeta** tiene **su propio archivo de changelog** (_XXX\-taskName_.changelog.yaml) que lista sus changesets, y el **master** incluye los **changelogs de todas las subcarpetas**, en orden.

A continuación se muestran ejemplos del contenido de estos changelogs:

#### Master changelog

```
# db.changelog-master.yaml
databaseChangeLog:
- include:
   file: db/changelog/001-base-models/base-models.changelog.yaml
- include:
   file: db/changelog/002-maps/maps.changelog.yaml
```

#### Task changelog

```
# 001-base-models/base-models.changelog.yaml
databaseChangeLog:
- include:
   file: db/changelog/001-base-models/001-create-clients.yaml
- include:
   file: db/changelog/001-base-models/002-create-products.yaml
```

#### Archivo de migración individual

```
# 001-base-models/001-create-clients.yaml
databaseChangeLog:
- changeSet:
   id: 001-create-clients
   author: dev1
   changes:
       - createTable:
           tableName: clients
           columns:
           - column:
               name: id
               type: INTEGER
               constraints:
                   primaryKey: true
                   nullable: false
           - column:
               name: name
               type: VARCHAR(255)
               constraints:
                   nullable: false
...
```

### Organización del contenido

Un buen uso de esta estructura de carpetas supone **dividir el contenido** de las migraciones en **grupos/secciones lógicos**.

Por ejemplo, si mi tarea implica la modificación de los modelos de productos, clientes y comercios, organizaré mis cambios de la siguiente manera:

1. Creación de la **subcarpeta** asociada a mi tarea (_XXX\-taskName_, con su _XXX\-taskName_.changelog.yaml)
2. Creación de los **archivos de migración individuales**, organizados **según la lógica de los cambios** realizados:
   1. Modificación del modelo de productos
   2. Modificación del modelo de clientes
   3. Modificación del modelo de comercios

3. Inclusión de los contenidos correspondientes en cada migración individual
4. Inclusión de las migraciones individuales en el changelog de la tarea
5. Inclusión del changelog de la tarea en el master changelog

De esta manera resulta mucho más sencillo realizar un seguimiento de los cambios realizados en todo momento.

### Orden del contenido

Siguiendo con el ejemplo de la sección anterior, hemos definido el orden de las migraciones individuales como:

`productos -> clientes -> comercios`

Esto es un factor a tener en cuenta en la creación de migraciones. Aunque se estructure en grupos/secciones lógicos, el orden escogido debe **tener en cuenta las dependencias entre tablas**.

De nuevo, con un ejemplo: Si nuestra modificación al modelo de comercios crea una nueva tabla StoreStock que depende de una tabla ProductStock creada en la migración de productos, el orden escogido sería el correcto. Sin embargo, si hubieramos escogido el siguiente orden:

`comercios -> clientes -> productos`

No sería posible acceder a la tabla ProductStock desde la migración de comercios, ya que se crearía más adelante.

## Contenido de las migraciones

### Creación de la migración "en bruto"

Una vez realizados los cambios y definida la organización y el orden de aplicación de estos, es necesario **incluir estas modificaciones** en los archivos de migración.

Antes de empezar, debemos asegurarnos de **tener aplicada la última migración** del proyecto antes de nuestros cambios. Lo haremos con el comando:

`./mvnw liquibase:update`

Esto podría lanzar un error si encuentra **migraciones aplicadas que no existen en la versión del proyecto en la que se está trabajando**. Por ejemplo, cuando he aplicado las migraciones de otro compañero en su rama, y luego he vuelto a la mía, donde no existen esas migraciones.

Para solucionar esto, eliminaremos los contenidos de la base de datos, para aplicar las migraciones de nuestra rama desde cero. Se puede hacer de dos maneras:

1. Eliminación de todas las tablas de manera manual, con programas como DBeaver o HeidiSQL.
2. Eliminación y recreación de los contenedores, incluyendo los volumenes. Con los comandos `docker compose down -v` y `docker compose up -d`.

Una vez hecho esto, se volverá a aplicar `./mvnw liquibase:update`. Si se encuentran problemas de nuevo, se encontrará en algún archivo de migración anterior y habrá que revisarlos manualmente.

A continuación, nos aseguraremos de **compilar el proyecto**, para asegurarnos de que liquibase encuentre los cambios realizados:

`./mvnw clean compile`

Y por último, hacemos que **liquibase genere el archivo** con los cambios que encuentre:

`./mvnw liquibase:diff`

### Limpieza del archivo generado

Tras ejecutar el último comando, encontraremos un nuevo archivo en `/src/main/resources/db/changelog`. Este incluirá todos los cambios que liquibase ha detectado en nuestro proyecto.

**Estos cambios pueden no ser del todo correctos, por lo que es necesario una revisión del archivo generado.**

#### Eliminación de "cambios basura"

Liquibase suele generar muchos "cambios basura", generados por equivocación o por errores en la generación de nombres (no siguen cierta convención, etc).

El más común, que encontraréis en todos los archivos generados por `liquibase:diff`, serán los cambios que intentan recrear las PKs de todas las tablas:

```
- changeSet:
    id: 1772283358365-26
    author: ignacio (generated)
    changes:
    - dropPrimaryKey:
        tableName: storefronts
- changeSet:
    id: 1772283358365-27
    author: ignacio (generated)
    changes:
    - addPrimaryKey:
        columnNames: id
        constraintName: storefrontsPK
        tableName: storefronts
```

Estos cambios pueden (y deben) ser eliminados de los archivos de migración.

#### Organización del resto de cambios

Las migraciones pueden ser autogeneradas, pero **con límites**. Por ejemplo, si se renombra una columna, la migración autogenerada **destruirá la vieja y creará una nueva** por defecto, y esto ha de ser ajustado manualmente. [Este artículo](https://bell-sw.com/blog/how-to-use-liquibase-with-spring-boot/) es un buen recurso.

**Los tipos de cada columna** son otro aspecto con el que se debe ser extremadamente cuidadoso. Si realizamos cambios en los tipos de algún atributo del modelo, debemos asegurarnos que el tipo de la columna de la DB **se corresponda con el del modelo y sea adecuado**.

Una vez limpiado el archivo generado, **se repartirán** los distintos `changeset` **entre los archivos de migración** individuales definidos previamente.

De nuevo, es importante tener en cuenta el **orden** en el que se aplican los `changeset` dentro de la misma migración individual, teniendo en cuenta las **dependencias entre modificaciones**.

Tras eliminar o repartir todos los cambios del archivo generado, este debe ser eliminado.

#### Comprobación de las migraciones creadas

Para probar la migración, se pueden usar los siguientes comandos que imprimen el SQL del upgrade y la migración. Nótese que **hay que ejecutar ambos** aún si sólo se quiere ver uno, porque imprimir el SQL actualiza el "estado" que liquibase piensa que tiene la base de datos.

```bash
./mvnw liquibase:updateSQL && cat target/liquibase/migrate.sql
./mvnw liquibase:rollbackSQL -Dliquibase.rollbackCount=1 && cat target/liquibase/migrate.sql
```

Para aplicar los cambios de manera definitiva, volveremos a aplicar `./mvnw liquibase:update`. Si no recibimos ningún error, lo más probable es que las migraciones hayan sido creadas correctamente.

Es conveniente también **acceder a las tablas** modificadas desde programas como **DBeaver o HeidiSQL**, para comprobar manualmente que los cambios se han aplicado correctamente.

Tras aplicar el update, si se quisiera **volver a un estado anterior** (por ejemplo, para realizar algún cambio a las migraciones recién creadas), es posible hacer rollback con los comandos:

`./mvnw liquibase:rollback -Dliquibase.rollbackCount=<count>`, especificando el número de archivos de migracion que se quieren deshacer

o

`./mvnw liquibase:rollback -Dliquibase.rollbackToDate=<date>`, especificando la fecha de la versión a la que se quiere volver.

## Migraciones antiguas

El objetivo de las migraciones es proporcionar una manera de gestionar todos los cambios realizados a la DB, **incluso si estos cambios se tienen que aplicar a una versión ya funcional** de la DB. Por esto mismo, es muy importante **NO MODIFICAR NINGUNA MIGRACIÓN QUE HAYA PASADO POR MAIN** y haya podido ser aplicada por otros compañeros (o aún más importante, por versiones desplegadas y funcionales de la DB).

En vez de hacer esto, se creará una **nueva migración** en la que se reviertan los cambios que se requiera. De esta forma, una versión de la DB que cuente con los cambios de migraciones antiguas podrá revertirlos sin tener que destruir la DB y recrearla de nuevo.

## Resumen de entornos de ejecución

- **dev-migration** (`localhost:5434/devmigrdb`): Usa Liquibase. Las migraciones se aplican automáticamente.
- **dev** (`localhost:5432/devdb`): Usa Hibernate auto-update. La base de datos se actualiza automáticamente sin Liquibase.
- **test** (`localhost:5433/testdb`): Usa Liquibase. Las migraciones se aplican automáticamente al ejecutar tests.
- **prod**: Usa Liquibase. Tira de variables de entorno configuradas en Azure.

**Todos los comandos de migraciones se ejecutan contra la base de datos dev-migration** (`localhost:5434/devmigrdb`).

El perfil de `dev` (`./mvnw spring-boot:run -Dspring-boot.run.profiles=dev`) no require de migraciones, lo cuál es útil para probar cambios rápidos en el desarrollo.

El perfil de `test` sí que usa las migraciones, por lo que han de ser **válidas** para que pasen. Si se quieren probar las migraciones por si mismas, se puede usar el perfil de `dev-migration`.
