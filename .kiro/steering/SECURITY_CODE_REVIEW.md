# Security Code Review Guidelines - IATECH

## 🎯 OBJETIVO

Establecer un proceso riguroso de revisión de código enfocado en seguridad para prevenir vulnerabilidades, especialmente inyección NoSQL, en el backend de IATECH.

## 🚨 CHECKLIST OBLIGATORIO - CODE REVIEW

### 🔍 REVISIÓN DE CONSULTAS MONGODB

#### ❌ RECHAZAR INMEDIATAMENTE si encuentras:
```typescript
// ❌ PROHIBIDO - Consultas sin validación
const user = await User.findOne({ email });
const data = await Model.find(req.query);
const result = await Collection.findById(req.params.id);

// ❌ PROHIBIDO - Construcción dinámica de queries
const query: any = {};
if (category) query.category = category;
const results = await Model.find(query);

// ❌ PROHIBIDO - Pasar directamente parámetros del request
const assessment = await BusinessAssessment.findOne({ quizResponseId });
```

#### ✅ APROBAR SOLO si ves:
```typescript
// ✅ CORRECTO - Validación completa
if (!email || typeof email !== 'string') {
  return res.status(400).json({ error: 'Invalid email' });
}
const sanitizedEmail = email.toString().trim().toLowerCase();
const user = await User.findOne({ email: sanitizedEmail });

// ✅ CORRECTO - Validación de ObjectId
const sanitizedId = validateObjectId(id, 'User ID');
const result = await Model.findById(sanitizedId);
```

### 🛡️ REVISIÓN DE MIDDLEWARE

#### ✅ VERIFICAR que esté presente:
```typescript
// OBLIGATORIO en todas las rutas
import { sanitizeQuery, validateMongoId } from '../middleware/sanitization';

const router = express.Router();
router.use(sanitizeQuery); // ← DEBE ESTAR PRESENTE

// OBLIGATORIO en rutas con parámetros ID
router.get('/:id', validateMongoId('id'), handler); // ← DEBE ESTAR PRESENTE
```

#### ❌ RECHAZAR si falta:
- Middleware `sanitizeQuery` en rutas nuevas
- Validación `validateMongoId` en rutas con parámetros `:id`
- Validación Zod en endpoints que manejan datos sensibles

### 📝 REVISIÓN DE VALIDACIÓN ZOD

#### ✅ APROBAR si endpoints críticos tienen:
```typescript
// OBLIGATORIO para endpoints sensibles
import { validateBody, validateParams } from '../validation/schemas';

router.post('/users', validateBody(createUserSchema), handler);
router.get('/assessment/:id', validateParams(getAssessmentSchema), handler);
```

#### ❌ RECHAZAR si endpoints críticos NO tienen:
- Esquemas Zod definidos
- Validación aplicada en middleware
- Manejo de errores de validación

### 🔐 REVISIÓN DE AUTENTICACIÓN

#### ✅ VERIFICAR que tokens JWT sean validados:
```typescript
// CORRECTO - Validación completa de JWT
const decoded = jwt.verify(token, JWT_SECRET);
if (!decoded.userId || !mongoose.Types.ObjectId.isValid(decoded.userId)) {
  return res.status(401).json({ error: 'Invalid token' });
}
const sanitizedUserId = new mongoose.Types.ObjectId(decoded.userId);
```

#### ❌ RECHAZAR validación insuficiente:
```typescript
// ❌ PROHIBIDO - Sin validación del payload
const decoded = jwt.verify(token, JWT_SECRET);
const user = await User.findById(decoded.userId); // ← VULNERABLE
```

## 🧪 REVISIÓN DE TESTS

### ✅ VERIFICAR que existan tests para:
```typescript
// Tests de seguridad OBLIGATORIOS
describe('Security Tests', () => {
  it('should reject NoSQL injection in parameters');
  it('should reject objects in query parameters');
  it('should validate ObjectId format');
  it('should sanitize request body');
});
```

### ❌ RECHAZAR PR sin:
- Tests de inyección NoSQL para endpoints nuevos
- Tests de validación de parámetros
- Tests de casos edge de seguridad
- Cobertura de tests >80% en código de seguridad

## 📊 REVISIÓN DE LOGGING

### ✅ APROBAR si hay logging de seguridad:
```typescript
// OBLIGATORIO - Log eventos de seguridad
logger.warn('Security event detected', {
  type: 'NoSQL_INJECTION_ATTEMPT',
  ip: req.ip,
  userAgent: req.get('User-Agent'),
  payload: suspiciousData,
  timestamp: new Date()
});
```

### ❌ RECHAZAR si falta:
- Logging de intentos de inyección
- Información de contexto (IP, User-Agent)
- Timestamp y tipo de evento
- Detalles del payload malicioso

## 🔧 PROCESO DE REVISIÓN

### 1. REVISIÓN AUTOMÁTICA (Pre-requisitos)
```bash
# Debe pasar ANTES de revisión humana
npm run test:security     # Tests de seguridad
npm run lint:security     # ESLint de seguridad
npm run audit:security    # Auditoría de dependencias
```

### 2. REVISIÓN MANUAL (Checklist)

#### Para CADA archivo modificado:
- [ ] ¿Hay consultas MongoDB nuevas?
  - [ ] ¿Están todas validadas?
  - [ ] ¿Usan ObjectId correctamente?
  - [ ] ¿No hay construcción dinámica insegura?

- [ ] ¿Hay nuevos endpoints?
  - [ ] ¿Tienen middleware de sanitización?
  - [ ] ¿Validan parámetros de entrada?
  - [ ] ¿Tienen esquemas Zod si es crítico?

- [ ] ¿Hay manejo de autenticación?
  - [ ] ¿Validan tokens JWT correctamente?
  - [ ] ¿Verifican roles y permisos?
  - [ ] ¿Manejan errores de auth apropiadamente?

- [ ] ¿Hay tests de seguridad?
  - [ ] ¿Cubren casos de inyección NoSQL?
  - [ ] ¿Prueban validación de entrada?
  - [ ] ¿Incluyen casos edge?

### 3. REVISIÓN DE DOCUMENTACIÓN

#### ✅ VERIFICAR que esté actualizada:
- Documentación de API con consideraciones de seguridad
- Comentarios en código explicando validaciones
- README con instrucciones de seguridad
- Changelog con cambios de seguridad

## 🚫 CRITERIOS DE RECHAZO INMEDIATO

### ❌ RECHAZAR automáticamente si:
1. **Consulta MongoDB sin validación** - Cualquier `findOne()`, `find()`, `findById()` sin validar parámetros
2. **Construcción dinámica de query** - Objetos query construidos con input del usuario
3. **Falta middleware de sanitización** - Rutas nuevas sin `sanitizeQuery`
4. **Sin tests de seguridad** - Endpoints nuevos sin tests de inyección NoSQL
5. **Validación JWT insuficiente** - Tokens no validados apropiadamente
6. **Logging de seguridad faltante** - No registra eventos de seguridad

### ⚠️ SOLICITAR CAMBIOS si:
1. **Validación incompleta** - Valida algunos pero no todos los parámetros
2. **Mensajes de error informativos** - Revelan información del sistema
3. **Tests insuficientes** - Cobertura <80% en código de seguridad
4. **Documentación faltante** - Sin comentarios de seguridad

## 👥 ROLES Y RESPONSABILIDADES

### 🔍 REVIEWER (Obligatorio)
- **Mínimo 2 reviewers** para cambios de seguridad
- **Al menos 1 reviewer** debe ser senior/lead
- **Conocimiento obligatorio** de guías de seguridad IATECH
- **Certificación** en revisión de código de seguridad

### 👨‍💻 DEVELOPER (Responsabilidades)
- **Self-review** antes de crear PR
- **Ejecutar tests** de seguridad localmente
- **Documentar** consideraciones de seguridad
- **Responder** a comentarios de seguridad rápidamente

### 🛡️ SECURITY CHAMPION (Escalación)
- **Revisión final** para cambios críticos de seguridad
- **Decisión** en casos de desacuerdo
- **Capacitación** del equipo en nuevas amenazas
- **Actualización** de guías de seguridad

## 📝 TEMPLATES DE COMENTARIOS

### ❌ Para rechazar código inseguro:
```markdown
🚨 **SECURITY ISSUE - BLOCKING**

**Problema**: Consulta MongoDB sin validación detectada en línea X
**Riesgo**: Inyección NoSQL - CRÍTICO
**Solución requerida**: 
1. Validar tipo y formato del parámetro
2. Usar `validateObjectId()` para IDs
3. Agregar test de inyección NoSQL

**Referencia**: Ver `SECURITY_GUIDELINES.md` sección "Patrones Seguros"
```

### ⚠️ Para solicitar mejoras:
```markdown
⚠️ **SECURITY IMPROVEMENT NEEDED**

**Observación**: Validación de email incompleta en línea X
**Recomendación**: Usar `sanitizeEmail()` helper function
**Impacto**: Mejora la consistencia y seguridad

**Ejemplo**:
```typescript
const cleanEmail = sanitizeEmail(email);
```

### ✅ Para aprobar código seguro:
```markdown
✅ **SECURITY REVIEW PASSED**

**Validaciones verificadas**:
- [x] Consultas MongoDB con validación apropiada
- [x] Middleware de sanitización aplicado
- [x] Tests de seguridad incluidos
- [x] Logging de eventos implementado

**Excelente trabajo siguiendo las guías de seguridad!**
```

## 🎓 CAPACITACIÓN PARA REVIEWERS

### Conocimientos Obligatorios:
1. **Inyección NoSQL**: Vectores de ataque y prevención
2. **Validación de Entrada**: Técnicas y herramientas
3. **Middleware de Seguridad**: Implementación y configuración
4. **Testing de Seguridad**: Casos de prueba y cobertura
5. **Incident Response**: Proceso de respuesta a vulnerabilidades

### Certificación Requerida:
- [ ] Completar tutorial de inyección NoSQL
- [ ] Revisar casos de vulnerabilidades corregidas
- [ ] Practicar con PRs de ejemplo
- [ ] Pasar examen de seguridad (90% mínimo)
- [ ] Actualización anual obligatoria

## 📊 MÉTRICAS DE REVISIÓN

### KPIs a Monitorear:
- **Tiempo promedio de revisión**: <24 horas
- **Vulnerabilidades detectadas**: Objetivo >95%
- **False positives**: <5%
- **PRs rechazados por seguridad**: Tendencia decreciente
- **Cobertura de tests de seguridad**: >90%

### Reportes Mensuales:
- Vulnerabilidades detectadas y corregidas
- Tiempo de respuesta de reviewers
- Efectividad del proceso de revisión
- Capacitación y certificación del equipo

## 🔄 PROCESO DE MEJORA CONTINUA

### Revisión Mensual del Proceso:
1. **Análisis de métricas** de revisión
2. **Feedback del equipo** sobre el proceso
3. **Actualización de guías** basada en nuevas amenazas
4. **Capacitación adicional** si es necesaria
5. **Mejoras en herramientas** de automatización

### Escalación de Problemas:
- **Desacuerdo entre reviewers** → Security Champion
- **Vulnerabilidad crítica detectada** → Incident Response
- **Proceso ineficiente** → Team Lead + Security Champion
- **Capacitación insuficiente** → Training Team

---

## ⚠️ RECORDATORIO CRÍTICO

### PRINCIPIO FUNDAMENTAL:
> **"Es mejor rechazar código seguro por exceso de precaución que aprobar código inseguro por falta de revisión"**

### MANTRA DEL REVIEWER:
> "¿Este código puede ser explotado para inyección NoSQL?"
> "¿Están todos los inputs validados apropiadamente?"
> "¿Hay tests que prueben estos escenarios de ataque?"

### RESPONSABILIDAD COMPARTIDA:
**Cada reviewer es responsable de la seguridad del sistema completo.**

---

**Estado**: ✅ ACTIVO Y OBLIGATORIO
**Aplicación**: Todos los code reviewers del equipo IATECH
**Actualización**: Después de cada incidente de seguridad o mensualmente