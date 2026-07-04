# Instalación y Configuración

Esta guía permite reproducir el entorno completo de SportMatch en local: backend, frontend, autenticación (Keycloak) y mensajería (RabbitMQ).

## Requisitos previos

| Herramienta | Versión recomendada |
|---|---|
| Java (JDK) | 21 |
| Maven | 3.9+ |
| Node.js | 20 LTS |
| npm | 10+ |
| Docker + Docker Compose | 24+ |
| Git | 2.40+ |

## 1. Clonar el repositorio

```bash
git clone https://codeberg.org/<org>/sportmatch.git
cd sportmatch
```

## 2. Levantar servicios de infraestructura (Docker Compose)

Keycloak y RabbitMQ se levantan como contenedores.

```yaml
# docker-compose.yml (raíz del proyecto)
version: "3.8"
services:
  keycloak:
    image: quay.io/keycloak/keycloak:25.0
    command: start-dev
    environment:
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
    ports:
      - "9090:8080"

  rabbitmq:
    image: rabbitmq:3.13-management
    ports:
      - "5672:5672"   # AMQP
      - "15672:15672" # Panel de administración
    environment:
      RABBITMQ_DEFAULT_USER: guest
      RABBITMQ_DEFAULT_PASS: guest
```

```bash
docker compose up -d
```

- Keycloak admin: http://localhost:8081 (admin/admin)
- RabbitMQ panel: http://localhost:15672 (guest/guest)

### Configurar el realm de Keycloak

1. Entra al panel de administración de Keycloak.
2. Crea un realm llamado `sportmatch`.
3. Crea un cliente `sportmatch-frontend` con:
   - Access Type: `public`
   - Valid Redirect URIs: `http://localhost:3000/*`
   - Web Origins: `http://localhost:3000`
4. Crea al menos un usuario de prueba con contraseña temporal.

## 3. Backend (Spring Boot)

```bash
cd backend
```

Configura `src/main/resources/application.properties`:

```properties
# Base de datos
spring.datasource.url=jdbc:postgresql://localhost:5432/sportmatch
spring.datasource.username=sportmatch
spring.datasource.password=sportmatch

# Keycloak / OAuth2 Resource Server
spring.security.oauth2.resourceserver.jwt.issuer-uri=http://localhost:8081/realms/sportmatch

# RabbitMQ
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
```

Ejecutar:

```bash
mvn clean install
mvn spring-boot:run
```

El backend queda disponible en `http://localhost:8080`.

### Librerías clave del backend

| Librería | Versión | Uso |
|---|---|---|
| Spring Boot | 3.3.x | Framework base |
| Spring Security (OAuth2 Resource Server) | 3.3.x | Validación JWT con Keycloak |
| Spring AMQP | 3.1.x | Integración con RabbitMQ |
| Spring WebSocket | 3.3.x | Notificaciones en tiempo real (STOMP) |
| springdoc-openapi | 2.6.0 | Generación de documentación de API |

## 4. Frontend (React)

```bash
cd frontend
npm install
```

Configura `.env`:

```env
VITE_API_BASE_URL=http://localhost:8080
VITE_KEYCLOAK_URL=http://localhost:8081
VITE_KEYCLOAK_REALM=sportmatch
VITE_KEYCLOAK_CLIENT_ID=sportmatch-frontend
```

Ejecutar:

```bash
npm run dev
```

El frontend queda disponible en `http://localhost:3000`.

### Librerías clave del frontend

| Librería | Versión | Uso |
|---|---|---|
| React | 18.x | UI |
| @dnd-kit/core | 6.x | Drag-and-drop del armador de alineaciones |
| Tailwind CSS | 3.x | Estilos |
| Leaflet + react-leaflet | 1.9.x / 4.x | Mapa de canchas (OpenStreetMap) |
| @stomp/stompjs + sockjs-client | — | Cliente WebSocket para notificaciones |
| keycloak-js | 25.x | Cliente de autenticación |

## 5. Verificación rápida

1. Abre `http://localhost:3000` e inicia sesión con el usuario de prueba de Keycloak.
2. Realiza una reserva en el mapa.
3. Verifica en el panel de RabbitMQ (`http://localhost:15672`) que se publique un mensaje en la cola de notificaciones.
4. Confirma que la notificación llega en tiempo real a la interfaz.

## Documentación de la API

El spec OpenAPI se genera automáticamente y se documenta en [API Reference](api.md).