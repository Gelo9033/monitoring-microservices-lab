# Monitoreo de Microservicios con Prometheus y Alertmanager

Stack de observabilidad para microservicios Spring Boot registrados en Eureka, con alertas enviadas a Telegram mediante Alertmanager.

## Descripcion

Este proyecto levanta un entorno de monitoreo con:

- Prometheus para recoleccion y consulta de metricas.
- Alertmanager para ruteo y notificacion de alertas.
- Integracion con Eureka para descubrimiento de servicios por ambiente.
- Notificaciones a Telegram configuradas por variables de entorno.

## Arquitectura

- [docker-compose.yml](docker-compose.yml) define los servicios de Prometheus y Alertmanager.
- [prometheus/prometheus.yml](prometheus/prometheus.yml) contiene la configuracion de jobs y scraping.
- [prometheus/alertas.yml](prometheus/alertas.yml) contiene reglas de alerta.
- [alertmanager/config.yml](alertmanager/config.yml) define el receptor Telegram y politica de notificaciones.
- [data/](data/) es almacenamiento local de series temporales de Prometheus y no debe subirse al repositorio.

## Requisitos

- Docker
- Docker Compose
- Red Docker externa creada previamente con nombre network_docker

Crear la red (si no existe):

docker network create network_docker

## Configuracion

1. Copia [ .env.example ](.env.example) a .env

cp .env.example .env

2. Edita tu .env local con valores reales:

- TOKEN_TELEGRAM
- CHAT_ID_TELEGRAM
- EUREKA_SERVER_dev
- EUREKA_SERVER_qa
- EUREKA_SERVER_prod
- USERNAME_EUREKA_DEV, PASSWORD_EUREKA_DEV
- USERNAME_EUREKA_QA, PASSWORD_EUREKA_QA
- USERNAME_EUREKA_PROD, PASSWORD_EUREKA_PROD

3. Verifica que no subes secretos:

- [ .gitignore ](.gitignore) ya excluye .env y data.
- Nunca publiques credenciales reales en archivos versionados.

## Levantar el entorno

Iniciar servicios:

docker compose up -d

Ver estado:

docker compose ps

Ver logs de Alertmanager:

docker compose logs --tail=100 alertmanager

Detener servicios:

docker compose down

## Seguridad y buenas practicas

- .env se usa solo local y no se sube al repositorio.
- Si un token fue expuesto en cualquier commit, rotalo antes de publicar.
- La carpeta data contiene informacion operativa de Prometheus y debe mantenerse fuera de Git.
- [alertmanager/config.yml](alertmanager/config.yml) usa variables de entorno y [docker-compose.yml](docker-compose.yml) habilita expansion con --config.expand-env.

## Publicacion en GitHub

Checklist recomendado antes de hacer push:

- git ls-files .env no debe mostrar salida.
- git ls-files | grep ^data/ no debe mostrar salida.
- git show --name-only --stat no debe incluir .env ni data.

## Notas

- El archivo [prometheus/prometheus.yml](prometheus/prometheus.yml) incluye placeholders para endpoints y credenciales de Eureka. Ajusta esos valores segun tu entorno real de despliegue.
