
# Instalación de Wazuh Server en Ubuntu Server 24.04

Documentación del procedimiento realizado en el laboratorio Wazuh SOC Lab.

En caso de tener que ampliar el volumen lógico de Ubuntu server, Luego de la instalacion:

1. Verificar espacio en disco

df -h /

2. Ampliar el volumen lógico
Ejecutar este comando para ampliar el volumen lógico a todo el espacio libre en Ubuntu Server 24.04.5:

sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv

3. Redimensionar el sistema de archivos EXT4
Luego, amplíar el sistema de archivos que está montado en / para que aproveche el nuevo espacio del disco:

sudo resize2fs /dev/ubuntu-vg/ubuntu-lv

4. Verificar que se haya ampliado correctamente

df -h /

///////////////// INSTALACIÓN WAZUH-MANAGER ////////////////////

Documentación oficial:
https://documentation.wazuh.com/current/quickstart.html

///////////////// VULNERABILITY DETECTION ////////////////////


Por defecto, Wazuh NO escanea el propio servidor/manager (Ubuntu Server). 
Para activarlo:

1. Editar /var/ossec/etc/internal_options.conf (ejecutando el siguiente comando)

    sudo nano /var/ossec/etc/internal_options.conf

2. Busca la línea en el editable:
    
    vulnerability-detection.disable_scan_manager=1
    
3. Cambia el valor a 0:
    
    `vulnerability-detection.disable_scan_manager=0

- ctrl+O para guardar y Ctrl+X para salir    

4. Reiniciar Wazuh-Manager

sudo systemctl restart wazuh-manager

5. Verificar el estado de Wazuh-Manager

sudo systemctl status wazuh-manager

  
