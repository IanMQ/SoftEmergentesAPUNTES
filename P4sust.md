5. Párrafo de Sustentación para la Pregunta 4
User Profiles - CQRS (ChangeEmailCommand)

Criterios y Sustento de decisión

He tomado en consideración el comando ChangeEmailCommand dentro del bounded context User Profiles, aplicando los principios de CQRS para separar explícitamente las operaciones de escritura de las de lectura. El comando es inmutable y contiene todos los parámetros necesarios en su constructor: userId, newEmail y requestedBy para auditoría. Su correspondiente ChangeEmailCommandHandler implementa la interfaz genérica CommandHandler<T> y recibe por inyección de dependencias cuatro servicios: UserProfileRepository para persistencia, EmailUniquenessService para validar unicidad global del email, EmailVerificationService para enviar el código de verificación, y DomainEventPublisher para publicar eventos de dominio. El handler valida el formato del email y su unicidad antes de delegar en el aggregate root UserProfile el método changeEmail(), que aplica reglas de negocio como verificar que el usuario esté activo y que el nuevo email sea diferente al actual. Posteriormente, se publica el evento de dominio EmailChangeRequestedEvent, cuyo estado se representa mediante la enumeración EmailChangeStatus (PENDING_VERIFICATION, VERIFIED, COMPLETED, REJECTED, EXPIRED), permitiendo que otros bounded contexts como Billing o Notification Services reaccionen al cambio pendiente. Esta separación entre comandos (escritura) y consultas (lectura) se alinea con el requisito del caso de implementar CQRS y Event-Driven Architecture, al tiempo que mantiene el aggregate root como única fuente de verdad para las reglas de negocio del cambio de email.

Párrafo de Sustentación (versión corta)
He seleccionado el comando ChangeEmailCommand dentro del bounded context User Profiles. El comando es inmutable y contiene en su constructor los parámetros userId y newEmail. El ChangeEmailCommandHandler recibe un UserRepository por dependencia y su método handle() recupera el aggregate UserProfile, ejecuta su método changeEmail() que contiene las reglas de negocio (usuario activo y email diferente), y finalmente persiste el cambio. Esta separación entre el comando (escritura) y su handler aplica CQRS al separar explícitamente la operación de cambio de email de cualquier consulta de lectura.

Tabla: Lo esencial que debes incluir (y lo que puedes omitir)
Debes incluir (puntúa)	Puedes omitir (ahorra tiempo)
Command con constructor y parámetros	Domain Events
CommandHandler con método handle	Event Publisher
Repository (interface o clase)	Servicios de dominio complejos
Aggregate Root con método de negocio	Enums de estado
Una regla de negocio clara (ej. validación)	Múltiples dependencias
Relación: Handler → Repository → Aggregate	getters/setters innecesarios
Consejos para el examen (100 minutos)
Pregunta	Tiempo sugerido	Clave
P1 (Context Diagram)	15 min	4 actores + sistema principal + 2 externos
P2 (Containers + sustento)	30 min	API Gateway + microservicios + BD por servicio
P3 (UML de dominio)	25 min	Aggregate Root + 2 Value Objects + métodos
P4 (CQRS)	30 min	Command + Handler + Repository + regla de negocio
Estrategia: En P4, no dibujes 10 clases. Dibuja 4-5 clases bien definidas con lo esencial. El párrafo de sustento puede ser 5-6 líneas bien escritas.
