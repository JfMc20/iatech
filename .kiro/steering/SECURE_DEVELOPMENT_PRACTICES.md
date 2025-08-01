# Prácticas de Desarrollo Seguro - IATECH

## 🎯 OBJETIVO

Establecer prácticas obligatorias de desarrollo para prevenir vulnerabilidades de seguridad, especialmente inyección NoSQL, en el backend de IATECH.

## 🚨 REGLAS CRÍTICAS - CUMPLIMIENTO OBLIGATORIO

### 1. VALIDACIÓN DE ENTRADA - SIEMPRE

#### ✅ PATRÓN OBLIGATORIO para todos los endpoints:
```typescript
// PASO 1: Validar tipo y existencia
if (!input || typeof input !== 'string') {
  return res.status(400).json({
    success: false,
    error: 'Invalid input',
    message: 'Input must be a valid string'
  });
}

// PASO 2: Sanitizar
const sanitizedInput = input.toString().trim();

// PASO 3: Validar formato específico
if (!isValidFormat(sanitizedInput)) {
  return res.status(400).json({
    success: false,
    error: 'Invalid format',
    message: 'Input format is invalid'
  });
}

// PASO 4: Usar en consulta
const result = await Model.findOne({ field: sanitizedInput });
```

### 2. MIDDLEWARE DE SEGURIDAD - OBLIGATORIO

#### ✅ En TODAS las rutas:
```typescript
import { sanitizeQuery, validateMongoId } from '../middleware/sanitization';

const router = express.Router();

// OBLIGATORIO: Sanitización global
router.use(sanitizeQuery);

// OBLIGATORIO: Validación de IDs en rutas con parámetros
router.get('/:id', validateMongoId('id'), handler);
router.put('/:id', validateMongoId('id'), handler);
router.delete('/:id', validateMongoId('id'), handler);
```

### 3. ESQUEMAS ZOD - ENDPOINTS CRÍTICOS

#### ✅ Para endpoints que manejan datos sensibles:
```typescript
import { z } from 'zod';
import { validateBody, validateParams } from '../validation/schemas';

// Definir esquema
const createUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
  role: z.enum(['user', 'admin'])
});

// Aplicar validación
router.post('/users', validateBody(createUserSchema), handler);
```

## 🔍 CHECKLIST PRE-COMMIT

### Antes de hacer commit, verificar:

#### ✅ Consultas MongoDB
- [ ] ¿Todas las consultas `findOne()` tienen validación de parámetros?
- [ ] ¿Todas las consultas `find()` validan query parameters?
- [ ] ¿Todos los `findById()` validan el ID como ObjectId?
- [ ] ¿No hay construcción dinámica de queries sin validación?

#### ✅ Validación de Entrada
- [ ] ¿Todos los `req.params` están validados?
- [ ] ¿Todos los `req.query` están sanitizados?
- [ ] ¿Todos los `req.body` tienen esquemas de validación?
- [ ] ¿Los emails están siendo sanitizados y validados?

#### ✅ Middleware Aplicado
- [ ] ¿`sanitizeQuery` está aplicado en la ruta?
- [ ] ¿`validateMongoId` está en rutas con parámetros ID?
- [ ] ¿Validación Zod está en endpoints críticos?
- [ ] ¿Rate limiting está configurado donde es necesario?

#### ✅ Tests de Seguridad
- [ ] ¿Hay tests para inyección NoSQL?
- [ ] ¿Hay tests para validación de parámetros?
- [ ] ¿Hay tests para sanitización de query parameters?
- [ ] ¿Los tests cubren casos edge?

## 🛠️ HERRAMIENTAS DE DESARROLLO

### 1. Scripts NPM Obligatorios

#### Agregar a package.json:
```json
{
  "scripts": {
    "test:security": "jest src/test/security.test.ts",
    "lint:security": "eslint src/**/*.ts --config .eslintrc.security.js",
    "audit:security": "npm audit && node scripts/security-audit.js",
    "build:secure": "npm run lint:security && npm run test:security && npm run build"
  }
}
```

### 2. Pre-commit Hooks

#### Configurar en .husky/pre-commit:
```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

# Ejecutar tests de seguridad
npm run test:security

# Verificar que no hay consultas inseguras
node scripts/check-unsafe-queries.js

# Lint de seguridad
npm run lint:security
```

### 3. ESLint Rules de Seguridad

#### Crear .eslintrc.security.js:
```javascript
module.exports = {
  rules: {
    // Prohibir consultas MongoDB sin validación
    'no-unsafe-mongodb-queries': 'error',
    // Requerir validación de ObjectIds
    'require-objectid-validation': 'error',
    // Prohibir construcción dinámica de queries
    'no-dynamic-query-construction': 'error'
  }
};
```

## 📊 MONITOREO Y ALERTAS

### 1. Logging de Seguridad

#### Implementar en todos los endpoints:
```typescript
import logger from '../config/logger';

// Log intentos de inyección
const logSecurityEvent = (req: Request, eventType: string, details: any) => {
  logger.warn('Security event detected', {
    type: eventType,
    ip: req.ip,
    userAgent: req.get('User-Agent'),
    url: req.originalUrl,
    method: req.method,
    details,
    timestamp: new Date().toISOString()
  });
};

// Usar en middleware de sanitización
if (typeof value === 'object' && value !== null) {
  logSecurityEvent(req, 'NoSQL_INJECTION_ATTEMPT', { parameter: key, value });
  return res.status(400).json({
    success: false,
    error: 'Invalid query parameter format'
  });
}
```

### 2. Métricas de Seguridad

#### Dashboard de seguridad debe mostrar:
- Intentos de inyección NoSQL por día/hora
- IPs que intentan ataques repetidamente
- Endpoints más atacados
- Tipos de payloads maliciosos más comunes
- Tasa de éxito/fallo de ataques

### 3. Alertas Automáticas

#### Configurar alertas para:
- Más de 10 intentos de inyección desde la misma IP en 1 hora
- Intentos de inyección en endpoints críticos
- Patrones de ataque conocidos
- Fallos de validación masivos

## 🎓 CAPACITACIÓN DEL EQUIPO

### 1. Onboarding de Seguridad

#### Todo nuevo desarrollador debe:
- [ ] Leer y entender `SECURITY_GUIDELINES.md`
- [ ] Completar tutorial de inyección NoSQL
- [ ] Revisar casos de vulnerabilidades corregidas
- [ ] Practicar con tests de seguridad
- [ ] Pasar quiz de seguridad (80% mínimo)

### 2. Revisiones de Código

#### Checklist para code reviewers:
- [ ] ¿Hay consultas MongoDB sin validación?
- [ ] ¿Los parámetros de entrada están validados?
- [ ] ¿El middleware de seguridad está aplicado?
- [ ] ¿Hay tests de seguridad para los cambios?
- [ ] ¿Los mensajes de error no revelan información sensible?

### 3. Sesiones de Seguridad Regulares

#### Agenda mensual:
- Revisión de nuevas vulnerabilidades
- Análisis de intentos de ataque
- Actualización de herramientas de seguridad
- Práctica con escenarios de ataque
- Mejoras en procesos de seguridad

## 🔧 AUTOMATIZACIÓN

### 1. CI/CD Pipeline

#### Stages obligatorios:
```yaml
security_checks:
  stage: security
  script:
    - npm run test:security
    - npm run audit:security
    - npm run lint:security
    - node scripts/check-vulnerabilities.js
  only:
    - merge_requests
    - main
```

### 2. Análisis Estático

#### Herramientas a integrar:
- **SonarQube**: Análisis de código para vulnerabilidades
- **Snyk**: Análisis de dependencias
- **ESLint Security**: Rules específicas de seguridad
- **Semgrep**: Detección de patrones inseguros

### 3. Tests Automatizados

#### Ejecutar en cada PR:
- Tests de inyección NoSQL
- Tests de validación de entrada
- Tests de sanitización
- Tests de autorización
- Tests de rate limiting

## 📚 DOCUMENTACIÓN OBLIGATORIA

### 1. Para cada endpoint nuevo:

#### Documentar en el código:
```typescript
/**
 * GET /api/users/:id
 * 
 * SECURITY:
 * - ID validated as MongoDB ObjectId
 * - Rate limited to 100 requests/hour
 * - Requires authentication
 * - Logs access attempts
 * 
 * VALIDATION:
 * - req.params.id: MongoDB ObjectId (required)
 * - Authorization header: JWT token (required)
 */
router.get('/:id', 
  validateMongoId('id'),
  authenticate,
  rateLimit({ max: 100, windowMs: 3600000 }),
  getUserById
);
```

### 2. Actualizar documentación de API:

#### Incluir sección de seguridad:
```markdown
## Security Considerations

### Input Validation
All endpoints validate input parameters and reject malicious payloads.

### Rate Limiting
Public endpoints are rate limited to prevent abuse.

### Authentication
Protected endpoints require valid JWT tokens.

### Logging
All security events are logged for monitoring.
```

## 🚀 DEPLOYMENT SEGURO

### 1. Checklist Pre-Deploy

#### Verificar antes de cada deploy:
- [ ] Tests de seguridad pasando al 100%
- [ ] Auditoría de dependencias sin vulnerabilidades críticas
- [ ] Rate limiting configurado correctamente
- [ ] HTTPS configurado y funcionando
- [ ] Headers de seguridad configurados
- [ ] Logs de seguridad funcionando
- [ ] Monitoreo de seguridad activo

### 2. Configuración de Producción

#### Variables de entorno obligatorias:
```bash
# Seguridad
SECURITY_STRICT_MODE=true
LOG_SECURITY_EVENTS=true
RATE_LIMIT_ENABLED=true
HTTPS_ONLY=true

# Monitoreo
SECURITY_MONITORING_ENABLED=true
ALERT_WEBHOOK_URL=https://alerts.company.com/webhook
LOG_LEVEL=warn

# Límites
MAX_REQUEST_SIZE=1mb
MAX_ARRAY_SIZE=100
REQUEST_TIMEOUT=30s
```

## 🔄 PROCESO DE INCIDENT RESPONSE

### 1. Detección de Vulnerabilidad

#### Pasos inmediatos:
1. **Aislar**: Identificar alcance del problema
2. **Contener**: Aplicar fix temporal si es posible
3. **Evaluar**: Determinar impacto y riesgo
4. **Comunicar**: Notificar al equipo y stakeholders
5. **Documentar**: Registrar todos los detalles

### 2. Corrección de Vulnerabilidad

#### Proceso obligatorio:
1. **Desarrollar fix**: Siguiendo guías de seguridad
2. **Probar fix**: Tests exhaustivos de seguridad
3. **Code review**: Revisión por al menos 2 personas
4. **Deploy urgente**: Con monitoreo intensivo
5. **Verificar**: Confirmar que vulnerabilidad está cerrada

### 3. Post-Incident

#### Acciones de seguimiento:
1. **Post-mortem**: Análisis de causa raíz
2. **Mejoras**: Actualizar procesos y herramientas
3. **Capacitación**: Entrenar equipo en lecciones aprendidas
4. **Documentación**: Actualizar guías de seguridad
5. **Monitoreo**: Vigilancia aumentada por período extendido

---

## ⚠️ RECORDATORIO FINAL

### PRINCIPIOS FUNDAMENTALES:

1. **NUNCA CONFIAR EN INPUT DEL USUARIO**
2. **VALIDAR TODO, SIEMPRE**
3. **APLICAR DEFENSA EN PROFUNDIDAD**
4. **LOGGEAR EVENTOS DE SEGURIDAD**
5. **MANTENER DOCUMENTACIÓN ACTUALIZADA**

### MANTRA DEL DESARROLLADOR:

> "¿Este código es seguro contra inyección NoSQL?"
> "¿Estoy validando todos los inputs?"
> "¿Tengo tests de seguridad para esto?"

**La seguridad es responsabilidad de TODOS los desarrolladores.**

---

**Estado**: ✅ ACTIVO Y OBLIGATORIO
**Aplicación**: Todos los desarrolladores del equipo IATECH
**Revisión**: Mensual o después de cada incidente de seguridad