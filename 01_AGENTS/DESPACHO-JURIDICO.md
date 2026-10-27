# DESPACHO JURÍDICO DIGITAL ALBRA — FICHA DE CONTEXTO

- Repositorio: `ALBRA8/DESPACHO-JURIDICO`
- Estado: IMPLEMENTED BASE / DOMAIN KNOWLEDGE NOT LOADED (according to README)
- Tipo: organismo jurídico especializado.

## Misión

Núcleo inteligente de un despacho jurídico, construido como organismo ALBRA preparado para incorporar conocimiento jurídico y subagentes especializados progresivamente.

## Lo que el repositorio declara NO tener en la versión base

- conocimiento jurídico cargado (normas, jurisprudencia, doctrina);
- subagentes especializados;
- integraciones externas activas.

## Arquitectura documentada

El repositorio declara 30 órganos ALBRA operativos y arquitectura para:

- identidad y runtime;
- razonamiento por capas;
- tools/capabilities;
- MCP/MSP server + client;
- memorydv con seis categorías;
- Doctor;
- multimodalidad y lectura/generación de documentos;
- comunicación WhatsApp/Telegram/texto/voz;
- research con fuentes oficiales;
- evidence + provenance;
- seguridad, permisos y HITL;
- workflows;
- handoffs;
- providers desacoplados;
- resiliencia/observabilidad/auditoría.

## Principios jurídicos/operativos recuperados

- GitHub es la fuente de verdad del código.
- El Constructor es quien modifica código.
- Conocimiento jurídico y código permanecen separados.
- No se inventan APIs, credenciales ni integraciones.
- Acciones sensibles requieren aprobación humana.
- Trazabilidad `SOURCE → EVIDENCE → CONCLUSION → ACTION`.
- Separación de DATOS, FUENTES, EVIDENCIA, INFERENCIAS, CONCLUSIONES y ACCIONES.

## Evolución propuesta en la conversación

El usuario decidió que, si el dominio legal crece, la estructura debe ser un pool/despacho de especialistas jurídicos, evitando sobrecargar Krea, Chismoso o Leads.

Especialistas propuestos durante la conversación:

- Director Jurídico.
- IP/Copyright.
- Legal Case / Contract.
- Evidence / Provenance.
- Legal Gate.

Estos nombres representan arquitectura propuesta en conversación; no deben confundirse con subagentes ya implementados en el repositorio base.

## Estado

El repositorio es evidencia directa de una base jurídica preparada para especialización, pero no demuestra por sí solo que todo el pool de abogados propuesto esté implementado.


## Reauditoría estática del repositorio — 2026-10-26

- HEAD revisado: `277be56540dd7298f3ebc6a5654c2c7aaa1ba0c5`.
- Informe canónico: [`DESPACHO_JURIDICO_REPOSITORY_AUDIT_2026-10-26.md`](../03_AUDITS/DESPACHO_JURIDICO_REPOSITORY_AUDIT_2026-10-26.md).
- Dictamen documental: base de arquitectura amplia, pero estado **PARTIAL / NOT VERIFIED INDEPENDENTLY BY TEST EXECUTION**.
- Bloqueos P0: CI/validación del workflow y trazabilidad/procedencia del corpus piloto.
- La construcción de `raw_content` desde fragmentos Python no demuestra que el hash corresponda a bytes originales de HTML oficial. Corpus no apto para considerarse certificado hasta reconciliar fuentes primarias y consumidores.
- No se ejecutaron localmente tests, lint ni clean-room; integraciones externas E2E y preparación de producción siguen sin verificar.
- El pool de especialistas jurídicos y la guía de agente contador continúan siendo propuestas; no equivalen a subagentes desplegados ni a una especificación contable aprobada.
