# Estándares y Normas de Desarrollo de Software - E2E SOLUTIONS MX

> Extracto de: *Estándares y Normas de Desarrollo de Software*, Dirección de Tecnología, E2E SOLUTIONS. 
> Clasificación: Confidencial | Versión 1.0 | Mayo 2026

---

## 2. Principios de Desarrollo

Todo el desarrollo en el E2E SOLUTIONS se rige por los siguientes principios no negociables:

| # | Principio | Descripción |
|---|-----------|-------------|
| P1 | Security by Design | La seguridad es un requerimiento, no un complemento. Se modela desde la fase de diseño. |
| P2 | Privacy by Default | Los datos personales se protegen por defecto, minimizando su recolección y exposición (LFPDPPP). |
| P3 | Compliance First | Ningún release llega a producción sin validación de cumplimiento regulatorio. |
| P4 | Clean Code | El código es legible, simple y mantenible. La complejidad innecesaria está prohibida. |
| P5 | You Build It, You Run It | El equipo que construye el servicio es responsable de su operación y monitoreo. |
| P6 | API-First | Cada capacidad se expone primero como API bien documentada antes de construir la UI. |
| P7 | Infrastructure as Code | Toda la infraestructura se define y versiona como código. |
| P8 | Fail Fast, Recover Fast | Los sistemas deben detectar y recuperarse de fallos de forma autónoma. |
| P9 | Shift Left Testing | Las pruebas inician desde el diseño, no al final del ciclo. |
| P10 | Trazabilidad Total | Todo cambio debe ser trazable a un requerimiento, usuario y ticket aprobado. |

---

## 3.2 Definition of Ready (DoR)

Una Historia de Usuario **DEBE** cumplir todos estos criterios antes de ser tomada en una iteración:

- Criterios de aceptación escritos en formato Gherkin (Given / When / Then).
- Estimación de Story Points acordada por el equipo.
- Dependencias externas identificadas y comprometidas.
- Mockups o wireframes aprobados (para funcionalidades de UI).
- Análisis de impacto regulatorio realizado (CNBV / PCI-DSS / LFPDPPP).
- Campos de datos personales identificados y catalogados.
- Requerimientos de seguridad no-funcionales documentados.

---

## 3.3 Definition of Done (DoD)

Una Historia de Usuario se considera **DONE** únicamente cuando cumple **TODOS** los siguientes criterios:

| Categoría | Criterio | Responsable |
|-----------|----------|-------------|
| Código | Pasa Code Review con al menos 2 aprobaciones (1 Senior). | Dev + Senior |
| Código | Cumple con los estándares de código de este documento. | Dev |
| Código | Sin deuda técnica no registrada en el backlog. | Tech Lead |
| Pruebas | Cobertura de pruebas unitarias >= 80% en nuevos módulos. | Dev + QA |
| Pruebas | Pruebas de integración y contrato ejecutadas satisfactoriamente. | QA |
| Seguridad | SAST y análisis de dependencias sin vulnerabilidades CRÍTICAS/ALTAS. | DevSecOps |
| Seguridad | Secrets, tokens o credenciales ausentes del código fuente. | Dev + DevSecOps |
| Regulatorio | Datos PAN/CVV/PII manejados conforme a controles PCI-DSS. | Dev + CISO |
| Documentación | Swagger/OpenAPI actualizado para cambios en APIs. | Dev |
| Documentación | README y ADR (Architecture Decision Record) actualizados. | Tech Lead |
| Despliegue | Pipeline CI/CD ejecutado sin errores en ambiente de staging. | DevSecOps |
| Negocio | Criterios de aceptación validados con el Product Owner. | PO + QA |

---

## 4.1 Estructura de Ramas (GitFlow)

El E2E SOLUTIONS adopta **GitFlow** como estrategia oficial de ramificación. Toda la base de código se aloja en repositorios corporativos bajo control de acceso estricto basado en roles (RBAC).

| Rama | Protegida | Propósito | Origen | Destino |
|------|-----------|-----------|--------|---------|
| `main` | Sí | Código en producción. Solo merges vía release o hotfix. | `release/*`, `hotfix/*` | — |
| `qa` | Sí | Integración continua de features. Base de staging. | `feature/*`, `bugfix/*` | `main` |
| `development` | Sí | Integración continua de features. Base de Desarrollo. | `feature/*`, `bugfix/*` | `qa` |
| `feature/TICKET-desc` | No | Desarrollo de una feature o HU específica. | `development` | `development` |
| `bugfix/TICKET-desc` | No | Corrección de bug no crítico en develop. | `development` | `development` |
| `release/vX.Y.Z` | Sí | Preparación del release: solo bug fixes y docs. | `qa` | `main` |
| `hotfix/TICKET-desc` | Sí | Corrección urgente en producción. | `main` | `main` |

### Convención de Nomenclatura de Ramas

Formato obligatorio: `<tipo>/<JIRA-ID>-<descripcion-en-kebab-case>`

- `feature/PAY-1234-integracion-spei-beneficiarios`
- `bugfix/AUTH-567-token-expiracion-incorrecta`
- `hotfix/SEC-890-vulnerabilidad-inyeccion-sql`
- `release/v2.4.0`

---

## 4.3 Convención de Commits (Conventional Commits)

Todos los commits deben seguir el estándar **Conventional Commits**.

**Formato de Commit:**

```
<tipo>(<alcance>): <descripcion-corta>

[cuerpo opcional — explica el QUÉ y el POR QUÉ, no el CÓMO]

[footer opcional — BREAKING CHANGE, Refs: JIRA-123, Closes: JIRA-456]
```

| Tipo | Uso |
|------|-----|
| `feat` | Nueva funcionalidad (genera entrada en CHANGELOG). |
| `fix` | Corrección de bug. |
| `docs` | Solo cambios en documentación. |
| `style` | Formato, espacios, punto y coma (sin cambio de lógica). |
| `refactor` | Refactoring sin fix ni feature. |
| `test` | Adición o corrección de pruebas. |
| `chore` | Tareas de mantenimiento (CI, dependencias). |
| `perf` | Mejoras de rendimiento. |
| `security` | Parche de seguridad (CRÍTICO: siempre con referencia al CVE/ticket). |
| `revert` | Reversión de un commit anterior. |

---

## 4.4 Reglas de Pull Request (PR) / Merge Request (MR)

- Mínimo 2 aprobaciones requeridas: 1 peer developer + 1 tech lead o senior.
- El autor del PR **NO** puede aprobarlo ni mergearlo.
- El PR debe referenciar el ticket de JIRA: `Refs: PAY-1234`.
- La descripción del PR debe incluir: Qué cambió, Por qué, Cómo probar, Capturas de pantalla (si aplica UI).
- Los comentarios del revisor deben resolverse antes del merge. No se cierra un comentario sin respuesta.
- El pipeline CI/CD debe estar en verde antes de que sea posible el merge.
- Los PRs abiertos por más de 5 días hábiles sin actividad se escalan al Tech Lead.
- Prohibido el force push a ramas protegidas (`main`, `develop`, `release/*`).

---

## 5.3 Frontend — Next.js y React

### 5.3.1 Convenciones

- **Componentes:** PascalCase en archivos y en la exportación (`TransferenciaForm.tsx`).
- **Hooks personalizados:** prefijo `use` (`useTransferencia`, `useSaldo`).
- **Constantes:** UPPER_SNAKE_CASE en archivos de constantes separados.
- **Variables y funciones:** camelCase (`handleSubmit`, `montoTransferencia`).
- **Archivos de estilos:** mismo nombre que el componente + `.module.css`.

### 5.3.2 Reglas de Calidad

- TypeScript estricto obligatorio (`strict: true` en `tsconfig.json`). Prohibido el uso de `any`.
- ESLint con ruleset bancario estándar + plugin de accesibilidad (`jsx-a11y`).
- Prettier configurado con `prettier.config.js` versionado.
- Componentes React deben ser funcionales. Prohibidas las class components en código nuevo.
- Límite de 200 líneas por componente. Extraer sub-componentes si se supera.
- Manejo de estado: usar Zustand o Context API para estado global. Prohibido Redux en proyectos nuevos.
- Imágenes y activos estáticos deben pasar por Next.js Image Optimization.
- Implementar lazy loading para rutas y componentes pesados (dynamic import).

### 5.3.3 Seguridad en Frontend

- Nunca almacenar tokens JWT, PAN, CVV o datos sensibles en `localStorage` o `sessionStorage`.
- Usar HttpOnly cookies para tokens de sesión.
- Sanitizar toda entrada de usuario. Nunca usar `dangerouslySetInnerHTML`.
- Implementar Content Security Policy (CSP) estricto en las cabeceras HTTP.
- Usar `next/headers` para configurar cabeceras de seguridad (`X-Frame-Options`, `HSTS`).

---

*Fuente: Estándares y Normas de Desarrollo de Software, E2E SOLUTIONS — Dirección de Tecnología, v1.0, mayo de 2026. Documento confidencial de uso interno.*