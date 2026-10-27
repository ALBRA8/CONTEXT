# ALBRA — Expansión de agentes de soporte empresarial

- Fecha de registro: 2026-10-27
- Estado: PROPOSED / USER-DECLARED INITIATIVE
- Autoridad: iniciativa expresada por el propietario de ALBRA
- Alcance de este documento: contexto institucional y arquitectura propuesta; no afirma que los agentes estén implementados, desplegados ni conectados.
- Repositorios de producto modificados: ninguno. Solo se actualiza `ALBRA8/CONTEXT`.

## 1. Contexto y propósito

El propietario está creando capacidades especializadas que una empresa necesita para sostener su operación: contabilidad, asuntos jurídicos y gestión de comunidad/marca. Se registran como una expansión del ecosistema ALBRA, manteniendo la separación entre agentes, capacidades, herramientas y sistemas de producto.

## 2. Líneas de trabajo

### 2.1 Agente Contador

**Estado:** PROPOSED / IN DEVELOPMENT (declarado por el propietario; implementación no verificada).

**Misión propuesta:** apoyar la organización contable y el control financiero de una empresa con trazabilidad, fuentes documentadas y supervisión adecuada.

**Capacidades objetivo por definir:**
- organizar ingresos, gastos, cuentas por cobrar y por pagar;
- preparar reportes de flujo de caja, presupuesto y variaciones;
- apoyar conciliaciones y seguimiento de soportes;
- controlar calendarios y alertas de obligaciones contables y tributarias;
- identificar inconsistencias y preparar preguntas para revisión profesional.

**Límites y controles:**
- separar datos fuente, cálculos, supuestos, conclusiones y recomendaciones;
- conservar procedencia y evidencia de cada cifra;
- no inventar comprobantes ni completar silenciosamente datos faltantes;
- no presentar resultados como asesoría contable/tributaria certificada sin revisión de un profesional competente;
- no presentar declaraciones, mover dinero ni ejecutar acciones financieras externas sin autorización explícita y controles apropiados;
- definir país, régimen contable/tributario, moneda y entidad antes de usar reglas específicas.

### 2.2 Agente / Despacho Jurídico

**Estado del ecosistema:** existe el repositorio `ALBRA8/DESPACHO-JURIDICO` como base técnica; el pool de especialistas y el corpus jurídico certificado no están verificados como completos.

**Objetivo de evolución:** continuar la especialización jurídica sin confundir arquitectura propuesta con capacidades ya implementadas.

**Capacidades objetivo por definir:**
- revisión preliminar de contratos y documentos;
- seguimiento de obligaciones, términos y renovaciones;
- identificación y clasificación de riesgos;
- preparación de borradores y expedientes con trazabilidad de fuentes;
- derivación a especialistas humanos cuando la materia o el impacto lo exijan.

**Límites y controles:**
- mantener la cadena SOURCE → EVIDENCE → CONCLUSION → ACTION;
- distinguir norma vigente, fuente primaria, evidencia, inferencia y recomendación;
- no utilizar corpus no verificado como autoridad jurídica operativa;
- exigir revisión humana para decisiones legales de alto impacto, compromisos vinculantes o presentación de documentos ante autoridades.

**Evidencia previa:** `03_AUDITS/DESPACHO_JURIDICO_REPOSITORY_AUDIT_2026-10-26.md` registra bloqueos P0 relacionados con CI/procedencia del corpus y declara que no se ejecutaron tests, lint, clean-room ni E2E de forma independiente en esa revisión.

### 2.3 Agente Community Manager

**Estado:** PROPOSED / USER-DECLARED INITIATIVE; ficha y repositorio específicos no identificados en el inventario revisado.

**Misión propuesta:** cuidar la voz pública, la comunidad, la reputación y la consistencia editorial de la empresa.

**Capacidades objetivo por definir:**
- estrategia editorial y calendario de contenidos;
- adaptación de mensajes a cada canal y audiencia;
- borradores de publicaciones y respuestas;
- clasificación y escalamiento de mensajes, quejas y riesgos reputacionales;
- análisis de métricas de comunidad y recomendaciones;
- coordinación con marketing y ventas sin asumir automáticamente sus funciones.

**Fronteras propuestas:**
- Community Manager: marca, publicaciones, conversación pública, comunidad y reputación.
- CRM / Leads: registros comerciales, prospección, oportunidades, seguimiento y atribución.
- Sistemas de campañas/marketing: audiencias, cadencias, distribución y automatización de campañas, según contratos vigentes.
- La publicación o respuesta sensible debe respetar permisos, políticas de canal, consentimiento y aprobaciones configurables.

## 3. Relación entre agentes y sistemas

Los tres dominios deben compartir convenciones de contexto, trazabilidad, permisos y auditoría, pero conservar sus propias fuentes de verdad y límites de autoridad.

- El Contador puede consumir información financiera autorizada desde sistemas fuente; no debe convertir el CRM en libro contable por defecto.
- El Despacho Jurídico revisa riesgos, contratos y obligaciones; no reemplaza al contador ni a la aprobación de la dirección.
- Community Manager coordina con CRM/Leads y herramientas de marketing mediante contratos explícitos; no duplica bases comerciales ni crea integraciones paralelas sin decisión arquitectónica.
- AGENTE-GENERAL sigue siendo el Seed/Host transversal y no se convierte automáticamente en jefe de los especialistas.
- Esta ficha no concede permisos de ejecución ni autoriza cambios en repositorios de producto.

## 4. Entregables siguientes

1. Identificar los repositorios y ramas reales de los desarrollos de Contador y Community Manager.
2. Leer su README, contratos, estructura, pruebas y estado de seguridad antes de afirmar capacidades.
3. Crear o completar fichas canónicas por agente con misión, entradas/salidas, herramientas, límites, permisos y criterios de aceptación.
4. Definir contratos de integración con CRM y otros agentes solo cuando haya evidencia de necesidad.
5. Definir pruebas de aceptación separando BUILT, TESTED, AUDITED, CONNECTED y E2E VERIFIED.
6. No usar datos reales sensibles, presentar declaraciones, emitir comunicaciones públicas ni ejecutar acciones financieras/jurídicas externas durante pruebas sin autorización explícita.

## 5. Evidencia y estado

- La intención de desarrollar los tres dominios procede de la declaración del propietario del 2026-10-27.
- La existencia de una base técnica de Despacho Jurídico y sus limitaciones se toma de la ficha y auditoría de CONTEXT.
- La existencia de agentes Contador y Community Manager ya implementados no queda establecida por este documento.
- No se ejecutaron pruebas ni se modificaron repositorios de producto en esta actualización.
