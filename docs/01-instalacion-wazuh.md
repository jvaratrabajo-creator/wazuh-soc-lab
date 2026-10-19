
# Instalación de Wazuh Server en Ubuntu Server 24.04

## 1. Descripción

Implementación de Wazuh mediante el método oficial **Installation Assistant / Quickstart**, utilizando una arquitectura **All-in-One** sobre Ubuntu Server 24.04.

Los componentes Wazuh Manager, Wazuh Indexer y Wazuh Dashboard se encuentran instalados en el mismo servidor.

## 2. Instalación de Wazuh

Se utilizó como referencia el procedimiento oficial de instalación rápida de Wazuh:

https://documentation.wazuh.com/current/quickstart.html

> Nota: El archivo TXT original registra la referencia a la guía oficial, pero no incluye los comandos ejecutados durante la instalación inicial.

## 3. Ampliación del volumen lógico de Ubuntu Server

Este procedimiento se documentó para el caso de necesitar ampliar el volumen lógico después de instalar Ubuntu Server.

### Paso 1. Verificar el espacio en disco

```bash
df -h /
```

### Paso 2. Ampliar el volumen lógico

```bash
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

Este comando amplía el volumen lógico utilizando el espacio libre disponible en el grupo de volúmenes.

### Paso 3. Redimensionar el sistema de archivos EXT4

```bash
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

### Paso 4. Verificar la ampliación

```bash
df -h /
```

> Importante: Los comandos anteriores corresponden a la ruta LVM documentada en el laboratorio. Antes de reutilizarlos en otro sistema, se debe verificar el volumen lógico y el tipo de sistema de archivos.

## 4. Vulnerability Detection — Configuración documentada

El procedimiento original incluye los siguientes pasos relacionados con el análisis de vulnerabilidades del propio Wazuh Manager.

### Paso 1. Editar el archivo de opciones internas

```bash
sudo nano /var/ossec/etc/internal_options.conf
```

### Paso 2. Localizar el parámetro documentado

```ini
vulnerability-detection.disable_scan_manager=1
```

### Paso 3. Modificar su valor

```ini
vulnerability-detection.disable_scan_manager=0
```

Guardar los cambios con `Ctrl+O` y salir con `Ctrl+X`.

### Paso 4. Reiniciar Wazuh Manager

```bash
sudo systemctl restart wazuh-manager
```

### Paso 5. Verificar el estado del servicio

```bash
sudo systemctl status wazuh-manager
```

> Nota técnica: Esta configuración se conserva como parte del procedimiento original. Su compatibilidad y efecto concreto deben verificarse para la versión de Wazuh utilizada antes de recomendar su aplicación en otros entornos.

## 5. Referencias

- [Wazuh — Quickstart](https://documentation.wazuh.com/current/quickstart.html)
 
