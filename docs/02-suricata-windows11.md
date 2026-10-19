
# Integración de Suricata IDS con Wazuh Agent en Windows 11

## 1. Descripción

Implementación de Suricata IDS sobre Windows 11 para monitorear tráfico de red y generar eventos de seguridad, integrándolos con Wazuh SIEM/XDR mediante Wazuh Agent.

Suricata genera eventos en formato JSON en el archivo `eve.json`, que posteriormente son recopilados y enviados al Wazuh Manager para su análisis y visualización.

## 2. Instalación de componentes

Descargar e instalar los siguientes componentes:

1. [Npcap](https://npcap.com/#download)
2. [Suricata](https://suricata.io/download/)
3. [Emerging Threats Open Rules](https://rules.emergingthreats.net/open/)

## 3. Configuración de Suricata

Editar el archivo:

```text
C:\Program Files\Suricata\Suricata.yaml
```

Configurar la dirección IP correspondiente al equipo Windows 11:

```yaml
address-groups:
    HOME_NET: "[IP_WINDOWS11]"
```

Agregar el archivo de reglas descargado:

```yaml
rule-files:
  - emerging-all.rules
```

Las secciones anteriores son fragmentos del archivo de configuración, no representan el contenido completo de `Suricata.yaml`.

## 4. Identificación del adaptador de red

El procedimiento original utiliza el identificador UUID del adaptador de red.

Comando original:

```cmd
wmic nicconfig get ipaddress,SettingID
```

Debido a que WMIC está deprecado en versiones recientes de Windows, se documentó la siguiente alternativa mediante PowerShell:

```powershell
Get-CimInstance Win32_NetworkAdapterConfiguration | Where-Object { $_.IPAddress } | Select-Object @{n="IPAddress";e={$_.IPAddress -join ", "}}, SettingID
```

Identificar el valor `SettingID` correspondiente a la interfaz que se desea monitorear.

## 5. Ejecución de Suricata IDS

Abrir CMD con permisos de administrador y ejecutar:

```cmd
"C:\Program Files\Suricata\suricata.exe" -c "C:\Program Files\Suricata\Suricata.yaml" -i \Device\NPF_{UUID_ADAPTADOR}
```

Reemplazar `UUID_ADAPTADOR` por el identificador correspondiente al adaptador de red.

## 6. Verificación de generación de eventos

Realizar una prueba de conectividad mediante ping desde otro equipo y comprobar que se generen registros en:

```text
C:\Program Files\Suricata\log\eve.json
```

La generación de registros permite verificar la captura de actividad. Una alerta IDS requiere además que el tráfico coincida con una regla de detección.

## 7. Integración con Wazuh Agent

Editar el archivo de configuración del agente Wazuh:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

Agregar el siguiente bloque dentro de la configuración del agente:

```xml
<localfile>
  <log_format>json</log_format>
  <location>C:\Program Files\Suricata\log\eve.json</location>
</localfile>
```

Reiniciar el servicio Wazuh Agent desde CMD con permisos de administrador:

```cmd
net stop Wazuh
net start Wazuh
```

## 8. Validación en Wazuh Dashboard

Ingresar al Dashboard de Wazuh y verificar los eventos generados por Suricata correspondientes al agente Windows 11.

En el laboratorio se comprobó la recepción de eventos del grupo `suricata` mediante Threat Hunting, incluyendo alertas asociadas a reglas IDS.

## 9. Inicio automático mediante el Programador de tareas

1. Abrir el Programador de tareas de Windows (Task Scheduler).
2. Seleccionar **Crear tarea básica**.
3. Asignar el nombre `SuricataIDSWazuh`.
4. Seleccionar **Al iniciar el sistema** como desencadenador.
5. En **Acción**, seleccionar **Iniciar un programa**.
6. En **Programa o script**, introducir:

```text
C:\Program Files\Suricata\suricata.exe
```

7. En **Agregar argumentos**, introducir:

```text
-c "C:\Program Files\Suricata\Suricata.yaml" -i \Device\NPF_{UUID_ADAPTADOR}
```

8. Guardar y ejecutar la tarea programada.

La tarea debe contar con los permisos necesarios para capturar tráfico y acceder a los archivos de configuración.

## 10. Referencias

- [Npcap](https://npcap.com/)
- [Suricata](https://suricata.io/)
- [Emerging Threats Open Rules](https://rules.emergingthreats.net/open/)
- [Wazuh Documentation](https://documentation.wazuh.com/current/)
  
