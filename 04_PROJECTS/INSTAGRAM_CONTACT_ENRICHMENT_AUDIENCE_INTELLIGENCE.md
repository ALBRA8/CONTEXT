# P-0010 — Instagram Contact Enrichment & Audience Intelligence

- Estado: PROPOSED — pendiente de aprobación explícita del propietario.
- Fecha: 2026-10-17
- Ubicación propuesta: capacidad de investigación/enriquecimiento de `AGENTE-LEADS`, dentro de Radar de Clientes.
- Tipo: capacidad de producto; no es un agente nuevo.
- Fuente de la idea: vídeo de demostración compartido por el propietario (`ssstik.io_@notfound404.es_1792234494848.mp4`) y descripción del flujo. El mecanismo interno, la exactitud de los datos y las cifras que aparecen en pantalla no han sido verificados independientemente.
- Estado de implementación: NO IMPLEMENTADO / NO AUDITADO.

## 1. Objetivo

Evaluar una capacidad que, partiendo de una cuenta competidora de Instagram, ayude a descubrir oportunidades comerciales mediante:
1. enriquecimiento de datos de contacto profesionales que el negocio haya publicado o autorizado;
2. análisis de audiencia en forma agregada y señales de nicho/afinidad;
3. incorporación de resultados trazables a Radar de Clientes, con revisión humana.

La meta es aprender del flujo de producto mostrado, no replicar sin evaluación el método de extracción de la herramienta del vídeo.

## 2. Encaje arquitectónico

La capacidad pertenece a `AGENTE-LEADS` porque Radar de Clientes ya contempla descubrimiento de prospectos, enriquecimiento, evidencia por campo, normalización, deduplicación y scoring comercial.

Nombre provisional de capacidad: **Instagram Contact Enrichment & Audience Intelligence**.

No crear un agente independiente. No convertir esta capacidad en parte de Browser Factory: Browser Factory administra perfiles/sesiones y routing de destinos; no es el sistema responsable de investigación comercial. No depender de sesiones automatizadas de Instagram salvo que una evaluación posterior demuestre una necesidad concreta y un acceso permitido.

## 3. Módulos separados

### A. Business Contact Enrichment

- Entrada: URL de un perfil empresarial/creador o prospecto ya registrado.
- Fuentes admisibles: datos de contacto profesionales que el titular haya publicado para contacto comercial; sitio web oficial enlazado; formularios o páginas de contacto; APIs/proveedores con autorización y términos compatibles.
- Extraer y normalizar email/teléfono solo cuando exista una fuente admisible.
- Guardar la URL fuente exacta, fecha de consulta, método, evidencia, estado y nivel de confianza.
- No inferir correos personales a partir de patrones ni convertir la ausencia de un dato en prueba de que no existe.
- No enviar mensajes ni campañas automáticamente.

### B. Audience Intelligence

- Objetivo inicial: insights agregados y señales de mercado/nicho, por ejemplo categorías de audiencia disponibles legítimamente, intereses inferidos a nivel de segmento con etiqueta explícita de inferencia, temas frecuentes y oportunidades comerciales.
- Priorizar estadísticas agregadas, datos autorizados por el titular, datos facilitados con consentimiento o fuentes con permiso claro de reutilización.
- No construir por defecto un listado masivo de seguidores individuales con emails, teléfonos u otros datos personales extraídos.
- No asumir que un dato visible públicamente puede recopilarse a escala, combinarse o utilizarse para publicidad personalizada sin otra base legítima.
- Si una fuente solo permite métricas agregadas, el resultado debe permanecer agregado; no intentar reconstruir identidades individuales.

## 4. Contrato de evidencia

Reutilizar el modelo de evidencia de Radar de Clientes cuando sea compatible. Antes de implementar, confirmar el esquema real en el repositorio fuente y evitar una migración innecesaria.

Cada dato candidato debería registrar como mínimo:

- `prospectId` o referencia al negocio, si aplica;
- `field`: email, phone, website, audience_metric u otro campo tipado;
- `value`: dato obtenido, con acceso restringido cuando sea personal;
- `sourceUrl` y tipo de fuente;
- `retrievedAt`;
- `status`: FOUND, NOT_FOUND_IN_CHECKED_SOURCES, BLOCKED, NEEDS_REVIEW o INVALID;
- `confidence` y razón verificable;
- `collectionMethod` / proveedor y versión cuando corresponda;
- base de permiso/consentimiento o clasificación de fuente, cuando aplique;
- fecha de retención/eliminación si corresponde.

`NOT_FOUND_IN_CHECKED_SOURCES` significa que no se encontró en las fuentes consultadas; no significa que el negocio no tenga contacto. Los datos inferidos deben etiquetarse como inferencia y nunca presentarse como hecho.

## 5. Límites de privacidad, plataforma y seguridad

- Antes de habilitar cualquier conector, revisar las condiciones vigentes de Instagram/Meta, el permiso real de acceso y la normativa aplicable a la jurisdicción y al uso previsto.
- No eludir autenticación, controles de acceso, límites técnicos, mecanismos antibots ni restricciones de plataforma.
- No extraer masivamente datos personales de seguidores ni reutilizarlos para audiencias publicitarias sin autorización y base legal documentadas.
- No subir listas de emails/teléfonos a Meta Ads ni crear audiencias personalizadas automáticamente desde esta función.
- Aplicar minimización, acceso por roles, registro de consultas, límites de frecuencia/coste, retención definida y procedimiento de exclusión/eliminación.
- Mantener controles SSRF para URLs externas; validar esquema, DNS/direcciones de destino, redirecciones, timeouts, tamaño y tipos de contenido. Reutilizar el módulo SSRF existente si la auditoría confirma que cubre el caso.
- No registrar secretos ni exponer datos personales en logs, errores, telemetría o exportaciones no autorizadas.
- No usar cuentas personales, credenciales o sesiones de terceros sin autorización explícita.

## 6. Plan de entrega propuesto

### Fase 0 — Discovery y revisión de permisos

- Examinar en solo lectura el HEAD actual de AGENTE-LEADS y confirmar el esquema de `Evidence`, los fetchers y el pipeline real.
- Identificar fuentes/APIs permitidas, condiciones, costes, límites y cobertura.
- Documentar un análisis de impacto de privacidad y una matriz de fuentes/datos.
- No hacer scraping de producción ni llamadas pagadas durante esta fase.

### Fase 1 — MVP de contactos empresariales

- Entrada manual de perfiles empresariales o prospectos seleccionados.
- Solo fuentes de contacto profesionales publicadas/permitidas.
- Evidencia por campo, deduplicación, estados explícitos y revisión humana.
- Tests unitarios para parsing, normalización, asociación de dominio/negocio, SSRF, límites y fallos de proveedores.
- Sin mensajes, campañas, cargas de audiencias ni automatización de cuentas.

### Fase 2 — Integración con Radar de Clientes

- Incorporar resultados al prospecto y scoring solo cuando la señal tenga una regla determinista documentada.
- Mostrar procedencia, fecha y confianza en UI.
- Exportar únicamente conforme a los permisos existentes y al contrato de exportación.
- Bloquear contactos sin evidencia suficiente y datos marcados DEMO/MOCK.

### Fase 3 — Audience Intelligence agregado

- Solo después de confirmar fuentes y permisos.
- Mostrar estadísticas agregadas y distinguir FACT / INFERENCE / HYPOTHESIS / RECOMMENDATION.
- Validar calidad y sesgos de los segmentos.
- No producir listas masivas de emails/teléfonos de seguidores ni integrarse automáticamente con audiencias publicitarias.

## 7. Criterios de aceptación

La propuesta no se considera implementada hasta que exista evidencia verificable de todos los puntos aplicables:

1. Ningún dato de contacto aparece sin fuente y fecha.
2. Se diferencia `NOT_FOUND_IN_CHECKED_SOURCES` de un fallo/bloqueo de consulta.
3. Deduplicación y asociación al negocio están cubiertas por tests.
4. Los fetchers resisten SSRF, redirecciones maliciosas, timeouts y respuestas excesivas.
5. No se eluden restricciones ni se recolectan masivamente contactos individuales de seguidores.
6. No existe autoenvío de outreach ni carga automática a plataformas publicitarias.
7. Hay límites de frecuencia/coste, control de acceso, logging seguro y política de retención.
8. Typecheck, lint, build y tests pasan en la rama de trabajo; los resultados y commits quedan documentados.
9. No hay despliegue a producción sin autorización explícita.

## 8. Dependencias y restricciones

- D-0015 autoriza evolución de AGENTE-LEADS bajo condiciones, pero no autoriza despliegues de producción ni elimina la necesidad de verificar permisos de fuentes y datos.
- Durante este flujo, solo se modifica `ALBRA8/CONTEXT`; el repositorio fuente de AGENTE-LEADS queda en solo lectura.
- El constructor mantiene la responsabilidad de integración según el plan Radar de Clientes.
- Esta propuesta no aprueba una técnica concreta de scraping ni certifica que el vídeo obtenga los datos de forma legítima o fiable.

## 9. Próximo paso

Revisar y aprobar o rechazar el alcance de esta propuesta. Si se aprueba, abrir un flujo de trabajo autorizado para auditar el HEAD actual de AGENTE-LEADS, validar las fuentes permitidas y preparar una implementación acotada en una rama dedicada.

## Historial

- 0.1.0 — Propuesta inicial a partir del vídeo y la descripción del propietario; pendiente de aprobación.
