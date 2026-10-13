# Tutorial 04 - Dockerizacion y Despliegue en AWS

Arquitectura de Software 2026 - EAFIT
Jeronimo Carvajal Zuluaga

Continuacion del Tutorial 03. La aplicacion "Tienda API" se empaqueta en contenedores Docker (Django + PostgreSQL) y se despliega en una instancia EC2 de AWS.

## Que se agrego

| Archivo | Que hace |
|---|---|
| `Tienda/settings.py` | La base de datos ahora es PostgreSQL y las credenciales se leen de variables de entorno (no estan quemadas en el codigo) |
| `requirements.txt` | Se agrega `psycopg2-binary` (driver de PostgreSQL) |
| `Dockerfile` | Receta de la imagen de Django (Python 3.11 slim) |
| `docker-compose.yml` | Levanta dos contenedores: `db` (PostgreSQL 15) y `web` (Django). `web` corre las migraciones al arrancar |
| `.dockerignore` | Evita copiar el venv, la base sqlite y los logs dentro de la imagen |

Cambios adicionales a lo del PDF:
- `ALLOWED_HOSTS = ['*']` para que Django acepte peticiones con la IP publica de EC2.
- `healthcheck` en el servicio `db` y `depends_on: condition: service_healthy`, para que Django espere a que PostgreSQL este listo antes de migrar.

## Correr localmente

```
docker compose up --build
```

API en http://localhost:8000/api/v1/comprar/ e inventario en http://localhost:8000/inventario/

Para crear libros de prueba (en otra terminal):
```
docker compose exec web python manage.py shell
```

## Despliegue en AWS EC2

1. Instancia Amazon Linux 2023, puerto 8000 abierto en el Security Group.
2. Instalar git y docker, clonar este repo.
3. `docker compose up -d --build`
4. Entrar a `http://<IP-PUBLICA-EC2>:8000/api/v1/comprar/`
