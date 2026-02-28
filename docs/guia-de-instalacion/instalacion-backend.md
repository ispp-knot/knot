---
sidebar_position: 1
---

# Instalación del backend

## Requisitos previos

- Java 25
- Docker compose (Opcional)
- Extension Pack for Java (VSCode)

## Clonar el repositorio

Lo primero es clonar el repositorio con `git clone`.

## Primeros pasos

### 0. Perfiles

Antes que nada hay que entender que el backend actualmente dispone de 4 perfiles diferentes:

#### 1. Desarrollo (dev)

Este perfil es con el que se va a trabajar habitualmente mientras desarrollamos. Provee acceso a la API que genera Swagger y por defecto manteniene una base de datos PostgreSQL en el contenedor _postgres_ que se actualiza sin necesidad de estar migrando.

Este perfil es el lanzado por defecto.

#### 2. Test (test)

Este perfil se usa a la hora de lanzar los tests, dispone de su propia base de datos en el contenedor _postgres-test_.

#### 3. Producción (prod)

Perfil que posee la configuración que se usará cuando esté desplegado en Azure, por defecto usará la base de datos que le sea configurada en el entorno, en este caso será una alojada en Neon, para esta base de datos si hay que utilizar migraciones en caso de cambios.

#### 4. Migraciones (dev-migration)

Perfil diseñado para probar las migraciones en la base de datos _postgres-devmigrations_, especificamente levantada para este propósito.

> [Más información](../guia-de-desarrollo/migraciones.md)

### 1. Configurar el entorno

Para el backend actualmente hay 2 configuraciones de entorno posibles:

1. Neon

Para ello es necesario copiar la plantilla `.env.example`:

```bash
cp .env.example .env
```

Una vez copiada, rellenar con los campos que proporciona Neon.

2. Docker

Los contenedores de docker ya vienen configurados en los diferentes perfiles por lo que no es necesaria configuración adicional. Simplemente habría que lanzar el siguiente comando para levantar las bases de datos:

```bash
docker compose up -d
```

Cuando se termina de usar ejecutar:

```bash
docker compose stop
```

> Si queremos levantar/parar solo uno de ellos añadir al final el nombre del contenedor.

### 2. Ejecutar el backend

Ejecutar el backend es tan simple como que, una vez se ha completado el build automático, ejecutar:

```bash
mvnw spring-boot:run
```

> Recordar en Windows usar .\mvnw

Por defecto se utiliza el perfil **dev**, en caso de querer ejecutar un perfil diferente perfil se debe añadir la opcion `-Dspring-boot.run.profiles=[perfil deseado]` sustituyende **[perfil deseado]** por alguno de los perfiles disponibles.

> Revisar los archivos application-[profile].yml

### 3. Ejecutar los tests

Ejecute:

```bash
mvnw test
```

> Recordar tener levantada la base de datos de pruebas

### 4. Hooks

Para mantener consistencia en el desarrollo y hacer que lo que llegue a Github siga buenas prácticas se hará uso de ciertos hooks pre-commit. A continuación, una lista con aquellos disponibles en la carpeta `.github/githooks/`:

- **pre-commit.sample**: Para detectar errores de linting. Configuración:

```bash
cp ./.github/githooks/pre-commit.sample ./.git/hooks/pre-commit
```

- **commit-msg.sample**: Para detectar no seguimientos de Conventional Commits. Configuración:

```bash
cp ./.github/githooks/commit-msg.sample ./.git/hooks/commit-msg
```

Por último, antes de comenzar, se aconseja encarecidamente consultar la sección de [linting automático para el backend](../guia-de-desarrollo/lint.md#linting-del-backend).
