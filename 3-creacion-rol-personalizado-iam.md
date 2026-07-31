# Creación y Gestión de Roles Personalizados en Google Cloud IAM

**Basado en el laboratorio:** *Usa informes para abordar hallazgos (Google Cloud Skills Boost)*

> **Entorno de Laboratorio:** Google Cloud Platform (GCP)
> **Herramientas:** Google Cloud Console, IAM & Admin Console, Policy Analyzer
---

##  Objetivos del Laboratorio

Al completar este laboratorio práctico, el analista habrá adquirido experiencia práctica en la gestión de accesos de grano fino, logrando:

- **Comprender la jerarquía de roles de IAM:** Diferenciar entre roles básicos, predefinidos y personalizados en Google Cloud.

- **Aplicar el Principio de Privilegio Mínimo:** Diseñar y aprovisionar un **Rol Personalizado** (`Audit Team Reviewer`) con permisos estrictamente restringidos (solo lectura para Firebase Realtime Database) para satisfacer un requisito de auditoría externa.
- **Gestionar la asignación de roles:** Otorgar el rol personalizado creado a una principal (usuario) específica dentro del proyecto.

- **Ejecutar auditoría de políticas:** Utilizar la herramienta **Analizador de Políticas** de Google Cloud para verificar técnicamente que el rol y los permisos correctos fueron otorgados a la identidad indicada.
---
## Arquitectura
<img width="1098" height="220" alt="image" src="https://github.com/user-attachments/assets/75682409-d3cb-4f03-aaa4-dd6c53af29b3" />

## Tipos de roles

Roles básicos: Son los roles que siempre estuvieron disponibles en la consola de Google Cloud. Estos roles son propietario, editor y visualizador.

Roles predefinidos: Son aquellos que brindan un control de acceso más detallado que los roles básicos. Por ejemplo, el rol predefinido de publicador de Pub/Sub (roles/pubsub.publisher) proporciona acceso solo para publicar mensajes en un tema de Pub/Sub.

Roles personalizados: Son los que creas para adaptar los permisos a las necesidades de tu organización cuando los roles predefinidos no las satisfacen.

## Etapas de lanzamiento de Roles

Alfa: Los roles en la etapa alfa suelen ser experimentales y pueden sufrir cambios significativos. No se recomiendan para entornos de producción. Los usuarios pueden enviar comentarios sobre los roles alfa para influir en su desarrollo.

Beta: Los roles en la etapa beta están más desarrollados que los roles alfa, pero aún podrían recibir actualizaciones y mejoras según los comentarios de los usuarios. Se los considera adecuados para determinadas situaciones ajenas a la producción, pero es posible que no sean totalmente estables.

Disponibilidad general (DG): Los roles que alcanzaron la disponibilidad general atravesaron etapas exhaustivas de desarrollo, prueba y refinamiento. Se los considera estables, confiables y adecuados para un uso más general en los entornos de producción. Los roles en DG se revisaron ampliamente y están diseñados para brindar un comportamiento coherente y confiable.

## Rol personalizado

<img width="1323" height="321" alt="image" src="https://github.com/user-attachments/assets/4b205ea8-a1c0-47da-9516-823a9016d8cd" />

Con las carcateristicas estalblecidas por el laboratorio se poreceden a configurar
<img width="1184" height="634" alt="image" src="https://github.com/user-attachments/assets/8755d33d-817e-4b60-9141-69c9e1e96cd6" />

U rol es lo que dice: "Aquí tienes un grupo de cosas (permisos) que puedes hacer,
pero si nesecitas cosas mas especificas y menos generales se utilizan los permisos 
El permiso es lo que dice: "Puedes hacer esto



## Rol  a filtrar
<img width="799" height="831" alt="image" src="https://github.com/user-attachments/assets/f7c997c6-bfb4-48e9-b977-929ca7930a96" />

## Permisos especificos
Ahora añadimos permisos filtrandolos por un  rol para tener permisos especificos 
<img width="1128" height="902" alt="image" src="https://github.com/user-attachments/assets/ec5ff0ed-cdb6-4731-ab1c-31d4d3724b34" />



## Analogía
Permiso: Es una sola llave específica que abre una cerradura muy concreta (ej. la llave de la puerta del servidor #3).

Rol: Es un llavero el que tiene muchas  llaves que alguien necesita para un trabajo. Al llavero le pones una etiqueta que dice "Técnico de Servidores".

Usuario: Es la persona.

Recurso/Ámbito (El "solo en esto"): Es el edificio en este caso el proyecto
## Asignación de rol
Primero nos movimos de roles a a IAM y otrogamos acceso a un estudiante de pruebas
<img width="800" height="837" alt="image" src="https://github.com/user-attachments/assets/74bb1e93-fc46-4635-abb5-908e83d1ceb3" />

## Visualizacion  de accignacion de rol
<img width="1231" height="823" alt="image" src="https://github.com/user-attachments/assets/01fad82e-501e-44d3-9960-37f411a17bbd" />

## Comprobación de la asignación correcta del rol
Al hacer una consulta peronalizada en ela nalizador d epoliticas introduccineo el proyecto donde de asigno el rol , el usuario
<img width="1280" height="877" alt="image" src="https://github.com/user-attachments/assets/840ae65e-be0c-40eb-8f12-ce23285eeb08" />
 en este caso ya que se le asigno a un usuario no a un grupo d erecursos además  y no queremos la lista de usuarios de un recurso que e slo que hace la opccion 2 y no queremos saber ahora los perisoso atomicos ya que los aisgnamos nosotros que es lo que hace la opccion 3   solo marcamos la primera opccion ya que es para que nos  muestre el recurso exacto del proyecto al que tiene alcance el usuario

<img width="1280" height="854" alt="image" src="https://github.com/user-attachments/assets/deec5a8f-9504-440a-86c2-793f6df13fd2" />

Al hacer la consulta optenmeos la comprobacion vusual  aquitectura mostrada al inicio d e este archivo y los resultado adecuados comos e ve en la imagen siguinete
<img width="1280" height="642" alt="image" src="https://github.com/user-attachments/assets/75dc52c4-3151-4ac4-a179-9d21ade87160" />








