
# Security Command Center: Cloud Storage Vulnerability Remediation

**Basado en el laboratorio:** *Usa informes para abordar hallazgos (Google Cloud Skills Boost)*

> **Entorno de Laboratorio:** Google Cloud Platform (GCP)  
> **Herramientas:** Google Security Command Center (SCC), Cloud Storage Console  

---

##  Objetivos

- **Identificar vulnerabilidades de seguridad:** Auditar los recursos del entorno utilizando Security Command Center (SCC) para detectar configuraciones erróneas de riesgo alto y medio (como accesos públicos no autorizados y falta de políticas uniformes).
- **Remediar riesgos en Cloud Storage:** Aplicar prácticas de *hardening* mediante la revocación de accesos públicos (`allUsers`) y la activación del acceso uniforme a nivel de bucket (*Uniform Bucket-Level Access*) para prevenir fugas de información.
---
##  Diagrama de Arquitectura de Seguridad

```
┌──────────────────────────────────────────────────────────────────────────────────────────────┐
│                                GOOGLE CLOUD PLATFORM (GCP)                                   │
│                                                                                              │
│   ┌───────────────────┐                  ┌──────────────────────────────────────────────┐    │
│   │   INTERNET        │                  │        SECURITY COMMAND CENTER (SCC)         │    │
│   │  (allUsers)       │                  │   - Evaluación de Postura (CIS Benchmark)    │    │
│   └─────────┬─────────┘                  └──────────────────────▲───────────────────────┘    │
│             │                                                   │                            │
│             │  Acceso Denegado                                  │ Auditoría &                │
│             │ (PUBLIC_BUCKET_ACL Revocado)                      │ Cumplimiento               │
│             ▼                                                   │                            │
│   ┌─────────┴───────────────────────────────────────────────────┴──────────────────────┐     │
│   │   CLOUD STORAGE BUCKET                                                             │     │
│   │   • Nivel de Permisos: Uniform Bucket-Level Access (Habilitado)                    │     │
│   │   • Acceso Externo: Revocado (allUsers / Lectura Pública Eliminada)                │     │
│   └────────────────────────────────────────────────────────────────────────────────────┘     │
│                                                                                              │
│   ┌─────────────────────────────────────────┐     Logs      ┌──────────────────────────┐     │
│   │   REGLAS DE FIREWALL                    ├──────────────►│     CLOUD LOGGING        │     │
│   │   - Firewall Flow Logs Habilitados      │               └──────────────────────────┘     │
│   └─────────────────────────────────────────┘                                                │
└──────────────────────────────────────────────────────────────────────────────────────────────┘
```


##  Revisión de Vulnerabilidades

![Revisión de vulnerabilidades inicial](https://github.com/user-attachments/assets/f7e82a24-86fe-4bed-aecd-9be222ec8ff9)

Al entrar a la consola de Security Command Center, nos encontramos principalmente con **3 vulnerabilidades activas**:

1. **LCA de buckets públicos (`PUBLIC_BUCKET_ACL`):**  
   Esta entrada indica que existe una regla en la Lista de Control de Acceso (ACL / LCA) del bucket de almacenamiento que lo hace de acceso público, lo que significa que cualquier persona en Internet puede leer los archivos almacenados. Es una vulnerabilidad de seguridad de **alto riesgo** que requiere priorizar su corrección.

2. **Solo política del bucket inhabilitada (`BUCKET_POLICY_ONLY_DISABLED`):**  
   Indica que los permisos uniformes a nivel de bucket no están habilitados. El acceso uniforme a nivel de bucket permite controlar quién puede acceder a los buckets y objetos de Cloud Storage mediante políticas centralizadas de IAM, simplificando la gestión de permisos. Es una vulnerabilidad de **riesgo medio** que también se debe corregir.

3. **Registro de buckets inhabilitado (`BUCKET_LOGGING_DISABLED`):**  
   Indica que hay un bucket de almacenamiento que no tiene habilitado el registro de auditoría. Es una vulnerabilidad de **bajo riesgo** que no es necesario corregir para este escenario de laboratorio.

---

##  Inspección de Vulnerabilidades a Resolver

![Inspección de vulnerabilidades a resolver](https://github.com/user-attachments/assets/f9290175-df87-4b4e-98a0-be10070d81c0)

Del lado izquierdo podemos apreciar **76 hallazgos activos y 1 inactivo** inicialmente al revisar.

- **LCA de buckets públicos (`PUBLIC_BUCKET_ACL`):** Indica una regla ACL pública abierta hacia Internet, permitiendo la lectura sin autenticación (Riesgo Alto).
- **Solo política del bucket inhabilitada (`BUCKET_POLICY_ONLY_DISABLED`):** Inexistencia de control de acceso uniforme a nivel de bucket (Riesgo Medio).

---

##  Remediación de Vulnerabilidades

### 1. Despliegue de Acceso Uniforme (`BUCKET_POLICY_ONLY_DISABLED`)

![Modificar acceso al bucket](https://github.com/user-attachments/assets/53de7bd1-0e5f-4bec-93a1-b528d0d812cb)

Cambiamos la configuración del bucket a **Acceso uniforme (Uniform)** con el propósito de activar *Bucket Policy Only*. Para que todos los permisos se manejen de forma centralizada a nivel de bucket mediante políticas de IAM, resolviendo la vulnerabilidad de riesgo medio.

![Confirmación de Acceso Uniforme](https://github.com/user-attachments/assets/b18bd5f6-4ce3-4a43-a7ce-8c5d717aa88f)

---

### 2. Revocación de Acceso Público (`PUBLIC_BUCKET_ACL`)

![Inspección de permisos públicos](https://github.com/user-attachments/assets/a0774dce-5c91-40a3-b77d-e96cbce04640)

Al estar activo el acceso público, cualquier usuario en Internet tenía acceso libre a la información guardada en el bucket.

![Quitar acceso público](https://github.com/user-attachments/assets/ea37ad43-a087-4867-a284-cf17f070013f)

Eliminamos la entidad pública (`allUsers`). De esta manera se corrigen y solucionan definitivamente las vulnerabilidades de nivel alto y medio.

---

##  Extra (Para Experimentar): Habilitación de Registros en Firewall

![Configuración de Firewall Logs 1](https://github.com/user-attachments/assets/1576848c-6087-44bf-9a91-e6867bf78590)

![Configuración de Firewall Logs 2](https://github.com/user-attachments/assets/a6eac9ab-570c-47cb-949e-f9a5374445fe)

Se habilitó el registro de logs en las reglas del Firewall para monitorear el tráfico y analizar eventos de red como ejercicio adicional de hardening e inspección.

---

##  Auditoría y Verificación de Resultados

De esta manera resolvemos las vulnerabilidades planteadas y comprobamos los resultados a través de **Security Command Center (SCC)**. 

Filtrando ahora por el estado **Inactivo**, como se puede observar a diferencia de la imagen inicial (donde solo había 1 vulnerabilidad inactiva), **ahora se muestran 7 hallazgos inactivos (corregidos)**.

![Verificación final en SCC](https://github.com/user-attachments/assets/247fb568-7dff-49e2-8945-f086d874ec7f)
