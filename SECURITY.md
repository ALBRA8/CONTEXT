# Seguridad de ALBRA-CONTEXT

- Estado: PROPOSED
- Alcance: archivos, commits, referencias y procesos documentales de este repositorio público.

## 1. Regla principal

Trata todo contenido de este repositorio como públicamente visible. No incluyas secretos ni información que no deba divulgarse.

## 2. Información que nunca debe publicarse

- Contraseñas, tokens de acceso, claves API, claves privadas y cookies de sesión.
- Cadenas de conexión con credenciales y archivos de configuración secretos.
- Datos personales sensibles o información confidencial de terceros.
- Volcados de entorno, logs o capturas que puedan revelar credenciales o datos privados.
- Copias de secretos dentro de ejemplos, prompts, pruebas o historial de commits.

Usa marcadores ficticios como `YOUR_API_TOKEN` en ejemplos. No uses credenciales reales aunque estén temporalmente desactivadas.

## 3. Antes de cada commit

1. Revisa los archivos modificados y el diff completo.
2. Busca accidentalmente tokens, claves, contraseñas, datos personales y rutas privadas.
3. Comprueba que los enlaces y evidencias no revelen información restringida.
4. Verifica que el cambio esté dentro del alcance autorizado.
5. No declares que se ejecutó una auditoría de secretos si solo se hizo una revisión manual.

## 4. Si se publica un secreto por accidente

1. Revoca o rota el secreto inmediatamente; borrar el archivo no invalida el secreto.
2. Informa al propietario y a los responsables del sistema afectado por un canal seguro.
3. Retira el contenido del repositorio y evalúa el historial Git, forks, cachés y copias.
4. Registra el incidente sin volver a copiar el secreto en el informe.
5. Verifica la mitigación y documenta el resultado sin exponer valores sensibles.

## 5. Integraciones y permisos

- Usa el menor nivel de permisos necesario.
- No compartas tokens entre personas o servicios si no es necesario.
- No pegues credenciales en conversaciones, issues o pull requests.
- Revisa periódicamente aplicaciones autorizadas, permisos y accesos.
- Documenta contratos y nombres de capacidades, nunca sus secretos.

## 6. Protección del Seed

El Seed congelado de `AGENTE-GENERAL` queda fuera del alcance de modificación de ALBRA-CONTEXT. Ningún cambio en este repositorio autoriza editarlo, regenerarlo o sustituirlo.

## 7. Reporte de incidentes

Reporta incidentes al propietario del repositorio mediante un canal privado y seguro. No publiques detalles explotables ni secretos en issues públicos. Si no existe un canal privado definido, solicita uno antes de enviar información sensible.

## 8. Alcance y estado

Este documento establece reglas preventivas iniciales; no certifica que el repositorio o sus integraciones hayan pasado una auditoría de seguridad. Las verificaciones deben registrar fecha, alcance, evidencia y limitaciones.