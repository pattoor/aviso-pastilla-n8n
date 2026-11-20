# Aviso de pastilla con Telegram y n8n

Una pequeña automatización para enviar recordatorios diarios por medio de Telegram, con un botón de confirmación y un recordatorio de seguimiento si no se confirma a tiempo.

## Qué hace

- Envía un aviso diario (una vez configurado) con un botón para confirmar que se tomó la pastilla.
- Si pasadas 2 horas el aviso no fue confirmado, envía un recordatorio (⚠️) con el mismo botón de confirmación.
- Si el usuario confirma, el recordatorio no se envía y el mensaje del aviso se queda como "Confirmado".
- Todos los días a una hora configurada (11:55) se limpian los mensajes viejos del chat, por lo que solo queda el mensaje del día actual.
- Se guarda un registro CSV con el historial de avisos y confirmaciones.

## Estructura del proyecto

El proyecto consta de un workflow en n8n con varios nodos organizados en grupos funcionales que deciden cuándo enviar el aviso, el recordatorio, cómo manejar la confirmación y cómo limpiar los mensajes viejos.

## Cómo instalar (brevemente)

1. Tener n8n corriendo (versión 2.35.4 o superior).
2. Configurar las 2 líneas en el docker-compose.yml para montar la carpeta local al contenedor:
   - `N8N_RESTRICT_FILE_ACCESS_TO=/home/node/.n8n-files;/aviso-pastilla`
   - `./aviso-pastilla:/aviso-pastilla`
3. Importar el workflow desde el archivo de backup JSON en la interfaz de n8n.
4. Crear la Data Table `med_pastilla` con las columnas necesarias (fecha, numero, message_id, estado, confirmado_at, aviso_final, aviso_final_message_id, aviso_final_borrado, borrado, indice).
5. Ajustar los horarios y la zona horaria según corresponda (ej. `America/Argentina/Buenos_Aires`).
6. Poner el token del bot de Telegram en los nodos HTTP correspondientes.
7. Publicar el workflow para que los triggers y webhooks corran.

## Usos probados

- Se probó el flujo completo: aviso diario → confirmación mediante botón → registro en tabla → edición del mensaje en Telegram → recordatorio a las 2 horas si no se confirmó → limpieza automática a las 11:55.
- También se probaron los webhooks manuales (botón de confirmación y link del CSV).
- Las ejecuciones se pueden correr desde el editor de n8n disparando un paso concreto (por ejemplo, solo la limpieza o solo el recordatorio).

## Nota

Este proyecto fue armado y probado en un entorno de servidor local con integración a Telegram. Los datos sensibles (token del bot, chat ID, URLs del servidor) deben configurarse en la propia instalación y **no formar parte de este repositorio público**.