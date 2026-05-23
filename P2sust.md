Nueva justificación para la Pregunta 2 (Database per Service)
El caso dice: "Debe aplicar microservices architecture para el backend, considerando un microservice por cada bounded context" (página 5).

Justificación de Database per Service:

Encapsulamiento de DDD: Cada Bounded Context tiene su propio modelo de dominio. Si compartieran la misma base de datos, se rompería el encapsulamiento porque un BC podría acceder directamente a las tablas de otro.

Independencia evolutiva: Cada microservicio puede cambiar su esquema de base de datos sin afectar a los demás.

Escalamiento independiente: analyticsDatabase puede ser optimizada para OLAP (columnstore), mientras que billingDatabase se optimiza para OLTP (transacciones).

Tolerancia a fallos: Si workflowDatabase falla, billingService sigue funcionando.

Seguridad: Cada servicio solo tiene credenciales para su propia base de datos.

Consistencia con la Pregunta 3: El enunciado pide un "Aggregate de dicho bounded context". Un Aggregate es un conjunto de objetos de dominio con una raíz que garantiza consistencia transaccional. Esto solo tiene sentido si el BC tiene su propia base de datos.

# Sustentación de Decisiones de Diseño - EduFlow Digital Corp.

## 1. Tabla de Sustentación: Punto por Punto

| # | Decisión de Diseño | ¿Dónde se especifica en el caso? | Justificación |
|---|-------------------|----------------------------------|----------------|
| 1 | **Arquitectura de microservicios** (1 servicio por Bounded Context) | Página 5: *"debe aplicarse microservices architecture para el backend, considerando un microservice por cada bounded context"* | Permite despliegue independiente, escalabilidad granular y equipos autónomos. Cada BC puede evolucionar sin afectar a los demás. |
| 2 | **Database per Service** (una BD por cada microservicio) | No está explícito en el caso. Es una decisión de diseño derivada de DDD. | Cada Bounded Context tiene su propio modelo de dominio. Compartir BD rompería el encapsulamiento. Además, la Pregunta 3 (Aggregate) solo tiene sentido si el BC tiene su propia BD. |
| 3 | **API Gateway como punto único de entrada** | Página 5: *"Debe utilizar un API Gateway para canalizar los requests provenientes de aplicaciones web, móviles y plataformas No-Code/Low-Code"* | Centraliza autenticación, rate limiting, routing y transformación de protocolos. Los 3 frontends (Web, Mobile, Power Apps) consumen APIs a través de él. |
| 4 | **Frontends separados** (Angular Web, Mobile App, Power Apps Portal) | Página 4: *"experiencias web sean implementadas usando Angular"* y mención de *"Mobile Business Services"* como unidad futura | Diferentes usuarios tienen diferentes necesidades. El Administrador TI usa Web para gestión compleja. El Profesional Independiente usa Mobile para acceso remoto. El Citizen Developer usa Power Apps para crear aplicaciones No-Code. |
| 5 | **Comunicación asíncrona con Azure Event Grid** | Página 5: *"Event-Driven Architecture (EDA)"* y mención de *"Azure Event Grid"* como tecnología sugerida | Desacopla los microservicios. Workflow Management publica eventos que Notification Services consume. Permite agregar nuevos consumidores sin modificar los publicadores. |
| 6 | **Uso de Serverless (Azure Functions y Logic Apps)** | Página 4: *"Serverless Computing"*; Página 5: *"Azure Functions, Azure Logic Apps"* | Escalamiento automático sin gestión de servidores. Pago por uso, ideal para SaaS con cargas variables. Azure Functions para BCs transaccionales (Billing, IAM); Logic Apps para orquestación (Workflow, Low-Code Integration). |
| 7 | **Uso de Azure Blob Storage para archivos** | Página 4: *"File Storage"* como constraint de Azure | Separar almacenamiento de archivos (documentos, assets de apps No-Code) de la BD relacional mejora rendimiento y reduce costos. |
| 8 | **Event Storming como método de modelado** | Página 4: *"En su sesión de EventStorming, llegan a los siguientes bounded contexts"* | El caso explícitamente usó EventStorming para identificar los BCs. Esto alinea el diseño con DDD y con el proceso real de la empresa. |
| 9 | **12 Bounded Contexts identificados** | Páginas 4-5: lista explícita de bounded contexts | El caso ya realizó el EventStorming y entregó los BCs. No hay ambigüedad: se toman tal cual de la especificación. |
| 10 | **Tecnologías base: Azure + Power Platform + Angular** | Página 4: *"constraint que las propuestas deben estar basadas en servicios cloud de Microsoft Azure"* y lista de tecnologías del equipo | El caso es explícito: alianza con Microsoft Azure, equipo con experiencia en Power Platform y Angular. Cumplir constraints es obligatorio. |

---

## 2. Guía de Criterios para Futuros Casos

Esta tabla te ayudará a identificar **qué buscar** en un enunciado para tomar decisiones de diseño correctas.

| Criterio / Palabra Clave | ¿Qué significa? | ¿Qué decisión de diseño implica? |
|--------------------------|----------------|----------------------------------|
| **"Microservices"** / **"Microservice por bounded context"** | La arquitectura debe estar compuesta por servicios pequeños e independientes | Cada BC es un microservicio independiente. Comunicación vía REST o eventos. |
| **"Domain-Driven Design"** / **"DDD"** | El diseño debe basarse en el dominio del negocio, no en tecnología | Identificar BCs, Aggregates, Entities, Value Objects. Cada BC tiene su propio modelo. |
| **"Bounded Context"** (lista explícita) | El caso ya hizo el análisis de dominio | Usar esos BCs tal cual. No inventar nuevos. |
| **"Event Storming"** | El equipo modeló colaborativamente el dominio | Los BCs y eventos ya están identificados. Buscar la lista en el texto. |
| **"CQRS"** / **"Command Query Responsibility Segregation"** | Separar comandos (escritura) de consultas (lectura) | Crear Commands, CommandHandlers, Queries. Posiblemente usar diferentes modelos o BD para lectura/escritura. |
| **"Event-Driven Architecture"** / **"EDA"** | La comunicación entre servicios debe ser asíncrona basada en eventos | Usar message broker (Event Grid, Kafka, RabbitMQ). Los BCs publican eventos que otros consumen. |
| **"API Gateway"** | Punto único de entrada para todos los clientes | Incluir un API Gateway en el diagrama de contenedores. |
| **"Serverless"** / **"Azure Functions"** / **"Lambda"** | Código ejecutado bajo demanda sin gestión de servidores | Usar Functions para lógica transaccional, procesamiento de eventos, orquestación ligera. |
| **"Constraint"** / **"basada en [tecnología]"** | Restricción obligatoria del caso | Todas las tecnologías deben cumplir esa constraint. No usar tecnologías fuera de ella. |
| **"SaaS"** / **"Suscripciones"** | Modelo de negocio por suscripción | Se necesita un BC de Billing/Subscription Management. Integración con pasarela de pagos (Stripe, etc.). |
| **"No-Code/Low-Code"** | Usuarios no técnicos crean aplicaciones | Incluir un BC de App Builder. Usar herramientas como Power Apps, Mendix, OutSystems. |
| **"Citizen Developer"** | Usuario de negocio que desarrolla sin código | Es un actor/persona en el diagrama de contexto. Se conecta a la plataforma No-Code. |
| **"Clean Architecture"** / **"Layered Architecture"** | Organización del código en capas (Interfaces, Application, Domain, Infrastructure) | En el diagrama de componentes, mostrar las capas dentro de cada BC (como en el ejemplo de Vanana Platform). |
| **"SSO"** / **"Single Sign On"** | Autenticación unificada entre plataformas | Incluir un BC de Identity & Access Management. Usar Azure AD B2C o similar. |
| **"Roadmap 2 años"** / **"unidades a crearse"** | La empresa planea expandirse | El diseño debe ser extensible. Los BCs futuros deben poder agregarse sin rediseño. |
| **"Equipo tiene experiencia con..."** | Tecnologías que el equipo ya conoce | Priorizar esas tecnologías en el diseño. Minimizar curva de aprendizaje. |

---

## 3. Párrafo Conciso de Sustentación (para este caso)

> *La arquitectura propuesta para EduFlow Digital Corp. se basa en una plataforma cloud-native sobre Microsoft Azure, alineada con los constraints del caso. Se identificaron 12 Bounded Contexts a partir del EventStorming realizado por el equipo, cada uno implementado como un microservicio independiente con su propia base de datos (Database per Service) para garantizar encapsulamiento y escalabilidad. Un API Gateway (Azure API Management) canaliza las peticiones de tres frontends diferenciados: Web App (Angular) para administradores, Mobile App para profesionales independientes, y Power Apps Portal para Citizen Developers. La comunicación asíncrona entre microservicios se gestiona mediante Azure Event Grid, cumpliendo con el requisito de Event-Driven Architecture. Los BCs transaccionales (Billing, Workflow, IAM) utilizan Azure Functions (serverless) para escalado automático, mientras que Workflow Management y Low-Code Integration emplean Azure Logic Apps para orquestación. Los archivos y assets se almacenan en Azure Blob Storage, separándolos de las bases de datos relacionales (Azure SQL) y documentales (Cosmos DB). Esta separación de responsabilidades permite evolucionar cada BC de forma independiente, escalar según demanda, y agregar las nuevas unidades de negocio previstas en el roadmap de 2 años (AI Assistant Services, Mobile Business Services) sin afectar la plataforma existente.*

---

## 4. Plantilla de Sustentación para Futuros Casos

Usa esta plantilla para estructurar tu sustentación en cualquier examen de arquitectura de software.

```markdown
## Sustentación de Decisiones de Diseño - [NOMBRE DEL CASO]

### Resumen ejecutivo
La arquitectura propuesta para [nombre de la empresa] se basa en [tecnología principal / cloud provider] siguiendo los principios de [DDD / Microservicios / EDA]. Se identificaron [N] Bounded Contexts a partir del [EventStorming / análisis de dominio], cada uno implementado como [microservicio / módulo] independiente con [Database per Service / BD compartida según criterio]. [Tecnología específica] se utiliza para [propósito], cumpliendo con los constraints del caso.

### Tabla de decisiones clave

| Decisión | Fuente en el caso | Justificación |
|----------|-------------------|----------------|
| [Decisión 1] | [Página X: "texto literal"] | [Por qué se eligió] |
| [Decisión 2] | [Página X: "texto literal"] | [Por qué se eligió] |
| ... | ... | ... |

### Patrones aplicados

| Patrón | ¿Dónde se aplica? | ¿Por qué? |
|--------|-------------------|-----------|
| Domain-Driven Design | [BCs / Aggregates] | [El caso lo exige / El negocio es complejo] |
| CQRS | [BC específico] | [Separar lecturas de escrituras por rendimiento] |
| Event-Driven Architecture | [Comunicación entre BCs] | [Desacoplamiento / Escalabilidad] |
| API Gateway | [Entrada a microservicios] | [El caso lo exige / Centralizar políticas] |
| Database per Service | [Cada BC] | [Encapsulamiento / Independencia evolutiva] |

### Cumplimiento de constraints obligatorios

| Constraint | Cumplimiento | Evidencia en el diseño |
|------------|--------------|------------------------|
| [Tecnología obligatoria 1] | ✅ Cumple | [Se usó en X componente] |
| [Tecnología obligatoria 2] | ✅ Cumple | [Se usó en Y componente] |
| [Patrón obligatorio] | ✅ Cumple | [Aplicado en Z] |

### Escalabilidad y extensibilidad

El diseño considera el roadmap de [X años] mencionado en el caso, permitiendo agregar nuevas unidades de negocio como [unidad futura 1] y [unidad futura 2] mediante [mecanismo de extensión: nuevos microservicios / eventos / plugins] sin modificar los BCs existentes.

### Conclusión
La arquitectura propuesta cumple con todos los requisitos funcionales y no funcionales del caso, respeta las constraints tecnológicas, aplica los patrones solicitados (DDD, CQRS, EDA, microservicios), y está preparada para la evolución futura de la plataforma.

5. Ejemplo de Aplicación de la Plantilla (Caso Hipotético)
Supongamos un caso: "GreenEnergy Corp necesita una plataforma IoT para monitorear paneles solares. Debe usar AWS, serverless, y el equipo conoce React. El EventStorming identificó BCs: Device Management, Energy Prediction, Billing, Alerts."

Aplicando la plantilla:

## Sustentación de Decisiones de Diseño - GreenEnergy Corp

### Resumen ejecutivo
La arquitectura propuesta para GreenEnergy Corp se basa en AWS siguiendo los principios de DDD y microservicios. Se identificaron 4 Bounded Contexts a partir del EventStorming, cada uno implementado como microservicio independiente con Database per Service. AWS Lambda (serverless) se utiliza para toda la lógica transaccional, cumpliendo con el constraint de serverless del caso.

### Tabla de decisiones clave

| Decisión | Fuente en el caso | Justificación |
|----------|-------------------|----------------|
| Microservicio por BC | *"microservices architecture"* | Permite escalar Energy Prediction (cálculo intensivo) independientemente de Billing |
| Database per Service | No explícito, derivado de DDD | Energy Prediction usa Timestream (series temporales); Billing usa Aurora (transaccional) |
| API Gateway + Lambda | *"serverless"*, *"AWS"* | API Gateway centraliza requests; Lambda ejecuta lógica sin gestión de servidores |
| React para frontend | *"equipo conoce React"* | Minimiza curva de aprendizaje, permite desarrollo rápido del dashboard |

### Patrones aplicados

| Patrón | ¿Dónde se aplica? | ¿Por qué? |
|--------|-------------------|-----------|
| DDD | BCs: Device, Energy, Billing, Alerts | El dominio de energía tiene reglas complejas (predicción, umbrales) |
| EDA | Energy Prediction → Alerts | Al superar umbral, publica evento que Alerts consume para notificar |
| CQRS | Energy Prediction (lectura) vs Device Management (escritura) | Lectura de predicciones es intensiva, escritura de telemetría es transaccional |

### Cumplimiento de constraints

| Constraint | Cumplimiento | Evidencia |
|------------|--------------|-----------|
| AWS | ✅ | Lambda, API Gateway, Timestream, Aurora |
| Serverless | ✅ | Todo el backend usa Lambda |
| React frontend | ✅ | Dashboard en React |

### Escalabilidad
Energy Prediction escala independientemente en horas pico (mediodía). Los BCs de Billing y Alerts pueden tener réplicas de lectura separadas. El roadmap de 1 año incluye BC de Maintenance Prediction, que se agregará como nuevo microservicio consumiendo eventos de Energy Prediction.

### Conclusión
La arquitectura cumple los requisitos de IoT, predicción y billing, respeta AWS y serverless, aplica DDD y EDA, y permite agregar predicción de mantenimiento sin rediseño.
