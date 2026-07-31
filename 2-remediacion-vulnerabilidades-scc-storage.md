# Security Command Center: Cloud Storage Vulnerability Remediation

**Basado en el laboratorio:** *Usa informes para abordar hallazgos (Google Cloud Skill Boost)*

> **Entorno de Laboratorio:** * Google Cloud Platform (GCP)* 
> **Herramientas:** *Google Security Command Center (SCC), Cloud Storage Console*
---
# Objetivos 
- **Identificar vulnerabilidades de seguridad:** Auditar los recursos del entorno utilizando Security Command Center (SCC) para detectar configuraciones erróneas de riesgo alto y medio (como accesos públicos no autorizados y falta de políticas uniformes).

- **Remediar riesgos en Cloud Storage:** Aplicar prácticas de hardening mediante la revocación de accesos públicos (allUsers) y la activación del acceso uniforme a nivel de bucket (Uniform Bucket-Level Access) para prevenir fugas de información.

- **Validar el estado de cumplimiento:** Evaluar la postura de seguridad del proyecto a través del informe del estándar CIS Google Cloud Platform Foundation 2.0, verificando que los hallazgos activos se reduzcan a cero tras aplicar las correcciones.
---
## Revisión de vulnerabilidades
<img width="1257" height="644" alt="image" src="https://github.com/user-attachments/assets/f7e82a24-86fe-4bed-aecd-9be222ec8ff9" />
Al netrara vemos que nos encontramos principalmnete con 3 vulnerabilidades activas

LCA de buckets públicos (PUBLIC_BUCKET_ACL): Esta entrada indica que hay una entrada de Lista de control de acceso (LCA) para el bucket de almacenamiento que es de acceso público, lo que significa que cualquier persona en Internet puede leer los archivos almacenados en el bucket. Esta es una vulnerabilidad de seguridad de alto riesgo que requiere que se priorice su corrección.

Solo política del bucket inhabilitada (BUCKET_POLICY_ONLY_DISABLED): Esta entrada indica que los permisos uniformes a nivel de bucket no están habilitados en un bucket. El acceso uniforme a nivel de bucket permite controlar quién puede acceder a los buckets y objetos de Cloud Storage, lo que simplifica el modo de otorgar acceso a los recursos de Cloud Storage. Esta es una vulnerabilidad de riesgo medio que también se debe corregir.

Registro de buckets inhabilitado (BUCKET_LOGGING_DISABLED): Esta entrada indica que hay un bucket de almacenamiento que no tiene habilitado el registro. Esta es una vulnerabilidad de bajo riesgo que no es necesario corregir en esta situación.

 ## Inspececcion de vulnearabilidades a resolver

 <img width="1280" height="746" alt="image" src="https://github.com/user-attachments/assets/f9290175-df87-4b4e-98a0-be10070d81c0" />
 Del lado izquiedo podemos apreciar 76 activas y 1 inactiva inicialmnete al revisar

LCA de buckets públicos (PUBLIC_BUCKET_ACL): Esta entrada indica que hay una entrada de Lista de control de acceso (LCA) para el bucket de almacenamiento que es de acceso público, lo que significa que cualquier persona en Internet puede leer los archivos almacenados en el bucket. Esta es una vulnerabilidad de seguridad de alto riesgo que requiere que se priorice su corrección.

Solo política del bucket inhabilitada (BUCKET_POLICY_ONLY_DISABLED): Esta entrada indica que los permisos uniformes a nivel de bucket no están habilitados en un bucket. El acceso uniforme a nivel de bucket permite controlar quién puede acceder a los buckets y objetos de Cloud Storage, lo que simplifica el modo de otorgar acceso a los recursos de Cloud Storage. Esta es una vulnerabilidad de riesgo medio que también se debe corregir.

## Remediación

### BUCKET_POLICY_ONLY_DISABLED

<img width="1280" height="491" alt="image" src="https://github.com/user-attachments/assets/53de7bd1-0e5f-4bec-93a1-b528d0d812cb" />

Cambiamos a uniforme con el proposito de que Bucket Policy Only  elc cual  obliga a que todos los permisos se manejen de forma centralizada a nivel de bucket con políticas de IAM resolviendo la vulnerabilidad de riesgo de nivel  medio 

<img width="532" height="391" alt="image" src="https://github.com/user-attachments/assets/b18bd5f6-4ce3-4a43-a7ce-8c5d717aa88f" />


### PUBLIC_BUCKET_ACL

<img width="1177" height="445" alt="image" src="https://github.com/user-attachments/assets/a0774dce-5c91-40a3-b77d-e96cbce04640" />

Al estar cualquier ussuario tenia acceso a la información del bucked 
<img width="1075" height="733" alt="image" src="https://github.com/user-attachments/assets/ea37ad43-a087-4867-a284-cf17f070013f" />

Ahora quitamos el acceso publico de de esta manera solucionamos las vulnerabilidades de nivel medio ,alta 
# Extra para experimentara habilitamos los registros de los Firewall 
<img width="713" height="218" alt="image" src="https://github.com/user-attachments/assets/1576848c-6087-44bf-9a91-e6867bf78590" />

<img width="1240" height="367" alt="image" src="https://github.com/user-attachments/assets/a6eac9ab-570c-47cb-949e-f9a5374445fe" />


## Auditoria
De esta manera resolvemos lás vulnerabilidades planteadas y comprobamos los resultados atraves del scc filtrando ahora en inactivo y comos  euede ver a diferencia de la imagen inicial con solo un vulnerabilidad activa ahora hay 7
<img width="1280" height="498" alt="image" src="https://github.com/user-attachments/assets/247fb568-7dff-49e2-8945-f086d874ec7f" />












 

