# AutograFlow SaaS

> **Actualización de producto — 14 de septiembre de 2026.** Esta sección prevalece sobre cifras, nombres de plan y flujos que difieran más abajo. El resto del documento conserva la arquitectura, seguridad, retención, modelo de eventos, roadmap y riesgos solicitados.

## Especificación comercial y funcional consolidada

| Plan | Precio | Vigencia | Documentos preparados | Incluye |
|---|---:|---:|---:|---|
| 3 DÍAS | S/ 4.90 | 3 días | 20 | Funciones individuales y hasta 2 dispositivos. CTA: **Adquirir Plan**. |
| 1 MES | S/ 19.90 | 30 días | 250 | Funciones individuales, estadísticas personales y hasta 2 dispositivos. Etiqueta: **MÁS POPULAR**. |
| 1 AÑO EMPRESARIAL | S/ 149.90 | 365 días | Ilimitados* | Organización, código único, 5 usuarios totales, equipo, estadísticas, dashboard y KPIs; hasta 2 dispositivos por usuario. Etiqueta: **MEJOR VALOR**. CTA: **Solicitar AutograFlow para mi empresa**. |

\* “Ilimitado” debe estar sujeto a política de uso razonable (tamaño/páginas/firmantes máximos y controles contra automatización abusiva), publicada antes de cobrar. El comparador visual ha de mostrar vigencia, cuota, estadísticas, equipo, organizaciones y dispositivos, no únicamente precio.

### Auditoría del 14 de septiembre de 2026 (segunda pasada)

Al comparar este documento contra la especificación vigente se encontraron tres inconsistencias que ya se corrigen en las secciones siguientes:

1. El análisis de rentabilidad de "Corrección de precios antes de lanzar" seguía calculado sobre un pase diario de S/2 y cuotas de 200/4,000 documentos que ya no existen; se recalculó con S/4.90/20 docs, S/19.90/250 docs e ilimitado.
2. Pides Yape + Plin + tarjetas para Perú sin fases, pero la pasarela de MVP (Mercado Pago) solo confirma Yape y tarjetas; Plin queda documentado como Fase 2 (Izipay/Culqi). Se marca como decisión pendiente de confirmar antes de vender el lanzamiento como "Plin incluido".
3. Los importes de ejemplo del usuario adicional (S/21.50 a 270 días, S/14.50 a 180, S/7.25 a 90, S/2.42 a 30) no se derivan de la fórmula publicada `29 × días/365`; corresponden a un modelo de "meses completos" que tampoco reproduce el valor de 270 días exactamente. Se documenta la discrepancia en la sección de prorrateo porque afecta directamente lo que se factura.

### Navegación y UX

A la izquierda del logo se coloca un único botón de menú vertical. En cuentas individuales muestra **Mi Plan** y **Mi progreso y estadísticas**. En cuentas empresariales añade **Mi Equipo** y **Desempeño del Equipo**, con: Dashboard empresarial, KPIs, Usuarios, Departamentos, Estadísticas, Productividad y Configuración. La autorización viene del backend; ocultar el botón no es control de seguridad.

**Mi Plan** muestra plan, inicio, vencimiento, documentos utilizados/restantes (o “ilimitados”), dispositivos registrados y acción para revocarlos. La API rechaza el inicio de una tercera sesión activa, no solo la interfaz.

**Mi Organización** permite al owner/admin ver integrantes, rol, departamento, etiquetas y categoría; muestra preparados, enviados y recibidos firmados. Departamentos se manejan como catálogo por organización (Finanzas, RR.HH., Operaciones, Administración, Legal) y etiquetas como lista controlada. Se deben prohibir etiquetas de datos sensibles que no sean necesarias.

### Organización, plazas e invitaciones

Al registrarse, “Unirme a una organización” revela el campo de código. Si es válido, vigente y hay plaza, la cuenta se integra automáticamente, hereda la fecha de vencimiento del titular y los beneficios empresariales. La pantalla informa nombre, código, ocupación y vencimiento, por ejemplo: `AutograFlow Demo · AF-ORG-5892 · 4/5 · 31/12/2027`.

El código visible es una representación amigable; en base de datos se guarda únicamente su hash. Debe poder rotarse, revocarse, expirar y limitar intentos/uso, con auditoría. Esto conserva la unión inmediata sin convertir un código filtrado en acceso perpetuo.

El dueño compra plazas adicionales prorrateadas a S/29.00/año:

`importe = redondear_a_2(S/29.00 × días_restantes / 365)`

La UI presenta los valores comerciales requeridos: 365 d S/29.00, 270 d S/21.50, 180 d S/14.50, 90 d S/7.25 y 30 d S/2.42, junto al mensaje de cálculo automático y `Usuarios activos 4/5 · Usuarios adicionales 2 · Capacidad total 7`. La plaza termina con la licencia principal; no crea una suscripción separada. Para importes muy pequeños se recomienda agrupar plazas o sumar el saldo a la renovación, porque una tarifa fija de pasarela puede absorber el margen.

### Estadísticas y KPIs

Eventos atómicos: `prepared`, `sent`, `opened`, `signed`, `completed`, `expired`, `marked_received`. Las vistas semanal, mensual y anual se calculan desde esos eventos y se materializan periódicamente para que los dashboards no consulten PDFs ni ralenticen el producto.

| KPI | Fórmula |
|---|---|
| Tasa de finalización | `completed / sent × 100` |
| Tiempo de respuesta | `opened_at − sent_at` |
| Tiempo promedio de firma | `completed_at − opened_at` |
| Productividad | documentos `prepared` por usuario/departamento/período |
| Cumplimiento | `completed antes de expires_at / sent × 100` |
| Pendientes / vencidos | enviados sin completar / enviados cuyo plazo venció |

El dashboard usa tarjetas KPI, barras para comparativos por usuario/departamento, líneas para tendencia temporal y donut para estados. “Crear KPI personalizado” permite nombre, métrica permitida, período, filtros y objetivo; no SQL o fórmulas arbitrarias. Se inicia con datos simulados (marcados como demostración) y luego sustituye por agregados de eventos sin cambiar la UX.

### Sellos y huellas

Sello 1/2 y huella 1/2 son instancias de campo, no tablas duplicadas: `type`, página, posición relativa, tamaño, orden de capa, responsable y vista previa. Se aceptan PNG transparente o captura de cámara, con validación de formato, tamaño, antivirus y recorte local. Las huellas son datos especialmente delicados: no se crea una biblioteca reutilizable en el MVP y sus imágenes se borran con el documento al finalizar la retención.

### Pagos: decisión de implementación

1. **MVP Perú: Mercado Pago Checkout Pro/API.** Permite lanzamiento rápido, tarjetas y Yape; las suscripciones sirven para renovaciones. Su página peruana publica para Checkout tarifas que dependen del plazo de disponibilidad (desde 3.29% + S/1.00; confirmar al contratar) y sin costo fijo. [Mercado Pago Checkout](https://www.mercadopago.com.pe/herramientas-para-vender/check-out) y [suscripciones](https://www.mercadopago.com.pe/herramientas-para-vender/suscripciones). **Nota:** la especificación comercial pide Yape + Plin + tarjetas para Perú sin distinguir fases; Plin no aparece confirmado en la documentación pública de Mercado Pago Perú, solo en Culqi/Izipay (fase 2). Confirmar con el ejecutivo de cuenta de Mercado Pago si Plin está disponible antes de anunciarlo en el lanzamiento; si es un requisito no negociable para el día uno, evaluar adelantar Culqi o Izipay como pasarela principal en vez de Mercado Pago.
2. **Cobertura local ampliada: Culqi o Izipay como adaptador fase 2.** Culqi publica Yape, Plin/billeteras, tarjetas nacionales (3.44%) e internacionales (3.99%), más tarifa fija para online y mínimo de S/3.50 bajo S/87.72; Izipay publica tarjetas, QR, Yape, Apple Pay y Plin Interbank. Confirmar costo, Google Pay y elegibilidad comercial directamente antes de prometerlos. [Culqi tarifas](https://culqi.com/precios/), [Izipay condiciones](https://cdnwebb.izipay.pe/content-manager/pdfs/terminos/233/T%C3%A9rminos%20y%20condiciones).
3. **Internacional:** tarjetas Visa/Mastercard por el adquirente que acepte a la sociedad peruana; Apple Pay/Google Pay solo tras validación contractual y pruebas. Separar moneda, impuestos, contracargos y comprobantes antes de vender fuera del país.

La API expone un adaptador `PaymentProvider` (crear checkout, consultar pago, verificar webhook, reembolso/cancelación). El plan se activa exclusivamente tras webhook firmado, idempotente y conciliado: nunca por volver desde la página de pago.

## Decisión ejecutiva

AutograFlow debe evolucionar de un HTML local a un SaaS multiusuario con un **VPS propio para la aplicación**, PostgreSQL, Redis y almacenamiento de objetos temporal compatible con S3. El producto no debe conservar el contenido de los documentos de forma permanente: los archivos se guardan cifrados durante la ventana elegida (24 horas, 7 días o 30 días) y se eliminan automáticamente. Sí se conserva una auditoría mínima, separada del archivo, porque permite soportar el producto y atender disputas sin convertir AutograFlow en un repositorio documental.

La recomendación de lanzamiento es: Perú primero, plan individual y empresarial, enlaces de firma por QR/WhatsApp, Mercado Pago como primera pasarela y una segunda integración de Izipay después de validar ventas. No conviene afirmar que una firma dibujada es una “firma digital certificada”; debe denominarse **firma electrónica simple** hasta integrar un proveedor/certificado aplicable.

## Modelo de producto

| Área | MVP | Regla principal |
|---|---|---|
| Documento | PDF, 1–2 firmantes, campos de firma/sello/huella | No se almacena permanentemente |
| Envío | URL de un solo uso + QR + botón WhatsApp/email | El token nunca contiene datos ni ID secuencial |
| Cuenta | registro, login, recuperación, cambio de contraseña | contraseña: solo hash Argon2id |
| Planes | diario, mensual, anual empresarial | cuotas comprobadas en backend, no en navegador |
| Organización | código de invitación y 5 plazas incluidas | el consumo pertenece a la organización |
| Métricas | eventos y agregados | no inspeccionar ni indexar contenido del PDF |

### Corrección de precios antes de lanzar

Con los precios vigentes (S/4.90 / 3 días / 20 docs, S/19.90 / mes / 250 docs, S/149.90 / año ilimitado) el margen ya no es negativo como en la versión anterior de este análisis, pero sigue siendo ajustado en el plan de 3 días:

- **3 días (S/4.90):** si el cobro se procesa como un enlace de pago con comisión mínima fija (por ejemplo, CulqiLink publica S/3.50 mínimo para montos bajo S/87.72), el margen bruto queda en ~S/1.40 antes de IGV, soporte e infraestructura — ajustado pero no negativo. [Culqi, comisión de CulqiLink](https://ayuda.culqi.com/portal/es/kb/articles/cual-es-la-comisi%C3%B3n-de-culqilink). Si en cambio se cobra por el Checkout API/suscripción normal de Mercado Pago (porcentaje + fijo, sin mínimo tipo CulqiLink), el margen es mayor; **hay que confirmar con la pasarela contratada cuál esquema de comisión aplica a un cobro único de S/4.90** antes de lanzar este plan.
- **1 mes (S/19.90 / 250 docs):** equivale a S/0.0796 por documento si se agota la cuota completa.
- **1 año empresarial:** al ser "ilimitado", no hay un costo por documento que acotar por precio; el riesgo de costo se controla únicamente con la política de uso razonable (tamaño de PDF, páginas, firmantes y detección de automatización), no con el precio del plan.

Recomendación: mantener el plan de 3 días pero confirmar el esquema de comisión real antes de venderlo, o venderlo empaquetado (ej. agrupar el cobro con el primer uso) si la pasarela aplica un mínimo fijo por transacción que erosione el margen. En todos los planes, imponer tamaño máximo de PDF, número máximo de firmantes y política de uso razonable para que el costo de procesamiento no supere el margen.

## Arquitectura objetivo

```text
Navegador / móvil
  └─ Cloudflare DNS + WAF + TLS + rate limiting
       └─ VPS: Caddy o Nginx (HTTPS y proxy)
            ├─ Frontend web (React/Next.js)
            ├─ API TypeScript (NestJS o Fastify)
            ├─ Worker de trabajos (BullMQ)
            ├─ PostgreSQL
            ├─ Redis (sesiones, cuotas, colas, rate limits)
            └─ MinIO local o, preferible, Cloudflare R2 (objetos temporales)
                   └─ ciclo de vida: borrado automático 1 / 7 / 30 días

Servicios externos: correo transaccional, Mercado Pago, Izipay (fase 2),
monitorización y copias cifradas de PostgreSQL.
```

El VPS aloja la lógica y la base de datos; el objeto PDF no debe pasar por el disco persistente del servidor cuando sea evitable. La API emite una URL prefirmada de carga y otra de descarga de corta vida; el navegador carga/descarga directamente del almacenamiento. Cloudflare R2 es compatible con S3 y publica 10 GB-mes, 1 millón de escrituras y 10 millones de lecturas mensuales gratuitas; después cobra US$0.015/GB-mes de almacenamiento estándar y no cobra egreso a Internet. [R2: funcionamiento](https://developers.cloudflare.com/r2/how-r2-works/), [tarifas vigentes](https://developers.cloudflare.com/r2/pricing/)

### Despliegue inicial en VPS

- Ubuntu LTS endurecido, acceso SSH solo con claves, usuario sin privilegios y actualizaciones automáticas de seguridad.
- Docker Compose con servicios aislados: `web`, `api`, `worker`, `postgres`, `redis`, `caddy` y `backup`.
- PostgreSQL y Redis sin puertos públicos; solo la capa proxy expone 80/443.
- Variables secretas fuera del repositorio, rotadas; no usar claves de pago en el frontend.
- Copia diaria cifrada de PostgreSQL hacia un bucket diferente; prueba mensual de restauración.
- Monitorización de disponibilidad, disco, memoria, errores y cola; alertas por correo/Telegram.

Un solo VPS es aceptable para MVP, pero es un punto único de falla. Cuando haya ingresos recurrentes, separar PostgreSQL en un segundo VPS o servicio administrado y ejecutar una réplica/plan de restauración probado.

## Flujo del documento y QR

1. El preparador crea un `document` y selecciona retención: 24 h, 7 d o 30 d.
2. La API valida cuota y tamaño; emite URL prefirmada de carga. El navegador sube el PDF cifrado al almacenamiento temporal.
3. El usuario fija campos por página: firma, sello y huella. Las coordenadas son relativas a cada página, no al tamaño de pantalla.
4. La API crea un `signing_request` con token aleatorio de 256 bits, almacena únicamente su hash y fija expiración/uso único.
5. El frontend crea QR y enlace `https://app.autograflow.pe/f/<token>`; WhatsApp comparte ese enlace con texto neutral.
6. El firmante abre el enlace, acepta el aviso de privacidad/consentimiento, firma y confirma. El backend registra el evento y genera el PDF final; el enlace del siguiente firmante conserva la versión anterior y solo habilita su campo.
7. La descarga final usa una URL prefirmada de pocos minutos. El archivo se borra al vencer la retención; si la política lo permite, se puede programar borrado tras la descarga final más un margen de recuperación de 24 h.

No debe afirmarse que una descarga demuestra que el destinatario guardó el archivo: se puede registrar el evento de entrega/solicitud de descarga, no el resultado físico en su dispositivo.

### Política de retención

| Elemento | Retención propuesta | Justificación |
|---|---:|---|
| PDF original, intermedios y PDF final | selección: 24 h, 7 d, 30 d; predeterminado 7 d | entrega y reintento razonable |
| Firma, sello y huella cargados | mismos límites del documento; borrar junto con él | minimización |
| URL/token de firma | hasta completar o vencer; hash hasta 90 días | revocación y soporte |
| Auditoría mínima | 90 días en individual; 12 meses configurable en empresarial | disputas/KPIs, sin PDF |
| pagos/facturación | plazo exigido por la normativa tributaria y el proveedor | no confundir con retención del documento |

La Ley peruana define tratamiento de manera amplia e incluye uso, almacenamiento y supresión; exige finalidad, proporcionalidad, seguridad y conservación solo durante el tiempo necesario. El borrado automático reduce exposición, pero no elimina las obligaciones sobre cuentas, logs, pagos o metadatos. [Ley 29733](https://www.congreso.gob.pe/Docs/DGP/DIDP/files/ley_29733.pdf)

## Seguridad y privacidad

### Identidad y sesiones

- Hash Argon2id con sal única; OWASP recomienda como mínimo 19 MiB, 2 iteraciones y paralelismo 1. Nunca cifrar ni guardar contraseñas en texto plano. [OWASP Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- Cookies `HttpOnly`, `Secure`, `SameSite=Lax/Strict`; sesiones rotativas y revocables, no JWT de larga vida guardados en `localStorage`.
- Recuperación de contraseña mediante token de un solo uso, hash del token en base de datos, vida de 15 minutos y notificación al correo.
- MFA opcional para administradores empresariales antes de ampliar el plan a organizaciones grandes.
- Dos dispositivos activos por usuario. Un dispositivo es una sesión registrada con identificador aleatorio y datos mínimos (último uso, agente resumido); permitir revocación desde “Mi Plan”. No usar huella invasiva de navegador como mecanismo principal.

### Controles obligatorios

- Rate limiting por IP, cuenta, enlace y acción sensible; CAPTCHA progresivo solo después de señales de abuso.
- Validar MIME y firma binaria del PDF, limitar tamaño/páginas, analizar archivos en una cola aislada y rechazar PDFs cifrados o malformados según política.
- Cifrado TLS en tránsito; cifrado del bucket; claves administradas/rotadas. Nunca colocar documentos o tokens completos en logs.
- Autorización en cada consulta con `organization_id`; usar políticas por fila en PostgreSQL como defensa adicional. PostgreSQL permite definir políticas de seguridad por fila. [PostgreSQL RLS](https://www.postgresql.org/docs/17/ddl-rowsecurity.html)
- Webhooks de pago: verificar firma, registrar `event_id`, responder idempotentemente y reconciliar contra el API de la pasarela. Nunca activar un plan al volver del navegador.
- Pruebas de autorización horizontal: un miembro nunca puede leer documentos, usuarios o métricas de otra organización.

### Avisos necesarios

Antes de usar el servicio deben existir términos, política de privacidad, política de retención/borrado, canal de derechos de datos y aviso de qué significa la firma ofrecida. La inscripción de un banco de datos personales puede ser aplicable cuando se almacenen cuentas y otros datos organizados; la ANPD indica que los titulares de bancos de datos tienen obligación de inscribirlos. Debe confirmarse el alcance con asesoría peruana antes de producción. [ANPD: inscripción](https://www.gob.pe/8060-inscribir-banco-de-datos-en-el-registro-nacional-proteccion-de-datos-personales)

## Diseño de base de datos

| Tabla | Campos esenciales | Notas |
|---|---|---|
| `users` | id, email único, password_hash, status, verified_at | sin contraseña ni token en claro |
| `user_devices` | id, user_id, session_hash, last_seen_at, revoked_at | máximo 2 activos |
| `organizations` | id, owner_user_id, name, invite_code_hash, plan_subscription_id | código rotativo, no predecible |
| `memberships` | org_id, user_id, role, department_id, tags, status | roles owner/admin/member/viewer |
| `plans` | id, code, price, duration_days, doc_limit, included_seats | catálogo versionado |
| `subscriptions` | id, owner type/id, plan_id, starts_at, ends_at, status, provider | fuente de verdad del acceso |
| `organization_seats` | org_id, quantity_included, quantity_extra, effective_at | no derivar plazas solo desde UI |
| `usage_periods` | owner type/id, starts_at, ends_at, prepared_count | contador transaccional/atómico |
| `documents` | id, owner/org, status, retention_policy, expires_at, object_key cifrada | no guardar el PDF en PostgreSQL |
| `document_pages` | document_id, page_number, width, height | coordenadas consistentes |
| `fields` | page_id, type, signer_order, x/y/w/h relativos | firma/sello/huella |
| `signing_requests` | document_id, token_hash, signer_email opcional, expires_at, used_at | URL de un solo uso |
| `document_events` | document_id, actor type/id, event_type, at, metadata minimizada | auditoría sin contenido |
| `payments` | provider, provider_payment_id único, amount, currency, status | idempotencia |
| `webhook_events` | provider, external_event_id único, received_at, payload_hash | deduplicación |
| `custom_kpis` / `kpi_values` | org_id, fórmula/definición, periodo, valor | definición versionada |

Para cada tabla multiempresa, incluir `organization_id`, índices compuestos y controles de pertenencia. `document_events` debe separar IP truncada/seudonimizada de los metadatos de documento; no guardar la imagen de firma dentro del evento.

## Usuarios, organizaciones y permisos

El plan anual empresarial crea una organización y asigna cinco plazas incluidas: titular + hasta cuatro miembros. Cada nueva cuenta puede elegir “Crear organización” o “Unirse a una organización”; para unirse ingresa un código y recibe aprobación del administrador por defecto. El código no debe conceder acceso automático indefinido: vence, puede rotarse y limita intentos.

| Rol | Capacidades |
|---|---|
| Owner | pago, plan, plazas, borrado, exportes, administración total |
| Admin | miembros, departamentos, etiquetas, documentos/KPIs; sin modificar titular de pago |
| Member | preparar/enviar sus propios documentos y ver sus métricas |
| Viewer | lectura limitada de tablero, sin descarga de documentos por defecto |

Departamentos y etiquetas son catálogos de organización. Los filtros deben producir estadísticas agregadas; no usar etiquetas sensibles como salud, religión u otra información que no sea necesaria.

### Usuarios adicionales: prorrateo

Para una suscripción anual de 365 días:

`importe = redondear_a_2(29 × días_restantes / 365)`

Ejemplo: si faltan 182 días, `29 × 182 / 365 = S/ 14.46`. El cargo añade una plaza con la misma fecha de vencimiento del plan principal; no crea una mini-suscripción distinta. Definir por contrato si el importe incluye IGV y aplicar el mismo criterio en pantalla, factura y pasarela. Para importes demasiado bajos, establecer un mínimo de checkout o agrupar varias plazas, ya que la comisión fija puede absorber el margen.

**Discrepancia numérica pendiente de resolver.** La especificación comercial pide mostrar exactamente 365 d → S/29.00, 270 d → S/21.50, 180 d → S/14.50, 90 d → S/7.25, 30 d → S/2.42. Ninguna fórmula única reproduce los cinco valores:

| Días | `29 × días/365` (exacto) | `29/12 × meses completos` | Valor pedido |
|---:|---:|---:|---:|
| 365 | S/29.00 | S/29.00 | S/29.00 |
| 270 | S/21.45 | S/21.75 (9 meses) | S/21.50 |
| 180 | S/14.30 | S/14.50 (6 meses) | S/14.50 |
| 90 | S/7.15 | S/7.25 (3 meses) | S/7.25 |
| 30 | S/2.38 | S/2.42 (1 mes) | S/2.42 |

El modelo de "meses completos" reproduce 4 de 5 valores exactos (90, 180, 30 días y el año completo) pero no el de 270 días (da S/21.75, no S/21.50); el modelo de días exactos no reproduce ninguno de los intermedios. Antes de programar el cobro, decidir cuál es la fórmula real: si es por meses completos, confirmar si S/21.50 a 270 días es una excepción comercial intencional o un error de dedo en la especificación (lo matemáticamente consistente con esa tabla sería S/21.75). Sea cual sea la fórmula elegida, debe ser la misma en pantalla, factura y pasarela — mostrar un número en la UI y cobrar otro por redondeo distinto en backend genera reclamos.

## Pagos

### Estrategia recomendada

**MVP: Mercado Pago.** Su documentación peruana soporta Checkout/API para tarjetas y Yape, y una API de suscripciones con cobros automáticos y reintentos. Es la mejor primera integración para mensual/anual porque evita construir recurrencia desde cero. [Checkout API](https://www.mercadopago.com.pe/developers/es/docs/checkout-api-payments/overview), [Suscripciones](https://www.mercadopago.com.pe/developers/es/docs/subscriptions/overview)

**Fase 2: Izipay como adaptador alternativo.** Sus términos publicados mencionan tarjetas, QR, Yape, Apple Pay y Plin Interbank; también publica condiciones para Google Pay. Confirmar comercialmente la disponibilidad exacta de cada método para un comercio online antes de prometerlo. [Izipay: términos](https://cdnwebdesa.izipay.pe/content-manager/pdfs/terminos/222/T%C3%A9rminos%20y%20condiciones), [Google Pay](https://cdnwebb.izipay.pe/content-manager/pdfs/terminos/215/T%C3%A9rminos%20y%20condiciones)

**Internacional.** Cobrar tarjetas internacionales a través de la pasarela que apruebe la empresa peruana; no basar el lanzamiento en Stripe sin comprobar elegibilidad de la entidad, pues su disponibilidad depende del país/region de la empresa. [Stripe: disponibilidad global](https://stripe.com/global) Para internacionalizar de verdad, validar fiscalidad, moneda, contracargos y proveedor de merchant-of-record con contador/abogado antes de abrir ventas fuera de Perú.

Implementar una interfaz `PaymentProvider` con: crear checkout, verificar webhook, reembolso, cancelar/pausar suscripción y consultar estado. Así Mercado Pago no queda mezclado con reglas de plan, y una segunda pasarela no exige reescribir el sistema.

## UX/UI propuesta

### Navegación

- Inicio: resumen de cuota y “Preparar documento”.
- Mi Plan: plan, activación, vencimiento, documentos usados/restantes, dispositivos y renovación.
- Mi Organización: miembros, plazas, departamentos, etiquetas e invitaciones.
- Documentos: estados `borrador`, `listo`, `enviado`, `visto`, `firmado`, `vencido`, `eliminado`.
- Estadísticas: semana/mes/año, filtros por persona/departamento/etiqueta.
- Configuración: seguridad, privacidad, retención predeterminada y facturación.

### Flujo de firma

El preparador debe ver una línea de progreso: “Preparar → Enviar → Esperar firma → Descargar → Se eliminará el [fecha]”. El firmante solo ve el documento, la identidad del preparador cuando se tenga autorización, el aviso de tratamiento, el método de firma y una confirmación final. QR es una alternativa de apertura, no un factor de autenticación: el enlace debe ser suficientemente aleatorio y expirar.

### Sellos y huellas

Tratar sello y huella como tipos de campo, no como módulos duplicados. Cada `field` tiene `type = signature | stamp | fingerprint`, página, posición, tamaño, capa y responsable. La imagen cargada pasa por recorte y se conserva solo hasta la expiración del documento. En una versión posterior, permitir una biblioteca de sellos reutilizables solo con consentimiento explícito y controles reforzados; la huella es especialmente sensible y no debe almacenarse como biblioteca en el MVP.

## Métricas y KPIs

Eventos base: `prepared`, `sent`, `opened`, `signed`, `completed`, `expired`, `marked_received`. Con estos eventos:

| Indicador | Fórmula |
|---|---|
| Tasa de finalización | documentos completados / documentos enviados |
| Tiempo de respuesta | `opened_at - sent_at` |
| Tiempo de firma | `completed_at - opened_at` |
| Documentos vencidos | enviados sin completar al superar `expires_at` |
| Productividad | preparados por usuario y periodo |
| Cumplimiento | completados antes de vencimiento / enviados |

Los KPIs personalizados deben ser métricas predefinidas configurables por filtros y objetivo, no SQL libre escrito por administradores. Esto evita fuga de datos y consultas costosas.

## Costos operativos estimados

Los siguientes montos son un presupuesto de arranque, no cotizaciones; deben revisarse al contratar. Excluyen IGV, dominio, contador, soporte y comisión por cobro.

| Concepto | MVP mensual | Crecimiento mensual | Observación |
|---|---:|---:|---|
| VPS 4 vCPU / 8 GB | US$24–48 | US$48–96 | aplicación, worker y DB pequeña |
| Backups/volumen | US$5–15 | US$15–40 | cifrados y con restauración probada |
| R2 temporal | US$0–5 | US$5–30 | depende de GB-día y operaciones; 10 GB-mes incluidos |
| Correo transaccional | US$0–20 | US$20–80 | verificación y recuperación |
| WAF/DNS/monitorización | US$0–25 | US$25–100 | priorizar límites y alertas |
| Total infraestructura | **US$29–113** | **US$113–346** | sin comisiones de pago |

Ejemplo de almacenamiento: 1,000 PDFs de 5 MB guardados 7 días consumen aproximadamente 1.17 GB-mes (`1,000 × 0.005 GB × 7/30`), por debajo de los 10 GB-mes gratuitos de R2, sin considerar intermedios. El costo que más puede crecer es el procesamiento, correo, soporte y pagos, no el almacenamiento si se aplica TTL.

## Roadmap

### MVP — 8 a 12 semanas

1. Convertir el HTML actual a frontend modular sin cambiar primero la lógica visual de firma.
2. VPS, CI/CD, HTTPS, PostgreSQL, Redis, storage temporal, backups y monitorización.
3. Cuentas, verificación de correo, recuperación de contraseña, sesiones/dispositivos y cuotas backend.
4. Documento remoto: carga temporal, campos multipágina, URL de firma, QR y WhatsApp.
5. Mercado Pago, webhooks idempotentes, planes y “Mi Plan”.
6. Auditoría mínima, expiración/borrado probado, términos/política y pruebas de seguridad.

**Criterio de salida:** un usuario paga, prepara, comparte un QR/enlace, el firmante completa el flujo, descarga el resultado y el sistema elimina los objetos al vencer sin intervención manual.

### Versión 2 — 6 a 10 semanas

- Organizaciones, invitaciones, cinco plazas, adicionales prorrateados y roles.
- Mi Organización, departamentos, etiquetas y métricas estándar.
- Izipay/adaptador de pago alternativo; revisión de Apple Pay, Google Pay y Plin contractual.
- Sellos, controles de reutilización y exportes de métricas.
- MFA para administradores, panel de soporte y pruebas de carga.

### Versión 3 — según validación comercial

- Flujos de múltiples firmantes, recordatorios, orden de firma y plantillas.
- Integración con firma digital certificada/proveedor acreditado si el mercado lo exige.
- API empresarial, SSO, roles avanzados, retención configurable por organización y KPIs personalizados seguros.
- Alta disponibilidad: segundo VPS/BD, colas separadas, recuperación ante desastre y pruebas externas de penetración.

## Riesgos y decisiones pendientes

| Riesgo | Mitigación |
|---|---|
| Pérdida de un VPS | backups externos cifrados + restauración ensayada |
| Acceso entre empresas | autorización por organización, RLS y pruebas automatizadas |
| Fraude de enlaces | tokens largos, hash, expiración, uso único, revocación y rate limits |
| Costo de documentos grandes | límite por plan, TTL, carga directa a objeto y cuotas |
| Pase diario no rentable | cambiar precio o agrupar cobro antes del lanzamiento |
| Reclamación por firma | lenguaje comercial preciso, consentimiento y auditoría mínima |
| Pago falso desde navegador | activar acceso solo por webhook verificado |
| Tratamiento de biometría/huella | no almacenar bibliotecas de huellas; asesoría legal previa |

## Próximas decisiones

1. Definir nombre de dominio y proveedor de VPS/región.
2. Decidir si el pase diario se elimina, sube de precio o se vende en paquete.
3. Fijar límites de tamaño/páginas/firmantes por plan.
4. Abrir cuenta de vendedor y entorno de pruebas de Mercado Pago.
5. Validar términos, privacidad, retención y tratamiento de firma/huella con abogado peruano.
6. Diseñar el MVP como proyecto nuevo; conservar el HTML actual como prototipo de referencia hasta que los flujos críticos estén probados.

## Fuentes

1. [Ley N.º 29733, Ley de Protección de Datos Personales](https://www.congreso.gob.pe/Docs/DGP/DIDP/files/ley_29733.pdf).
2. [ANPD, inscripción de banco de datos personales](https://www.gob.pe/8060-inscribir-banco-de-datos-en-el-registro-nacional-proteccion-de-datos-personales).
3. [OWASP, Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html).
4. [OWASP, Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html).
5. [PostgreSQL 17, Row Security Policies](https://www.postgresql.org/docs/17/ddl-rowsecurity.html).
6. [Cloudflare R2, pricing](https://developers.cloudflare.com/r2/pricing/).
7. [Mercado Pago Perú, Checkout API](https://www.mercadopago.com.pe/developers/es/docs/checkout-api-payments/overview) y [Suscripciones](https://www.mercadopago.com.pe/developers/es/docs/subscriptions/overview).
8. [Izipay, términos y condiciones de pasarela online](https://cdnwebdesa.izipay.pe/content-manager/pdfs/terminos/222/T%C3%A9rminos%20y%20condiciones).
9. [Culqi, comisión de CulqiLink](https://ayuda.culqi.com/portal/es/kb/articles/cual-es-la-comisi%C3%B3n-de-culqilink).
