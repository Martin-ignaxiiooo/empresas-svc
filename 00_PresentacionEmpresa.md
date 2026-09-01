# WorkMatch — Presentación del Caso de Negocio

**Documento para la Evaluación Parcial N°1 (EP01)**

---

## 1. Presentación de la empresa

**WorkMatch** es una plataforma web de búsqueda de empleo orientada a **empresas y empleadores** (lado corporativo de la bolsa de trabajo). Permite a las empresas publicar ofertas de trabajo, recibir y gestionar postulaciones, filtrar candidatos, coordinar entrevistas y administrar sus planes de suscripción de contratación.

- **Rubro:** HR Tech / Empleo corporativo
- **Usuarios objetivo:** reclutadores, RRHH de pymes y grandes empresas
- **Escala esperada:** miles de ofertas publicadas y decenas de miles de postulaciones por mes
- **Modelo de negocio:** suscripción mensual por planes (Básico, Pro, Enterprise) + publicación destacada de ofertas

## 2. Problema / necesidad de negocio

Las empresas gestionan las postulaciones por correo o planillas, lo que genera pérdida de candidatos, tiempos de respuesta lentos y nula trazabilidad del proceso de selección. WorkMatch centraliza la publicación, el filtrado y la comunicación con los candidatos en un único sistema cloud, escalable según la demanda de temporada alta de contrataciones.

## 3. Requisitos funcionales (RF)

| Código | Requisito | Dominio |
|--------|-----------|---------|
| RF-01 | Registrar empresa y usuarios reclutadores con roles y permisos | Gestión de empresas |
| RF-02 | Crear, editar, publicar y archivar ofertas de trabajo | Gestión de ofertas |
| RF-03 | Recibir postulaciones de candidatos provenientes de la app de trabajadores | Postulaciones |
| RF-04 | Buscar y filtrar candidatos por habilidades, experiencia y ubicación | Búsqueda de candidatos |
| RF-05 | Gestionar etapas del proceso (revisión, entrevista, oferta, cierre) | Pipeline de selección |
| RF-06 | Procesar el cobro del plan de suscripción y pagos por publicación destacada | Facturación |
| RF-07 | Notificar por correo/notificaciones push eventos clave del proceso | Notificaciones |
| RF-08 | Generar reportes de métricas de contratación (tiempo, costo, embudo) | Analítica |

## 4. Requisitos no funcionales (RNF)

| Código | Criterio | Requerimiento |
|--------|----------|---------------|
| RNF-01 | Escalabilidad | Soportar picos de carga en temporada alta de contratación escalando componentes de forma independiente |
| RNF-02 | Disponibilidad | Disponibilidad 99,9 %; el módulo de postulaciones no debe caer ante fallas de facturación |
| RNF-03 | Mantenibilidad | Servicios con responsabilidad única, despliegue independiente y bajo acoplamiento |
| RNF-04 | Seguridad | Autenticación y autorización por roles, cifrado de datos personales en tránsito y reposo, auditoría de acciones |
| RNF-05 | Rendimiento | Búsqueda de candidatos con respuesta menor a 2 segundos en la mayoría de las consultas |
| RNF-06 | Integridad de datos | Los datos de postulaciones deben sobrevivir a fallas (persistencia y colas de mensajería) |

## 5. Dominios del negocio y microservicios propuestos

| Microservicio | Responsabilidad (SRP) | Justificación |
|---------------|------------------------|---------------|
| **Empresas y Usuarios** | Registro, autenticación y gestión de perfiles de reclutadores y roles | Cambia por razones de identidad/seguridad; aislar permite escalar y proteger credenciales |
| **Ofertas** | Ciclo de vida de la oferta de empleo (creación, publicación, archivado) | Ciclo de vida propio y alto volumen; se puede escalar de forma independiente |
| **Postulaciones** | Recepción y almacenamiento de postulaciones, integración con la app de candidatos | Dominio central; consume colas (SQS) para absorber picos sin perder postulaciones |
| **Pipeline / Entrevistas** | Avance del candidato por etapas y coordinación de entrevistas | Responsabilidad única sobre el proceso de selección |
| **Facturación** | Planes, suscripciones y cobros de publicaciones | Datos sensibles de pago; aislar por seguridad y normativa |
| **Notificaciones** | Envío de correos y push según eventos del sistema | Independiente y reutilizable por los demás servicios |
| **Analítica** | Reportes de métricas de contratación | Procesos batch/reportes no deben afectar la operación transaccional |

## 6. Arquitectura cloud propuesta

- **Patrones de diseño:** API Gateway como puerta de entrada única, Service Registry para descubrimiento de servicios, Circuit Breaker en llamadas a Facturación y Analítica, Cola de mensajería (AWS SQS) para Postulaciones y Notificaciones, Eventos asíncronos.
- **Serverless:** AWS Lambda para el procesamiento de postulaciones, generación de reportes y envío de notificaciones; API Gateway expone los endpoints.
- **Almacenamiento:** bases de datos relacionales por dominio (una por microservicio), Amazon S3 para CV y documentos adjuntos de candidatos.
- **Seguridad:** autenticación con tokens (JWT/OAuth2), autorización por roles de reclutador, cifrado TLS en tránsito y cifrado at-rest en bases y S3, secreto en AWS Secrets Manager, auditoría de acciones sensibles.
- **Flujos principales:** el candidato postula desde la app de trabajadores → evento llega por SQS → Postulaciones lo registra → notificación por Lambda → pipeline continúa; la búsqueda de candidatos usa índices dedicados para responder rápido.

## 7. Pauta para el diagrama EP01 (checklist)

El diagrama de arquitectura debe representar:

- [ ] Componentes cloud native y microservicios con sus funciones y relaciones (IE4)
- [ ] Patrones: API Gateway, Service Registry, Circuit Breaker, colas SQS (IE5)
- [ ] Flujos de comunicación entre servicios y tecnologías cloud usadas (IE6)
- [ ] Puntos de seguridad: autenticación, autorización, cifrado, controles de acceso (IE7)
- [ ] Flujos de datos críticos: postulación → cola → almacenamiento → reporte, con puntos de monitoreo (IE8)
- [ ] Almacenamiento de datos por dominio (bases relacionales y S3)
- [ ] Criterios de escalabilidad, disponibilidad y mantenibilidad visibles en el diseño
