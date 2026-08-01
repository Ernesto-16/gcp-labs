#  Creación y Gestión de Roles Personalizados en Google Cloud IAM

**Basado en el laboratorio:** *Crea un rol en Google Cloud IAM (Google Cloud Skills Boost)*

> **Entorno de Laboratorio:** Google Cloud Platform (GCP)  
> **Herramientas:** Google Cloud Console, IAM & Admin Console, Policy Analyzer  

---

##  Objetivos del Laboratorio

Al completar este laboratorio práctico, se habrá adquirido experiencia práctica en la gestión de accesos de grano fino, logrando:

- **Comprender la jerarquía de roles de IAM:** Diferenciar entre roles básicos, predefinidos y personalizados en Google Cloud.
- **Aplicar el Principio de Privilegio Mínimo:** Diseñar y aprovisionar un **Rol Personalizado** (`Audit Team Reviewer`) con permisos estrictamente restringidos (solo lectura para Firebase Realtime Database) para satisfacer un requisito de auditoría externa.
- **Gestionar la asignación de roles:** Otorgar el rol personalizado creado a una identidad (usuario) específica dentro del proyecto.
- **Ejecutar auditoría de políticas:** Utilizar la herramienta **Analizador de Políticas** de Google Cloud para verificar técnicamente que el rol y los permisos correctos fueron otorgados a la identidad indicada.

---

##  Arquitectura de la Solución

<img width="1098" height="220" alt="image" src="https://github.com/user-attachments/assets/75682409-d3cb-4f03-aaa4-dd6c53af29b3" />

---

##  Conceptos de IAM

### Tipos de roles
*   **Roles básicos:** Son los roles heredados que siempre han estado disponibles en la consola de Google Cloud. Estos roles son propietario (Owner), editor (Editor) y visualizador (Viewer).
*   **Roles predefinidos:** Brindan un control de acceso más detallado que los roles básicos. Por ejemplo, el rol predefinido de *Publicador de Pub/Sub* (`roles/pubsub.publisher`) proporciona acceso exclusivamente para publicar mensajes en un tema de Pub/Sub.
*   **Roles personalizados:** Son los que se crean a medida para adaptar los permisos exactos a las necesidades de tu organización cuando los roles predefinidos no son suficientes.

### Etapas de lanzamiento de Roles
*   **Alfa:** Los roles en esta etapa suelen ser experimentales y pueden sufrir cambios significativos. No se recomiendan para entornos de producción.
*   **Beta:** Están más desarrollados que los alfa, pero aún podrían recibir actualizaciones. Son adecuados para situaciones fuera de producción, pero pueden no ser totalmente estables.
*   **Disponibilidad General (DG / GA):** Atravesaron etapas exhaustivas de desarrollo, prueba y refinamiento. Son estables, confiables y adecuados para el uso en entornos de producción.

---

##  Diferencia entre Roles y Permisos (Analogía)

De manera sencilla para entender mejor los permisos y roles:

*   **Permiso:** Es una sola llave específica que abre una cerradura muy concreta (ej. la llave de la puerta de un recurso ). Es aquella que dice : *"Puedes hacer esto"*.
*   **Rol:** Es un llavero que contiene  muchas llaves   que alguien necesita para hacer su trabajo. Este tiene una etiqueta que agrupa los permisos: *"Dando un grupo de cosas que puedes hacer"*.
*   **Usuario (Identidad):** Es la persona que tiene el rol.
*   **Recurso/Ámbito:** Es el edificio (en este caso, el Proyecto) donde las llaves funcionan.

---

## 🛠️ Creación del Rol Personalizado

![Creación de rol personalizado](https://github.com/user-attachments/assets/4b205ea8-a1c0-47da-9516-823a9016d8cd)

Con las características establecidas por los requisitos del laboratorio, podemos a configurar el nuevo rol personalizado.

![Configuración del rol](https://github.com/user-attachments/assets/8755d33d-817e-4b60-9141-69c9e1e96cd6)

### Selección de Permisos Específicos
En lugar de dar un rol general, ponemos permisos filtrándolos por un rol que ya existe para obtener permisos atómicos y específicos, cumpliendo con el principio de privilegio mínimo.

![Filtrado de permisos](https://github.com/user-attachments/assets/f7c997c6-bfb4-48e9-b977-929ca7930a96)

![Permisos agregados](https://github.com/user-attachments/assets/ec5ff0ed-cdb6-4731-ab1c-31d4d3724b34)

---

##  Asignación del Rol a un Usuario

Una vez creado el rol, nos movemos al apartado de **IAM** para otorgar el acceso a un estudiante de pruebas.

![Otorgar acceso IAM](https://github.com/user-attachments/assets/74bb1e93-fc46-4635-abb5-908e83d1ceb3)

### Visualización de la Asignación
Confirmamos que el usuario tiene ahora asignado el rol personalizado correctamente dentro de la lista de identidades del proyecto.

![Asignación exitosa](https://github.com/user-attachments/assets/01fad82e-501e-44d3-9960-37f411a17bbd)

---

##  Auditoría: Comprobación con Policy Analyzer

Para validar el acceso, hacemos una consulta personalizada en el **Analizador de Políticas (Policy Analyzer)**, introduciendo el proyecto y el usuario al que se le asignó el rol.

![Consulta en Policy Analyzer](https://github.com/user-attachments/assets/840ae65e-be0c-40eb-8f12-ce23285eeb08)

**Configuración de la consulta:**
En este caso, marcamos únicamente la **primera opción** para que nos muestre el recurso exacto del proyecto al que tiene alcance el usuario.
*   *¿Por qué no las otras opciones?* No elegimos la opción 2 porque el rol se asignó a un usuario y no a un grupo de recursos si lo seleccionamos nos mostrará la lista de usuarios de un recurso , no elegimos la opción 3 (permisos atómicos) porque nosotros mismos acabamos de configurar esos permisos .

![Configuración de opciones de análisis](https://github.com/user-attachments/assets/deec5a8f-9504-440a-86c2-793f6df13fd2)

### Resultados de la Auditoría
Al ejecutar la consulta, obtenemos la comprobación visual de la arquitectura planteada al inicio del documento. Los resultados confirman de manera técnica y gráfica que el usuario tiene el alcance y los permisos esperados sobre el recurso.

![Resultado de Policy Analyzer](https://github.com/user-attachments/assets/75dc52c4-3151-4ac4-a179-9d21ade87160)
