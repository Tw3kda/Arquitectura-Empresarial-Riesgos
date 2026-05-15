# Análisis de Riesgos de Arquitectura Empresarial
**Proyecto:** ACR VITAL LABORAL SAS  
**Caso de Estudio:** Problema #3 - Consolidación Financiera y Gestión de Cartera  
Integrantes del equipo

    David Santiago Medina (davidmebu@unisabana.edu.co)
    Santiago Navarro (santiagonacu@unisabana.edu.co)
    Jacobo Pacheco (jacobopama@unisabana.edu.co)
    Juan Diego Martínez (juandmaes@unisabana.edu.co)


---

## 1. Descripción de la Situación Actual (Arquitectura AS-IS)

La arquitectura actual de **ACR VITAL LABORAL SAS** se clasifica como una **Cloud Básica No Estructurada**, caracterizada por una alta dependencia de procesos manuales y herramientas ofimáticas aisladas.

* **Infraestructura y Almacenamiento:** El núcleo financiero reside en archivos de Excel dispersos almacenados en **Google Drive**. El acceso depende de la seguridad de dispositivos locales y la estabilidad de la conexión a internet.
* **Gestión de Datos:** Existe una desconexión total entre las dos líneas de negocio (Salud Ocupacional y CRC). La información de ingresos se transcribe manualmente desde los sistemas operativos (**BIOFILE** y recibos de **SISEC**) hacia un Excel consolidado mensual.
* **Procesos de Cartera:** El seguimiento de los créditos corporativos (plazos de hasta 3 meses) es puramente visual y reactivo. No existen alertas automáticas, lo que delega la detección de morosidad a la revisión periódica manual.
* **Gobierno TI:** El conocimiento del proceso está centralizado en dos personas, lo que genera una alta dependencia del factor humano y una gobernanza de datos limitada a permisos básicos de edición.

---

### Matriz de Riesgos de Arquitectura Empresarial 

| Dominio | Riesgo Identificado | Descripción Concisa | Impacto | Probabilidad |
| :--- | :--- | :--- | :--- | :--- |
| **Negocio** | **Riesgo de Liquidez** | Falta de alertas de vencimiento que incrementa la morosidad y afecta el capital de trabajo. | **Crítico** | **Alta** |
| **Negocio** | **Dependencia de Personas** | Operatividad contable supeditada al conocimiento de un único actor clave (Administradora). | **Alto** | **Media** |
| **Procesos** | **Errores de Transcripción** | Digitación manual de datos desde BIOFILE y SISEC a Excel, propensa a fallos humanos. | **Alto** | **Alta** |
| **Procesos** | **Falta de Trazabilidad** | Uso de Gmail y archivos planos que impide auditar cambios en saldos o negociaciones. | **Alto** | **Alta** |
| **Datos** | **Silos de Información** | Desconexión total entre el área de Salud Ocupacional y el CRC, limitando la visión 360°. | **Medio** | **Alta** |
| **Datos** | **Baja Integridad** | Hojas de Excel vulnerables a errores de fórmulas, duplicidad y corrupción de archivos. | **Alto** | **Media** |
| **Aplicaciones** | **Alta Deuda Técnica** | Uso de herramientas ofimáticas básicas para procesos financieros complejos y crecientes. | **Medio** | **Alta** |
| **Infraestructura** | **Punto Único de Falla** | Dependencia de un computador físico y conexión a internet para acceder a la nube. | **Crítico** | **Media** |
| **Seguridad** | **Accesos Inseguros** | Control de acceso básico en Drive sin un modelo formal de roles ni auditoría. | **Alto** | **Alta** |
| **Gobierno TI** | **Falta de Estándares** | Decisiones arquitectónicas no documentadas y procesos basados en prácticas informales. | **Bajo** | **Media** |

---

## 3. Propuesta de Arquitectura Objetivo (TO-BE)

La solución propuesta evoluciona hacia una **Arquitectura Centralizada y Escalable**, diseñada para mitigar los riesgos identificados.

* **Capa de Integración (Backend):** Implementación de un servicio de API para la **carga masiva** de reportes. Esto elimina la transcripción manual y estandariza la ingesta de datos desde BIOFILE y SISEC.
* **Capa de Datos (Datalayer):** Base de datos relacional (Financial_DB) que mantiene libros contables separados por área, pero permite una consolidación automática en tiempo real.
* **Motor de Reglas de Cartera:** Sistema automatizado que:
    1. Registra la fecha de la primera cuenta de cobro.
    2. Calcula saldos totales considerando pagos parciales.
    3. Dispara **alertas internas** periódicas antes de cumplirse el plazo de 3 meses.
* **Capa de Presentación:** Dashboards interactivos con indicadores históricos y gráficos de rendimiento para el apoyo a la toma de decisiones gerenciales.

---
### 4. Matriz de Mitigación (Arquitectura TO-BE)

| Riesgo Identificado | Mitigación Arquitectónica (TO-BE) | Mejora Detallada Obtenida |
| :--- | :--- | :--- |
| **Errores de digitación manual** | **Carga Masiva y API de Ingesta:** Implementación de un componente que procesa reportes estructurados de BIOFILE y SISEC. | **Integridad de Datos:** Eliminación total del error humano en el traspaso de información y garantía de consistencia financiera. |
| **Morosidad no detectada** | **Motor de Reglas y Alertas Automáticas:** Sistema que monitorea el límite de 3 meses y notifica preventivamente al equipo administrativo. | **Optimización de Recaudo:** Reducción proactiva de la cartera morosa y fortalecimiento del flujo de caja corporativo. |
| **Acceso inseguro / Falta de logs** | **Servicio de Identidad (IAM) y Audit Logs:** Control de acceso por roles (Admin/Analista) y registro histórico de toda actividad. | **Seguridad y Cumplimiento:** Trazabilidad absoluta de cambios en saldos y cumplimiento estricto de la Ley 1581 (Protección de Datos). |
| **Silos de información** | **Capa de Datos Centralizada (Financial_DB):** Unificación de los ingresos/egresos de Salud Ocupacional y CRC en un repositorio relacional. | **Visión 360° del Negocio:** Eliminación de la fragmentación de datos, permitiendo una visión financiera integral de la IPS. |
| **Decisiones sin datos** | **Dashboard de Reporting Service:** Generación automática de gráficos de rendimiento e indicadores históricos. | **Inteligencia de Negocio:** Toma de decisiones basada en evidencia y proyecciones reales en lugar de procesos manuales reactivos. |
| **Punto único de falla (SPOF)** | **Infraestructura Cloud Redundante:** Migración del almacenamiento de archivos locales a una base de datos distribuida con respaldos automáticos. | **Continuidad del Negocio:** Garantía de disponibilidad del sistema contable independientemente de fallos en dispositivos físicos locales. |


> **Conclusión:** La transición al modelo TO-BE permite a **ACR VITAL LABORAL SAS** transformar su gestión financiera de un proceso manual propenso al riesgo en una operación estratégica, segura y alineada con los objetivos de crecimiento de la IPS.
