Sustento (párrafo corto para la P3)
He seleccionado el bounded context User Profiles. El aggregate root es UserProfile, que contiene un Email como Value Object por ser inmutable y tener su propia validación. UserProfile expone métodos de negocio como createProfile(), changeEmail(), activate() y deactivate(). Se incluye la enumeración Language para los idiomas soportados. Esta estructura cumple con DDD al encapsular las reglas de negocio dentro del aggregate, manteniendo la simplicidad para facilitar su implementación.

User Profiles

Criterios y Sustento de decisión

He tomado en consideración el bounded context User Profiles, detallando en cada clase sus métodos y atributos más relevantes. 
Por ejemplo, UserProfile como Aggregate Root, que representa la información principal de un usuario dentro de la plataforma EduFlow. 
Esta clase tiene una relación de composición de 1 a 1 con Email, Address y NotificationPreferences, todos ellos implementados como Value Objects por ser inmutables y carecer de identidad propia fuera del Aggregate Root. 
Email encapsula la lógica de validación de formato y dominio, mientras que Address agrupa los datos de ubicación física del usuario para fines de facturación. 
NotificationPreferences contiene las preferencias de notificación del usuario por canal (email, SMS, push) e incluye métodos como canSendEmail() y enableEmail() que encapsulan reglas de negocio como la dependencia entre marketingEmails y emailEnabled. 
Además, UserProfile se relaciona con las enumeraciones Language (idiomas soportados: ENGLISH, SPANISH, FRENCH, PORTUGUESE) y NotificationPreferences a su vez con AlertThreshold (IMMEDIATE, HOURLY, DAILY, NEVER), las cuales representan valores fijos y conocidos en tiempo de diseño. 
Finalmente, la clase UserProfile expone métodos de comando como createProfile(), changeEmail(), activate() y deactivate(), así como métodos de consulta como getNotificationPreferences(), aplicando de forma temprana los principios de CQRS al separar operaciones de escritura de las de lectura, lo que facilitará la implementación solicitada en la pregunta siguiente.

Nota: Este párrafo integra:

Nombre del BC

Aggregate Root (UserProfile)

Value Objects (Email, Address, NotificationPreferences)

Enums (Language, AlertThreshold)

Métodos representativos

Relaciones (composición de 1 a 1)

Justificación de decisiones (inmutabilidad, encapsulamiento, CQRS temprano)

Conexión con la Pregunta 4


1. Descripción y Especificación del Bounded Context
Nombre
User Profiles (Perfiles de Usuario)

Propósito
Gestionar la información de perfil de todos los usuarios de la plataforma EduFlow, incluyendo sus datos personales, preferencias de notificación, y configuración de cuenta.

Responsabilidades
Crear, actualizar y eliminar perfiles de usuario

Validar datos de contacto (email, teléfono)

Gestionar preferencias de notificación por canal (email, SMS, push)

Almacenar preferencias de idioma y zona horaria

Proveer información de perfil a otros BCs (ej. Billing necesita el email para facturar)

Límites (Ubiquitous Language)
Término	Definición
UserProfile	Agregado raíz que contiene toda la información de un usuario
Email	Value Object que valida formato y unicidad
NotificationPreferences	Value Object que define cómo y cuándo notificar al usuario
Address	Value Object con dirección física (para facturación)
Eventos de dominio (opcional para este BC)
UserProfileCreated (publicado cuando un usuario se registra)

UserProfileUpdated (cuando cambia datos personales)

NotificationPreferencesChanged (cuando cambia preferencias)

3. Explicación y Sustento de Decisiones de Diseño
Decisión 1: UserProfile como Aggregate Root
¿Por qué? UserProfile es la entidad principal que agrupa y controla el acceso a todas las demás entidades y value objects del BC. Todos los cambios al perfil deben pasar por el Aggregate Root para garantizar consistencia transaccional.

Regla de negocio que lo justifica: Un usuario no puede tener preferencias de notificación sin un perfil activo. Tampoco puede cambiar su email sin validación. El Aggregate Root UserProfile orquesta estas reglas.

Decisión 2: Email como Value Object (inmutable)
¿Por qué? El email tiene reglas de validación propias (formato, dominio permitido) y no tiene identidad propia fuera del UserProfile.

Reglas de negocio:

El email debe tener formato válido (usuario@dominio.com)

El dominio debe estar en lista blanca (ej. solo @eduflow.com o @corporativos)

Un email no puede ser reutilizado en otro perfil activo

Ventaja: Si la regla de validación de email cambia, solo se modifica la clase Email, no todo UserProfile.

Decisión 3: NotificationPreferences como Value Object
¿Por qué? Las preferencias de notificación son un conjunto de atributos que juntos definen el comportamiento, pero no tienen identidad propia. Si un usuario cambia sus preferencias, se reemplaza el objeto completo.

Reglas de negocio:

Si emailEnabled = false, no se pueden enviar marketingEmails ni weeklyDigest

El alertThreshold solo aplica si pushEnabled = true

Métodos de comportamiento:

canSendEmail() encapsula la lógica: retorna emailEnabled && isActive

enableEmail() modifica el estado interno manteniendo invariantes

Decisión 4: Separación de métodos de consulta y comandos (aplicación temprana de CQRS)
¿Por qué? Aunque la Pregunta 4 es la que explícitamente pide CQRS, ya en este diagrama se aplica sutilmente:

Tipo	Métodos en UserProfile
Comandos (escritura)	createProfile(), updateProfile(), changeEmail(), activate(), deactivate()
Consultas (lectura)	getNotificationPreferences()
Esto facilita la implementación de CQRS en la Pregunta 4.

Decisión 5: Uso de enums para Language y AlertThreshold
¿Por qué? Son valores fijos y conocidos en tiempo de diseño. No cambian dinámicamente.

Language: Idiomas soportados por la plataforma

AlertThreshold: Frecuencias predefinidas para alertas

Decisión 6: Nomenclatura en inglés y convenciones UML
Convención	Ejemplo	Justificación
Upper-Camel-Case para clases	UserProfile, NotificationPreferences	Estándar UML y Java/C#
lowerCamelCase para atributos/métodos	fullName, canSendEmail()	Estándar Java/C#
Prefix is para booleanos	isActive, isValid()	Claridad semántica
<<stereotype>> para clasificar	<<Aggregate Root>>, <<Value>>	Claridad en DDD
