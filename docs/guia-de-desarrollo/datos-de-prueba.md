---
sidebar_position: 6
---

# Datos de prueba

El perfil `seed` puebla la base de datos `dev-migration` con datos realistas para desarrollo y demos. El proceso tiene dos fases: datos manuales fijos (una tienda de ejemplo con productos y outfits concretos) y datos aleatorios configurables generados a partir de ficheros de texto.

Los datos estáticos de referencia (redes sociales, tipos/colores/tallas de productos, etiquetas de outfits) se insertan mediante la **migración Liquibase 007**, por lo que también están presentes en los entornos de test y prod.

### Qué genera el seeder

- **1 tienda manual** ("La Boutique de Sevilla") con 2 redes sociales, 4 productos con variantes y 2 outfits
- **5 tiendas aleatorias** (configurable) con redes sociales, productos con variantes y outfits
- **10 clientes** (configurable)

### Prerequisitos

La base de datos `dev-migration` debe estar levantada:

```bash
docker compose up -d
```

### Comando

```bash
./mvnw spring-boot:run -Dspring-boot.run.profiles=dev-migration,seed
```

Al arrancar, Liquibase aplica las migraciones (incluida la 007 con los datos de referencia) y después el seeder inserta los datos. La aplicación termina sola al acabar porque el perfil `seed` deshabilita el servidor web.

### Parámetros configurables

Los parámetros se pueden cambiar en `src/main/resources/application-seed.yaml` o sobreescribir con `-D`:

| Parámetro | Por defecto | Descripción |
|-----------|-------------|-------------|
| `seed.random-seed` | `42` | Semilla aleatoria (misma semilla → mismos datos) |
| `seed.store-count` | `5` | Número de tiendas aleatorias |
| `seed.products-per-store` | `6` | Productos por tienda aleatoria |
| `seed.social-networks-per-store` | `2` | Redes sociales por tienda aleatoria |
| `seed.outfits-per-store` | `2` | Outfits por tienda aleatoria |
| `seed.client-count` | `10` | Número de clientes aleatorios |

Ejemplo con parámetros distintos:

```bash
./mvnw spring-boot:run \
  -Dspring-boot.run.profiles=dev-migration,seed \
  -Dspring-boot.run.arguments="--seed.store-count=20 --seed.client-count=50"
```

### Idempotencia

El seeder comprueba al inicio si ya hay tiendas en la base de datos. Si las hay, termina sin hacer nada:

```
Database already seeded, skipping.
```

### Cómo re-sembrar

Para volver a sembrar desde cero, hay que limpiar la base de datos primero. La forma más sencilla es recrear el contenedor:

```bash
docker compose down postgres-devmigrations
docker volume rm dondesiempre-backend_pgdata-devmigr # El nombre exacto del volumen puede variar
docker compose up -d postgres-devmigrations
./mvnw spring-boot:run -Dspring-boot.run.profiles=dev-migration,seed
```

### Verificación

```bash
docker compose exec postgres-devmigrations \
  psql -U devmigruser -d devmigrdb \
  -c "SELECT COUNT(*) FROM stores; SELECT COUNT(*) FROM products; SELECT COUNT(*) FROM clients;"
```
