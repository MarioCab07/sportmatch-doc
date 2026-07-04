# Guía de Usuario

Esta guía cubre el uso básico de SportMatch desde la perspectiva de un usuario final.

## 1. Iniciar sesión

SportMatch usa Keycloak para la autenticación:

1. Entra a la aplicación en el navegador.
2. Haz clic en **Iniciar sesión**. Serás redirigido a la pantalla de login de Keycloak.
3. Ingresa tus credenciales.
4. Al autenticarte, Keycloak te redirige de vuelta a SportMatch con la sesión activa.

Si no tienes cuenta, contacta al administrador del club/torneo para que te cree un usuario, o usa la opción de registro si está habilitada en el realm.

## 2. Reservar una cancha

1. Ve a la sección **Canchas**.
2. El mapa (basado en OpenStreetMap/Leaflet) muestra las canchas disponibles según tu ubicación o filtro de ciudad.
3. Selecciona una cancha en el mapa o en el listado.
4. Elige fecha y horario disponible.
5. Confirma la reserva. Recibirás una notificación en tiempo real confirmando el estado.

## 3. Armar una alineación (Lineup Builder)

Disponible para fútbol y básquetbol.

1. Ve a la sección **Alineaciones**.
2. Selecciona el deporte con las pestañas superiores (fútbol / básquetbol). Cada deporte mantiene su propia alineación de forma independiente.
3. Arrastra a los jugadores desde la lista lateral hacia las posiciones en la cancha (SVG interactivo).
4. Puedes reordenar arrastrando un jugador ya ubicado a otra posición.
5. Guarda la alineación cuando termines.

## 4. Notificaciones en tiempo real

- El ícono de campana muestra notificaciones nuevas (reservas confirmadas, cambios de horario, invitaciones a equipo, etc.).
- Las notificaciones llegan automáticamente sin recargar la página (WebSocket).
- Al cerrar sesión, la conexión de notificaciones se cierra correctamente y no persiste para el siguiente usuario que use el mismo navegador.

## 5. Cerrar sesión

Haz clic en tu perfil (esquina superior derecha) → **Cerrar sesión**. Esto invalida la sesión tanto en SportMatch como en Keycloak.

## Preguntas frecuentes

**¿Por qué no veo canchas en el mapa?**
Verifica que tu navegador tenga permisos de ubicación habilitados, o busca manualmente por ciudad/zona.

**¿Puedo tener alineaciones guardadas para varios equipos?**
Sí, cada equipo mantiene su propio historial de alineaciones por deporte.

**No me llegan notificaciones en tiempo real.**
Revisa tu conexión a internet y que la sesión siga activa; si el problema persiste, cierra sesión y vuelve a entrar.