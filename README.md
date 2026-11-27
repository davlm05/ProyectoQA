# ProyectoQA

**Escuela:**
Escuela de Ingeniería en Computación

**Curso:**
IC-6831 Aseguramiento de la Calidad del Software

**Profesora:**
Alicia Salazar Hernández

**Estudiantes:** 

* David López Murillo
* Naomi Ilama Gamboa
* Josue Rodriguez Montes

**Fecha:**
30 de noviembre de 2026

# **SELECCIÓN DEL SISTEMA A AUDITAR** 

El sistema seleccionado para desarrollar el proyecto integrado de auditoría y validación es **Restaurante E2**, una aplicación orientada a la gestión de las operaciones de un restaurante mediante una arquitectura basada en servicios y tecnologías de desarrollo web.

El sistema cuenta con una API principal encargada de gestionar las operaciones relacionadas con usuarios, restaurantes, menús, productos, pedidos y reservaciones. Además, incorpora un servicio de búsqueda independiente que utiliza Elasticsearch para facilitar la consulta de información.

Para su funcionamiento, el proyecto integra diferentes tecnologías, entre ellas Node.js, Express, PostgreSQL, MongoDB, Redis, Elasticsearch, Keycloak, Docker y Nginx. También incorpora mecanismos de autenticación y autorización, almacenamiento en caché y herramientas para la documentación de sus servicios mediante Swagger.

La selección de este sistema permite realizar una evaluación integral de la calidad del software, debido a la variedad de funcionalidades, tecnologías y componentes que intervienen en su funcionamiento. Asimismo, su arquitectura permite analizar aspectos relacionados con la comunicación entre servicios, la persistencia de datos, la seguridad y la mantenibilidad del código fuente.

El objetivo de la auditoría consiste en evaluar el nivel de calidad del sistema Restaurante E2, identificando posibles defectos, riesgos y oportunidades de mejora, tomando como referencia el modelo de calidad de producto establecido en la norma ISO/IEC 25010:2023.

Para llevar a cabo esta evaluación se utilizarán técnicas de análisis estático y pruebas dinámicas, incluyendo pruebas funcionales, unitarias, de integración, de sistema, de seguridad y de rendimiento. Los resultados permitirán determinar el grado de cumplimiento de los requisitos establecidos y formular recomendaciones fundamentadas en evidencia.

# **FICHA DE IDENTIFICACIÓN DEL SISTEMA**

A continuación se presentan los datos generales del sistema seleccionado como objeto de la auditoría.

| Dato | Detalle |
| ----- | ----- |
| **Nombre y naturaleza de la aplicación** | **Restaurante E2.** Plataforma distribuida para la gestión de las operaciones de un restaurante (usuarios, restaurantes, menús, productos, reservaciones y pedidos). Su naturaleza es la de una aplicación web de gestión administrativa y transaccional, implementada como un backend de microservicios que expone servicios REST. El sistema no cuenta con una interfaz gráfica propia: el acceso se realiza mediante sus API. |
| **Tecnologías utilizadas** | Node.js 20 y Express 5 como plataforma de ejecución; PostgreSQL 15.6 y MongoDB 7 como motores de persistencia intercambiables; Elasticsearch 8.13 para la búsqueda de texto completo; Redis 7 para el almacenamiento en caché; Keycloak 26.5.5 para la autenticación y autorización mediante tokens JWT; Nginx como balanceador de carga y puerta de entrada; Docker y Docker Compose para la contenerización del entorno; Jest 30 y Supertest 7 para las pruebas automatizadas existentes; Swagger y OpenAPI para la documentación de los servicios; y Ollama (modelo llama3.2) para la generación de datos sintéticos. |
| **Población meta** | Personal administrativo de restaurantes, responsable de mantener la información de establecimientos, menús y productos, y de dar seguimiento a las reservaciones y los pedidos; y personas clientes del restaurante, que consultan el catálogo de productos, realizan búsquedas y gestionan sus propias reservaciones y pedidos.  |
| **URL o forma de acceso al ambiente de pruebas** | El sistema no dispone de un despliegue público. El ambiente de pruebas lo levanta el equipo auditor de manera local mediante contenedores, ejecutando `./scripts/start.ps1` desde el directorio `proyecto/`. Una vez iniciado, el acceso se realiza a través del balanceador Nginx en `http://localhost`, con la documentación interactiva disponible en `http://localhost/api/docs/` y `http://localhost/search/docs/`, y la consola de administración de identidades en `http://localhost:8080`. La interacción con las funcionalidades se efectúa mediante Swagger UI o un cliente de API como Postman. |
| **Curso y equipo de origen del sistema** | Curso de Bases de Datos II a cargo del profesor Kenneth Obando Rodríguez. El sistema fue desarrollado por Gadiel Campos Castillo  y Josué Rodríguez Montes. |
| **Perfiles de persona usuaria disponibles** | El sistema define dos perfiles mediante roles del cliente `restaurant-api` en el realm `restaurant-realm`: el perfil **`admin`**, con permisos para crear, modificar y eliminar restaurantes, menús y productos, además de consultar y eliminar usuarios; y el perfil **`user`**, con permisos para consultar el catálogo y gestionar sus propias reservaciones y pedidos. Se cuenta además con la cuenta administrativa de Keycloak, utilizada para la gestión del realm y la asignación de roles. La configuración exportada del realm no incluye usuarios precargados, por lo que las cuentas de prueba se crean mediante el servicio de registro y sus roles se asignan desde la consola de administración. |
| **Cantidad total de requerimientos funcionales** | **34 requerimientos funcionales.** Se adopta como regla de conteo un requerimiento por cada operación observable que el sistema expone. De ellas, 31 corresponden a la API principal, distribuidas en autenticación (2), usuarios (4), restaurantes (5), menús (5), productos (5), reservaciones (5) y pedidos (5), y 3 al servicio de búsqueda: búsqueda textual de productos, filtrado por categoría y reindexación manual, las cuales están especificadas de forma explícita en el enunciado de origen. Las reglas de negocio asociadas, como la indexación por nombre, categoría y descripción o el uso del valor por defecto cuando un producto carece de descripción, se tratan como criterios de aceptación de su operación correspondiente y no como requerimientos independientes. |
| **Cantidad total de requerimientos no funcionales** | **11 requerimientos no funcionales**, tomados en su mayoría del enunciado de origen del sistema: cobertura de pruebas unitarias de al menos 90 %; replicación con un nodo primario y dos secundarios; particionamiento de los datos de productos y reservas; persistencia garantizada de los datos; intercambiabilidad entre PostgreSQL y MongoDB mediante una variable de configuración, sin condiciones específicas por tecnología en la lógica de negocio; cacheo de respuestas frecuentes con políticas de expiración y limpieza; enrutamiento de `/api/**` y `/search/**` con distribución de carga; escalado horizontal de los servicios de API y de búsqueda; y disponibilidad de un pipeline de integración y entrega continua que ejecute las pruebas, construya la imagen y la publique en el registro de contenedores. A estos se suman la autenticación mediante tokens firmados con autorización basada en roles, derivada del código por corresponder a la etapa previa del sistema, y la documentación técnica del diseño junto con un archivo de instrucciones reproducible. |


# **ALCANCE DE LA AUDITORÍA** 

La auditoría del sistema Restaurante E2 comprenderá la evaluación de sus principales componentes, funcionalidades y mecanismos de comunicación, con el propósito de determinar si el software cumple con los criterios de calidad definidos para el proyecto.

El alcance se establecerá considerando los componentes que forman parte de la solución, así como los riesgos asociados a posibles fallos en su funcionamiento. La evaluación se realizará en un ambiente de pruebas controlado, utilizando datos sintéticos y evitando cualquier afectación a sistemas externos.

## **Elementos incluidos en la auditoría** 

Los siguientes componentes y funcionalidades formarán parte del proceso de evaluación:

| Elemento | Descripción |
| ----- | ----- |
| API principal | Evaluación de los servicios REST desarrollados con Node.js y Express, encargados de gestionar las operaciones principales del sistema. |
| Gestión de usuarios | Verificación de las funcionalidades de registro, autenticación y administración de usuarios. |
| Gestión de restaurantes | Evaluación de las operaciones de creación, consulta, actualización y eliminación de restaurantes. |
| Gestión de menús y productos | Verificación de las operaciones relacionadas con la administración y consulta de menús y productos. |
| Gestión de reservaciones | Evaluación de las operaciones de creación, consulta, actualización y eliminación de reservaciones. |
| Gestión de pedidos | Verificación de las operaciones relacionadas con la creación y administración de pedidos. |
| Servicio de búsqueda | Evaluación del servicio independiente que utiliza Elasticsearch para realizar búsquedas de información. |
| Bases de datos | Evaluación de la persistencia y recuperación de información mediante PostgreSQL y MongoDB. |
| Autenticación y autorización | Verificación de los mecanismos de seguridad implementados mediante Keycloak y la utilización de tokens JWT. |
| Sistema de caché | Evaluación del almacenamiento temporal de información mediante Redis y su interacción con los servicios. |
| Balanceador de carga | Revisión de la configuración e integración de Nginx para la distribución de solicitudes. |
| Documentación de API | Revisión de los servicios y sus operaciones mediante la documentación disponible en Swagger. |

También se evaluará la comunicación entre los diferentes componentes, prestando especial atención a la transferencia de información, la consistencia de los datos y el manejo de errores durante las operaciones.

### **Interfaces y APIs incluidas**

| Elemento | Detalle |
| ----- | ----- |
| Interfaces incluidas | El sistema no cuenta con una interfaz gráfica desarrollada por el equipo de origen. Las interfaces que se incluyen en la auditoría son la documentación interactiva generada a partir de la especificación OpenAPI de ambos servicios, que constituye el medio real de acceso a las operaciones, y el formulario de inicio de sesión del gestor de identidades, considerado únicamente como paso previo para la obtención de credenciales de prueba. |
| APIs incluidas | La API principal, que expone 31 operaciones REST sobre autenticación, usuarios, restaurantes, menús, productos, reservaciones y pedidos; y la API del servicio de búsqueda, que expone 3 operaciones de búsqueda textual, filtrado por categoría y reindexación. Se incluyen además las operaciones de diagnóstico y verificación de estado, utilizadas como instrumento de las pruebas de balanceo y de disponibilidad. |

## **Elementos excluidos de la auditoría** 

Se establecerán las siguientes exclusiones para delimitar adecuadamente el proceso de evaluación:

| Elemento excluido | Justificación |
| ----- | ----- |
| Generador de datos de prueba mediante LLM | Se trata de una utilidad de apoyo que no forma parte del tiempo de ejecución del sistema ni de su lógica de negocio. Su falla no ocasiona pérdida de datos ni afecta a las personas usuarias, pues únicamente impediría poblar el ambiente de pruebas, y se manifestaría de inmediato al ejecutarla. Los datos sintéticos necesarios para la auditoría pueden cargarse mediante las propias operaciones de la API. |
| Reportes de cobertura incluidos en el repositorio | Corresponden a artefactos generados automáticamente y no a código ejecutable, por lo que no presentan comportamiento susceptible de fallar durante la ejecución. Su contenido se utiliza como evidencia de partida, no como objeto de prueba. |
| Servicios externos de terceros | No se evaluará el funcionamiento interno de servicios que no formen parte del desarrollo del sistema. La auditoría se limitará a verificar las interacciones realizadas desde Restaurante E2. |
| Infraestructura física y hardware | No se realizará una evaluación de los equipos físicos, dispositivos de red o servidores externos, debido a que el proyecto se ejecuta mediante un entorno de contenedores Docker. |
| Código fuente interno de tecnologías externas | No se auditará el código interno de plataformas como Keycloak, PostgreSQL, MongoDB, Redis y Elasticsearch. Sin embargo, sí se evaluará su configuración e integración con el sistema. |
| Sistemas independientes de terceros | No se realizarán pruebas directamente sobre aplicaciones o plataformas externas que no estén bajo el control del equipo de desarrollo. |

## **Tipos de pruebas contempladas**

Para evaluar el cumplimiento de los criterios de calidad se realizarán diferentes tipos y niveles de pruebas:

**Pruebas estáticas:** consistirán en la revisión de los requerimientos, el código fuente, la estructura de los componentes, la complejidad y la posible existencia de deuda técnica.

**Pruebas unitarias:** permitirán verificar individualmente las funciones, clases, servicios y controladores que forman parte de la aplicación.

**Pruebas de integración:** tendrán como propósito comprobar la correcta comunicación entre la API, el servicio de búsqueda, las bases de datos y los servicios de infraestructura. Estas pruebas se ejecutarán contra instancias reales de los motores de persistencia y verificarán el efecto de lado de cada operación, es decir, el estado efectivamente almacenado, y no únicamente el contenido de la respuesta.

**Pruebas de sistema:** permitirán evaluar el comportamiento general de la aplicación por medio de su interfaz real y verificar que sus funcionalidades cumplan con los requerimientos establecidos. Se automatizarán de punta a punta sobre el ambiente levantado con contenedores, atravesando el balanceador de carga.

**Pruebas de aceptación:** estarán orientadas a comprobar que el sistema satisface las necesidades de las personas usuarias mediante escenarios de utilización.

**Pruebas de rendimiento y carga:** permitirán analizar los tiempos de respuesta, el comportamiento ante solicitudes concurrentes y la utilización de recursos.

**Pruebas de seguridad:** se enfocarán en verificar los mecanismos de autenticación, autorización, control de acceso y protección de la información.

**Pruebas de usabilidad y accesibilidad:** permitirán evaluar las interfaces disponibles y su facilidad de utilización, considerando los criterios de accesibilidad establecidos en WCAG 2.2 nivel AA cuando corresponda.

Todas las pruebas de seguridad y rendimiento se ejecutarán exclusivamente sobre el sistema propio del equipo y en un ambiente de pruebas controlado.

# **MARCO DE CALIDAD: ISO/IEC 25010:2023**

Para realizar la evaluación de la calidad del sistema Restaurante E2 se utilizará como referencia el modelo de calidad de producto establecido en la norma ISO/IEC 25010:2023.

Esta norma proporciona un marco de referencia para evaluar diferentes características de calidad de los productos de software. Su aplicación permite analizar el sistema desde distintas perspectivas, considerando aspectos relacionados con el cumplimiento de sus funcionalidades, el rendimiento, la seguridad, la confiabilidad y la facilidad de mantenimiento.

La edición 2023 establece nueve características de calidad que permiten realizar una evaluación estructurada del producto. A partir de estas características se seleccionarán aquellas que tengan mayor relevancia para el sistema Restaurante E2, considerando su arquitectura, funcionalidades y posibles riesgos.

## **Características del modelo de calidad** 

Las nueve características establecidas en la norma ISO/IEC 25010:2023 son las siguientes:

| N.º | Característica | Descripción |
| ----- | ----- | ----- |
| 1 | Adecuación funcional | Evalúa si el sistema proporciona las funciones necesarias para satisfacer los requisitos establecidos. |
| 2 | Eficiencia de desempeño | Evalúa el comportamiento temporal, la capacidad y la utilización de recursos del sistema. |
| 3 | Compatibilidad | Evalúa la capacidad del sistema para coexistir e intercambiar información con otros componentes. |
| 4 | Capacidad de interacción | Evalúa la facilidad con la que las personas usuarias pueden reconocer, aprender y utilizar el sistema. |
| 5 | Fiabilidad | Evalúa la capacidad del sistema para mantener su funcionamiento y recuperarse ante posibles fallos. |
| 6 | Seguridad | Evalúa la protección de la información mediante mecanismos de confidencialidad, integridad, autenticidad y resistencia. |
| 7 | Mantenibilidad | Evalúa la facilidad para analizar, modificar y mantener el código fuente del sistema. |
| 8 | Flexibilidad | Evalúa la capacidad del sistema para adaptarse, escalarse, instalarse y ser probado. |
| 9 | Protección (Safety) | Evalúa la capacidad del sistema para evitar situaciones que puedan ocasionar daños a personas, bienes o datos. |

## **Características de calidad seleccionadas** 

Para la auditoría del sistema Restaurante E2 se seleccionan cinco de las nueve características establecidas en ISO/IEC 25010:2023.

La selección considera las características técnicas del sistema, su arquitectura basada en servicios, la integración de diferentes tecnologías y los riesgos asociados a su funcionamiento.

### **Prioridad de las características seleccionadas**

La prioridad se establece según el daño que produciría una falla y según a quién afectaría.

| Prioridad | Característica | Justificación de la prioridad |
| ----- | ----- | ----- |
| 1 | Seguridad | Una falla en el control de acceso permitiría consultar o modificar información de otras personas usuarias, además de alterar el catálogo y los pedidos. Es el único caso en que el daño recae directamente sobre terceros. El sistema implementa su propia validación de tokens en lugar de utilizar el mecanismo nativo de la biblioteca de autenticación, lo que incrementa la probabilidad de defecto. |
| 2 | Adecuación funcional | Si las operaciones de pedidos y reservaciones no producen los resultados esperados, el sistema pierde su propósito. Constituye además la base de la matriz de trazabilidad, dado que todos los casos de prueba se originan en los requerimientos. |
| 3 | Flexibilidad | La intercambiabilidad entre PostgreSQL y MongoDB es la promesa principal del sistema, y los dos modelos de datos no son equivalentes entre sí, pues los productos se almacenan en una tabla independiente en el modelo relacional y de forma embebida en el modelo de documentos. El riesgo de que la equivalencia funcional se rompa al cambiar de motor es concreto y verificable. |
| 4 | Eficiencia de desempeño | Una degradación en los tiempos de respuesta no ocasiona pérdida de información, pero invalidaría la justificación de los componentes de caché, balanceo y búsqueda incorporados a la arquitectura. |
| 5 | Mantenibilidad | Sus efectos recaen sobre el equipo de desarrollo y no sobre las personas usuarias, y se manifiestan de forma diferida. Se mantiene entre las características evaluadas por ser obligatoria y por constituir la base del análisis de deuda técnica. |

### **- Adecuación funcional** 

Esta característica se selecciona porque el sistema Restaurante E2 debe garantizar que las operaciones relacionadas con usuarios, restaurantes, menús, productos, pedidos y reservaciones cumplan correctamente con los requisitos establecidos.

Se evaluará que las funcionalidades estén implementadas de manera correcta, que los resultados obtenidos sean los esperados y que las operaciones respondan adecuadamente ante diferentes escenarios de utilización.

* Subcaracterísticas: completitud funcional, corrección funcional y pertinencia funcional.  
* Métodos de evaluación: pruebas funcionales, unitarias, de integración y de sistema.  
* Métrica propuesta: porcentaje de requerimientos funcionales que cuentan con al menos un caso de prueba aprobado.

### **- Eficiencia de desempeño**

Se selecciona esta característica debido a que el sistema integra diferentes servicios y tecnologías que deben responder adecuadamente ante las solicitudes realizadas por las personas usuarias.

La utilización de Redis para almacenamiento en caché, Nginx para el balanceo de carga y Elasticsearch para las búsquedas hace necesario evaluar el comportamiento del sistema bajo diferentes niveles de utilización.

* Subcaracterísticas: comportamiento temporal, capacidad y utilización de recursos.  
* Métodos de evaluación: pruebas de rendimiento y carga.  
* Métrica propuesta: tiempo de respuesta en el percentil 95 (P95) bajo una cantidad determinada de solicitudes concurrentes.

### **- Seguridad**

Esta característica es obligatoria y se considera fundamental debido a que el sistema administra información relacionada con usuarios, restaurantes, pedidos y reservaciones.

Además, incorpora Keycloak como mecanismo de gestión de identidad y autenticación, junto con mecanismos de autorización y utilización de tokens JWT.

Se evaluará que únicamente las personas usuarias autorizadas puedan acceder a los recursos correspondientes y que los mecanismos de protección funcionen correctamente.

* Subcaracterísticas: confidencialidad, integridad, no repudio, responsabilidad, autenticidad y resistencia.  
* Métodos de evaluación: pruebas de autenticación, autorización, control de acceso y verificación de vulnerabilidades.  
* Métrica propuesta: porcentaje de controles de acceso verificados que funcionan correctamente.

### **- Mantenibilidad** 

Esta característica es obligatoria y se selecciona porque el sistema cuenta con una estructura basada en servicios y utiliza diferentes componentes para separar las responsabilidades de la aplicación.

También incorpora el patrón DAO (Data Access Object), que permite abstraer las operaciones de acceso a los datos y trabajar con diferentes motores de bases de datos.

Se evaluará la organización del código, la modularidad, la reutilización de componentes y la facilidad para identificar y corregir posibles defectos.

* Subcaracterísticas: modularidad, reusabilidad, analizabilidad y modificabilidad.  
* Métodos de evaluación: análisis estático del código, revisión de arquitectura y evaluación de deuda técnica.  
* Métrica propuesta: complejidad ciclomática promedio de los componentes evaluados.

### **- Flexibilidad** 

Se selecciona esta característica porque el sistema utiliza una arquitectura basada en contenedores y permite trabajar con diferentes tecnologías de almacenamiento de datos.

La utilización de Docker Compose facilita la configuración del entorno de ejecución, mientras que la estructura de acceso a datos permite evaluar la adaptación del sistema ante cambios en el motor de persistencia.

Se evaluará la capacidad del sistema para adaptarse a diferentes configuraciones, facilitar su instalación y responder ante cambios en las condiciones de ejecución.

* Subcaracterísticas: capacidad de ser probado, adaptabilidad, escalabilidad, instalabilidad y reemplazabilidad.  
* Métodos de evaluación: pruebas de instalación, adaptación y escalabilidad.  
* Métrica propuesta: porcentaje de operaciones que mantienen su funcionamiento correcto al cambiar el motor de base de datos.

## **Características no seleccionadas y justificación** 

**Compatibilidad:** la coexistencia no aplica, porque el sistema se ejecuta en un entorno de contenedores aislado, sin compartir recursos con software independiente ni integrarse con sistemas externos. La interoperabilidad se da entre componentes del propio sistema y su evidencia se obtiene igualmente mediante las pruebas de integración de la adecuación funcional y la verificación del cambio de motor de base de datos de la flexibilidad, de modo que evaluarla como eje independiente duplicaría evidencia sin aportar hallazgos nuevos.

**Fiabilidad:** el sistema no se encuentra en operación continua, no posee acuerdos de nivel de servicio ni personas usuarias que dependan de su disponibilidad, y sus datos de prueba son sintéticos y regenerables, por lo que una falla no ocasiona pérdida irrecuperable de información. Sus mecanismos más representativos, como las políticas de reinicio automático, la verificación del estado de los componentes y la redistribución de solicitudes ante la caída de una réplica, se ejercitan de forma indirecta en las pruebas de escalabilidad e instalación de la flexibilidad.

**Capacidad de interacción:** el sistema no cuenta con una interfaz gráfica propia, pues se trata de un backend que expone únicamente servicios REST. Las únicas pantallas disponibles son la documentación generada automáticamente a partir de la especificación de los servicios y el formulario de inicio de sesión del gestor de identidades, ninguna de las cuales fue desarrollada por el equipo de origen, por lo que los hallazgos de una evaluación de esta característica no serían atribuibles al software auditado.

**Protección (Safety):** el sistema está orientado a la gestión administrativa de restaurantes y, según la documentación disponible, no presenta funcionalidades críticas relacionadas directamente con la seguridad física de las personas o los bienes. Por este motivo, no se considera prioritaria para esta auditoría.

## **Relación entre las características de calidad y las pruebas** 

Para garantizar que la evaluación proporcione evidencia suficiente sobre la calidad del sistema, cada característica seleccionada se relacionará con diferentes tipos de pruebas y métricas.

| Característica | Pruebas principales | Evidencia esperada |
| ----- | ----- | ----- |
| Adecuación funcional | Pruebas funcionales, unitarias, de integración y de sistema. | Casos de prueba aprobados, fallidos y requerimientos cubiertos. |
| Eficiencia de desempeño | Pruebas de rendimiento y carga. | Tiempos de respuesta, concurrencia y consumo de recursos. |
| Seguridad | Pruebas de seguridad. | Hallazgos de vulnerabilidades y controles de acceso verificados. |
| Mantenibilidad | Pruebas estáticas y análisis de código. | Complejidad ciclomática, duplicación y deuda técnica. |
| Flexibilidad | Pruebas de instalación, adaptabilidad y escalabilidad. | Resultados de instalación, cambios de configuración y comportamiento ante el escalado. |

## **Aplicación del enfoque GQM para la definición de métricas** 

Para establecer las métricas de calidad se utilizará el enfoque GQM (Goal, Question, Metric), el cual permite relacionar los objetivos de evaluación con preguntas concretas y métricas cuantificables.

Este enfoque permitirá definir qué se desea evaluar, cuáles preguntas deben responderse para alcanzar los objetivos y qué métricas permitirán obtener evidencia sobre el comportamiento del sistema.

Cada objetivo se redacta con la plantilla de Basili, la cual establece el objeto de estudio, el propósito, el foco de calidad, la perspectiva y el contexto. De cada objetivo se derivan entre dos y cuatro preguntas, y de cada pregunta se derivan las métricas que permiten responderla.

### **- Adecuación funcional**

**Objetivo:** analizar las operaciones expuestas por la API principal y el servicio de búsqueda, con el propósito de evaluar su conformidad, con respecto a la completitud y la corrección funcional, desde el punto de vista del equipo auditor, en el contexto del ambiente de pruebas local del sistema Restaurante E2.

| Preguntas | Métricas que las responden |
| ----- | ----- |
| ¿Qué proporción de los requerimientos funcionales cuenta con al menos un caso de prueba aprobado? | Porcentaje de requerimientos funcionales con al menos un caso de prueba aprobado. |
| ¿Las operaciones responden correctamente ante datos inválidos y valores de frontera? | Porcentaje de casos negativos y de frontera aprobados respecto del total ejecutado. |
| ¿Las respuestas corresponden a lo declarado en la documentación de los servicios? | Porcentaje de respuestas conformes al esquema publicado en la especificación OpenAPI. |

### **- Eficiencia de desempeño**

**Objetivo:** analizar los componentes de caché, balanceo de carga y búsqueda de texto completo, con el propósito de evaluar su comportamiento temporal, con respecto al tiempo de respuesta y la capacidad bajo concurrencia, desde el punto de vista de la persona usuaria final, en el contexto del ambiente de pruebas local con carga sintética creciente.

| Preguntas | Métricas que las responden |
| ----- | ----- |
| ¿Cuánto tarda el sistema en responder bajo una carga concurrente determinada? | Tiempo de respuesta en el percentil 95 con N solicitudes concurrentes. |
| ¿Cómo se degrada el desempeño al duplicar la carga? | Variación porcentual del percentil 95 al duplicar la cantidad de solicitudes concurrentes. |
| ¿Qué mejora aporta el almacenamiento en caché? | Diferencia de latencia media entre las respuestas con acierto de caché y las respuestas con fallo de caché. |

### **- Seguridad**

**Objetivo:** analizar los mecanismos de autenticación y autorización, con el propósito de evaluar su efectividad, con respecto a la confidencialidad y al control de acceso a los recursos, desde el punto de vista de la persona propietaria de los datos, en el contexto del ambiente de pruebas local y con datos sintéticos.

| Preguntas | Métricas que las responden |
| ----- | ----- |
| ¿Se impide el acceso a los recursos cuando no se presenta un token válido? | Porcentaje de operaciones protegidas que rechazan correctamente las solicitudes sin token válido. |
| ¿Se restringen las operaciones según el rol de la persona usuaria? | Porcentaje de controles de acceso por rol verificados que funcionan correctamente. |
| ¿Resiste el sistema entradas maliciosas en los parámetros de las operaciones? | Cantidad de hallazgos de seguridad clasificados por severidad. |

### **- Mantenibilidad**

**Objetivo:** analizar el código fuente de los dos microservicios, con el propósito de evaluar su calidad interna, con respecto a la modularidad, la complejidad y la deuda técnica, desde el punto de vista del equipo de desarrollo que daría mantenimiento al sistema, en el contexto del repositorio entregado.

| Preguntas | Métricas que las responden |
| ----- | ----- |
| ¿Presenta el código una complejidad que dificulte su modificación? | Complejidad ciclomática promedio y máxima por función. |
| ¿Existe código duplicado o código muerto? | Porcentaje de líneas duplicadas sobre el total de líneas analizadas. |
| ¿Cuánta deuda técnica acumula el sistema? | Razón de deuda técnica, expresada como la relación entre el esfuerzo de corrección estimado y el esfuerzo de desarrollo. |

### **- Flexibilidad**

**Objetivo:** analizar la configuración de despliegue y la capa de acceso a datos, con el propósito de evaluar su adaptabilidad, con respecto a la equivalencia funcional entre motores de base de datos y a la escalabilidad horizontal, desde el punto de vista del equipo que operaría el sistema, en el contexto del entorno de contenedores levantado localmente.

| Preguntas | Métricas que las responden |
| ----- | ----- |
| ¿Se mantiene el comportamiento funcional al cambiar el motor de base de datos? | Porcentaje de casos funcionales que producen un resultado idéntico con PostgreSQL y con MongoDB. |
| ¿Puede instalarse el sistema desde cero sin intervención manual? | Tiempo de instalación desde cero hasta que los servicios responden correctamente, y cantidad de pasos manuales requeridos. |
| ¿Cómo responde el sistema al aumentar la cantidad de réplicas? | Variación del percentil 95 del tiempo de respuesta al duplicar la cantidad de réplicas de los servicios. |

# **HERRAMIENTAS**

| Propósito | Herramienta seleccionada | Modo | Justificación de la selección |
| ----- | ----- | ----- | ----- |
| Análisis estático y deuda técnica | SonarQube Community, en contenedor local | Automatizada | Es la única opción de la lista que entrega en un mismo reporte las tres métricas comprometidas en el objetivo de mantenibilidad: complejidad ciclomática, porcentaje de líneas duplicadas y razón de deuda técnica.  |
| Pruebas unitarias | Jest | Automatizada | Ya está instalado en ambos microservicios, con 41 archivos de prueba existentes y el umbral de cobertura configurado. Adoptarlo tiene costo cero y permite reutilizar los dobles de prueba ya construidos por el equipo de origen. |
| Pruebas de integración | Testcontainers para Node.js | Automatizada | Levanta instancias reales y desechables de PostgreSQL y MongoDB desde el propio Jest, lo que permite afirmar sobre el estado efectivamente persistido, es decir el efecto de lado que exige el enunciado, sin depender de que el entorno se haya levantado de antemano.  |
| Pruebas de sistema de punta a punta | Playwright, en modo de peticiones HTTP | Automatizada | El sistema no posee interfaz gráfica, de modo que las pruebas de punta a punta se ejecutan por HTTP contra el balanceador, atravesando Nginx, el microservicio y la base de datos.  |
| Pruebas de API | Postman con Newman | Automatizada | Postman permite diseñar y documentar los casos funcionales de camino feliz, negativos y de frontera en un formato legible para todo el equipo, y Newman los ejecuta por consola con reporte, de manera que una misma colección sirve como documentación y como suite repetible.  |
| Rendimiento y carga | k6 | Automatizada | Permite definir la carga creciente por etapas y, sobre todo, declarar los umbrales dentro del propio script, que es exactamente la condición de fijar el umbral de antemano. Entrega el percentil 95 de forma nativa, que es la métrica del objetivo de eficiencia, y sus scripts se escriben en el mismo lenguaje del sistema. |
| Seguridad | OWASP ZAP en modo pasivo, en contenedor | Automatizada y manual | Se ejecuta como contenedor contra el ambiente propio y produce un reporte de hallazgos clasificados por severidad, que es una de las métricas declaradas. Un escáner pasivo no puede resolver las verificaciones de control de acceso por rol, pues requieren tokens de cada perfil; esas se automatizan en la colección de Postman y se complementan con revisión manual guiada por OWASP Top 10. |
| Usabilidad y accesibilidad | Lighthouse | Automatizada y manual | Está integrado en el navegador y dispone de interfaz de línea de comandos, por lo que no requiere instalación ni cuenta externa, y genera un reporte de incumplimientos por pantalla. La prueba con personas usuarias es manual por definición. |
| Cobertura de código | Istanbul | Automatizada | Es el motor de cobertura que Jest utiliza al ejecutarse con la opción correspondiente, y genera los reportes en los formatos que el proyecto ya tiene versionados, por lo que no requiere incorporación alguna. |




