# Integración de File Integrity Monitoring (FIM) + VirusTotal con Wazuh

## 1. Descripción

Configuración de File Integrity Monitoring (FIM) en Wazuh Agent sobre Windows 11 para monitorear cambios en archivos y directorios, e integración con VirusTotal para consultar la reputación de archivos asociados a eventos FIM.

## 2. Configuración de File Integrity Monitoring (FIM)

### Paso 1. Editar el archivo de configuración de Wazuh Agent

Abrir el archivo:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

Agregar o ajustar dentro de la sección `<syscheck>`:

```xml
<syscheck>
  <directories check_all="yes" report_changes="yes" realtime="yes">C:\Users\USUARIO\Downloads</directories>
</syscheck>
```

Reemplazar `USUARIO` por el nombre de usuario correspondiente.

El directorio configurado será monitoreado para detectar cambios en archivos.

### Paso 2. Reiniciar Wazuh Agent

Abrir CMD con permisos de administrador:

```cmd
net stop Wazuh
net start Wazuh
```

## 3. Integración con VirusTotal

### Paso 1. Obtener una API key

Registrarse en [VirusTotal](https://www.virustotal.com/) y obtener una clave API para utilizar la integración.

### Paso 2. Configurar Wazuh Manager

En Ubuntu Server, editar:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Agregar el siguiente bloque dentro de la configuración existente:

```xml
<integration>
  <name>virustotal</name>
  <api_key>YOUR_VIRUSTOTAL_API_KEY</api_key>
  <group>syscheck</group>
  <alert_format>json</alert_format>
</integration>
```

Reemplazar `YOUR_VIRUSTOTAL_API_KEY` por una clave válida en el entorno local. No publicar claves API reales en GitHub.

### Paso 3. Reiniciar Wazuh Manager

```bash
systemctl restart wazuh-manager
```

## 4. Pruebas de funcionamiento

### Paso 1. Crear un archivo de prueba EICAR

En el directorio monitoreado, crear un archivo llamado `eicar.txt`.

Introducir la cadena estándar de prueba EICAR:

```text
X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*
```

EICAR es un archivo de prueba utilizado para verificar mecanismos de detección antivirus; no contiene malware real.

### Paso 2. Visualizar los eventos en Wazuh Dashboard

Acceder a Wazuh Dashboard y verificar los eventos FIM y las respuestas de la integración con VirusTotal.

### Paso 3. Crear un segundo archivo de prueba

Crear `malwaretest.txt` en el directorio monitoreado utilizando la misma cadena EICAR.

Comprobar nuevamente los eventos generados.

## 5. Validación de la integración

Durante las pruebas del laboratorio se observaron eventos de creación y eliminación de archivos, así como respuestas de VirusTotal en Wazuh Dashboard.

Las evidencias incluyen consultas con resultados `No positives found` y `No records in VirusTotal database`.

Estos resultados demuestran la recepción y el procesamiento de respuestas de la integración; no deben interpretarse por sí solos como detecciones positivas de malware.

## 6. Referencias

- [Wazuh Documentation](https://documentation.wazuh.com/current/)
- [VirusTotal](https://www.virustotal.com/)
- [EICAR — Anti-Malware Test File](https://www.eicar.org/download-anti-malware-testfile/)
