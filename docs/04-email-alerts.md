
# Integración de Email Alerts con Postfix y Gmail SMTP

## 1. Descripción

Implementación de notificaciones automáticas de seguridad en Wazuh SIEM/XDR mediante Postfix como servidor de retransmisión de correo (SMTP relay) y Gmail SMTP como servicio de envío.

Esta integración permite recibir alertas generadas por Wazuh Manager directamente en una cuenta de correo electrónico.

## 2. Requisitos previos

- Wazuh Manager instalado en Ubuntu Server 24.04.
- Cuenta de Gmail.
- Autenticación en dos pasos habilitada en Google.
- Contraseña de aplicación de Google para autenticación SMTP.
- Conectividad hacia `smtp.gmail.com` por el puerto TCP 587.

**Importante:** las contraseñas de aplicación y los correos personales se sustituyen por valores de ejemplo para evitar la exposición de credenciales.

## 3. Instalación de Postfix

### Paso 1. Instalar Postfix y dependencias

Acceder a la terminal de Ubuntu Server y ejecutar:

```bash
sudo apt-get update && sudo apt-get install postfix mailutils libsasl2-2 ca-certificates libsasl2-modules -y
```

Durante la instalación pueden aparecer pantallas de configuración interactiva de Postfix. Completar las opciones correspondientes al entorno.

## 4. Configuración de Postfix con Gmail SMTP

### Paso 1. Editar el archivo principal

```bash
sudo nano /etc/postfix/main.cf
```

Agregar las siguientes líneas al final del archivo:

```ini
relayhost = [smtp.gmail.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
smtp_use_tls = yes
smtpd_relay_restrictions = permit_mynetworks, permit_sasl_authenticated, defer_unauth_destination
```

Estas opciones configuran Gmail como servidor SMTP de retransmisión y habilitan la autenticación SASL y el uso de TLS.

### Paso 2. Configurar credenciales Gmail

El procedimiento original utiliza el archivo:

```text
/etc/postfix/sasl_passwd
```

La entrada debe mantener el siguiente formato:

```text
[smtp.gmail.com]:587 usuario@gmail.com:YOUR_GMAIL_APP_PASSWORD
```

Reemplazar los valores de ejemplo por las credenciales correspondientes únicamente en el servidor local.

**No publicar contraseñas de aplicación reales en GitHub.**

El procedimiento original utiliza el siguiente comando para generar el mapa de credenciales:

```bash
sudo postmap /etc/postfix/sasl_passwd
```

**Nota de seguridad:** el archivo `sasl_passwd` y su mapa generado deben protegerse mediante permisos restrictivos para evitar accesos no autorizados. La contraseña no debe introducirse en comandos que puedan quedar registrados en el historial de la terminal.

## 5. Prueba de envío SMTP

Una vez configurado Postfix, realizar una prueba de envío de correo electrónico.

Comando utilizado en el laboratorio, con direcciones anonimizadas:

```bash
echo "Test mail from postfix" | mail -s "Test Postfix" -r "usuario@gmail.com" destinatario@gmail.com
```

Comprobar que el mensaje de prueba llegue a la bandeja de entrada de la cuenta destinataria.

## 6. Configuración de Email Alerts en Wazuh

### Paso 1. Editar ossec.conf

En Ubuntu Server, ejecutar:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Dentro del bloque `<global>` existente, configurar los parámetros de correo:

```xml
<email_notification>yes</email_notification>
<smtp_server>localhost</smtp_server>
<email_from>usuario@gmail.com</email_from>
<email_to>destinatario@gmail.com</email_to>
```

Estos parámetros permiten que Wazuh utilice el servidor SMTP local para enviar notificaciones.

### Paso 2. Configurar nivel de alertas

Dentro del bloque `<alerts>` existente, configurar:

```xml
<log_alert_level>3</log_alert_level>
<email_alert_level>3</email_alert_level>
```

Para las pruebas del laboratorio se configuró el nivel de notificación por correo en `3`.

**Observación:** este nivel fue utilizado con fines de validación. En entornos con alto volumen de eventos puede generar una cantidad considerable de notificaciones.

## 7. Reinicio de Wazuh Manager

Aplicar los cambios reiniciando el servicio:

```bash
systemctl restart wazuh-manager
```

## 8. Validación de funcionamiento

Acceder a Gmail y comprobar la recepción de notificaciones generadas automáticamente por Wazuh.

Durante las pruebas del laboratorio se evidenció la recepción de correos correspondientes a eventos de distintos niveles de alerta, incluyendo notificaciones de los agentes monitoreados.

La recepción de los mensajes confirma el funcionamiento del envío de notificaciones mediante Wazuh, Postfix y Gmail SMTP.

## 9. Consideraciones de seguridad

- No publicar contraseñas de aplicación de Gmail.
- Proteger los archivos de autenticación SMTP.
- Evitar mostrar direcciones de correo personales en capturas públicas.
- Utilizar filtros y niveles de alerta adecuados para controlar el volumen de notificaciones.
- Revocar las credenciales que hayan sido expuestas accidentalmente.

## 10. Referencias

- [Wazuh — Alert Management](https://documentation.wazuh.com/current/user-manual/manager/alert-management.html)
- [Postfix — Documentación oficial](https://www.postfix.org/documentation.html)
- [Google — Contraseñas de aplicación](https://support.google.com/accounts/answer/185833)
