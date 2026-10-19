# Security Evidence / Evidencias del laboratorio

Esta sección reúne las capturas de pantalla obtenidas durante la implementación y validación del laboratorio SOC basado en Wazuh SIEM/XDR.

Las evidencias muestran el monitoreo de endpoints Windows/Linux, la recopilación de eventos de seguridad y el funcionamiento de las integraciones implementadas.

## Inventario de evidencias

| N.º | Evidencia | Descripción |
|---|---|---|
| 01 | [Dashboard Overview](01-dashboard-overview.png) | Estado general de agentes y alertas de seguridad. |
| 02 | [Wazuh Agents](02-wazuh-agents.png) | Cuatro agentes activos con sistemas Windows y Linux. |
| 03 | [Windows 11 Threat Hunting](03-windows11-threat-hunting.png) | Visualización de eventos de seguridad del agente Windows 11. |
| 04 | [Suricata Alerts](04-suricata-alerts.png) | Eventos IDS generados por Suricata y recopilados por Wazuh. |
| 05 | [Suricata ICMP Detection](05-suricata-icmp-tests.png) | Alertas asociadas a tráfico ICMP. |
| 06 | [FIM + VirusTotal Events](06-fim-virustotal-events.png) | Eventos de integridad de archivos y respuestas VirusTotal. |
| 07 | [VirusTotal Details](07-virustotal-details.png) | Detalles de la consulta de reputación de archivos. |
| 08 | [Email Alerts](08-email-alerts.png) | Correos de notificación generados mediante Wazuh y Postfix. |
| 09 | [Vulnerability Detection](09-vulnerability-detection.png) | Vulnerabilidades clasificadas por severidad y sistemas afectados. |

## Consideraciones

Las capturas corresponden a diferentes momentos de las pruebas realizadas en el laboratorio. Los eventos, métricas y alertas reflejan la información disponible en cada captura y no representan necesariamente incidentes de seguridad maliciosos.

Estas evidencias tienen fines educativos y de demostración técnica.
