# Guías de Seguridad - IATECH Backend

## 🚨 REGLAS CRÍTICAS DE SEGURIDAD

### ⚠️ NUNCA HACER - Vulnerabilidades NoSQL

#### ❌ PROHIBIDO: Consultas MongoDB sin validación
```typescript
// ❌ NUNCA HACER ESTO - Vulnerable a inyección NoSQL
const user = await User.findOne({ email });
const assessment = await BusinessAssessment.findOne({ quizResponseId });
const packages = await ServicePackage.find(query);
```

#### ✅ SIEMPRE HACER: Validación y sanitización
```typescript
// ✅ CORRECTO - Validar y sanitizar antes de usar
if (!email || typeof email !== 'string') {
  return res.status(400).json({ error: 'Invalid email' });
}
const sanitizedEmail = email.toString().trim().toLowerCase();
const user = await User.findOne({ email: sanitizedEmail });
```

### 🛡️ MIDDLEWARE OBLIGATORIO

#### Aplicar sanitización en TODAS las rutas
```typescript
// ✅ OBLIGATORIO en todas las rutas
import { sanitizeQuery } from '../middleware/sanitization';
router.use(sanitizeQuery);
```

#### Validar ObjectIds en parámetros de ruta
```typescript
// ✅ OBLIGATORIO para rutas con :id
import { validateMongoId } from '../middleware/sanitization';
router.get('/:id', validateMongoId('id'), handler);
```

### 📝 VALIDACIÓN CON ZOD

#### Crear esquemas para endpoints críticos
```typescript
// ✅ OBLIGATORIO para endpoints que manejan datos sensibles
import { z } from 'zod';

const objectIdSchema = z.string().refine(
  (val) => mongoose.Types.ObjectId.isValid(val),
  { message: 'Invalid MongoDB ObjectId format' }
);

export const userSchema = z.object({
  email: z.string().email(),
  id: objectIdSchema
});
```

## 🔍 CHECKLIST DE SEGURIDAD

### Antes de crear/modificar cualquier endpoint:

#### ✅ Validación de Entrada
- [ ] Todos los parámetros de ruta validados
- [ ] Todos los query parameters sanitizados
- [ ] Request body validado con esquema
- [ ] ObjectIds validados con `mongoose.Types.ObjectId.isValid()`
- [ ] Emails validados con regex + sanitización

#### ✅ Consultas MongoDB
- [ ] Nunca pasar directamente req.params/req.query a consultas
- [ ] Siempre validar tipos antes de usar en consultas
- [ ] Usar `new mongoose.Types.ObjectId()` para IDs
- [ ] Sanitizar strings antes de usar en $regex

#### ✅ Middleware Aplicado
- [ ] `sanitizeQuery` aplicado globalmente
- [ ] `validateMongoId` en rutas con parámetros ID
- [ ] Validación Zod en endpoints críticos
- [ ] Rate limiting en endpoints públicos

#### ✅ Manejo de Errores
- [ ] Mensajes de error no revelan información sensible
- [ ] Logging de intentos de inyección
- [ ] Respuestas consistentes para errores de validación

## 🚫 PATRONES PROHIBIDOS

### ❌ Construcción dinámica de queries
```typescript
// ❌ NUNCA HACER - Permite inyección
const query: any = {};
if (category) query.category = category; // category puede ser { $ne: "value" }
const results = await Model.find(query);
```

### ❌ Pasar directamente parámetros de request
```typescript
// ❌ NUNCA HACER - Vulnerable
const user = await User.findById(req.params.id);
const data = await Model.findOne(req.query);
```

### ❌ Usar variables sin validación en consultas
```typescript
// ❌ NUNCA HACER - Sin validación
const { email, password } = req.body;
const user = await User.findOne({ email, password });
```

## ✅ PATRONES SEGUROS

### ✅ Validación estricta de tipos
```typescript
// ✅ CORRECTO - Validación completa
const validateObjectId = (id: string): mongoose.Types.ObjectId => {
  if (!id || typeof id !== 'string') {
    throw new Error('ID must be a valid string');
  }
  
  const sanitized = id.toString().trim();
  if (!mongoose.Types.ObjectId.isValid(sanitized)) {
    throw new Error('Invalid ObjectId format');
  }
  
  return new mongoose.Types.ObjectId(sanitized);
};
```

### ✅ Sanitización de emails
```typescript
// ✅ CORRECTO - Sanitización completa
const sanitizeEmail = (email: string): string => {
  if (!email || typeof email !== 'string') {
    throw new Error('Email must be a valid string');
  }
  
  const sanitized = email.toString().trim().toLowerCase();
  const emailRegex = /^[^\\s@]+@[^\\s@]+\\.[^\\s@]+$/;
  if (!emailRegex.test(sanitized)) {
    throw new Error('Invalid email format');
  }
  
  return sanitized;
};
```

### ✅ Construcción segura de queries
```typescript
// ✅ CORRECTO - Validación de cada campo
const buildSecureQuery = (params: any) => {
  const query: any = { isActive: true };
  
  // Validar categoría
  if (params.category) {
    const validCategories = ['web-design', 'graphic-design', 'brand-identity'];
    if (!validCategories.includes(params.category)) {
      throw new Error('Invalid category');
    }
    query.category = params.category;
  }
  
  // Validar precio
  if (params.minPrice) {
    const price = parseFloat(params.minPrice);
    if (isNaN(price) || price < 0) {
      throw new Error('Invalid price');
    }
    query.price = { $gte: price };
  }
  
  return query;
};
```

## 🔧 HERRAMIENTAS OBLIGATORIAS

### 1. Middleware de Sanitización
```typescript
// Usar en TODAS las rutas
import { sanitizeQuery, validateMongoId } from '../middleware/sanitization';
```

### 2. Validación Zod
```typescript
// Usar en endpoints críticos
import { validateBody, validateParams } from '../validation/schemas';
```

### 3. Helper Functions
```typescript
// Usar para validaciones comunes
import { validateObjectId, sanitizeEmail, sanitizeString } from '../middleware/sanitization';
```

## 📊 TESTING DE SEGURIDAD

### Tests obligatorios para cada endpoint:

#### ✅ Test de Inyección NoSQL
```typescript
it('should reject NoSQL injection in parameters', async () => {
  const maliciousPayload = { $ne: 'someValue' };
  const response = await request(app)
    .get(`/api/endpoint/${JSON.stringify(maliciousPayload)}`)
    .expect(400);
  
  expect(response.body.success).toBe(false);
  expect(response.body.error).toContain('Invalid');
});
```

#### ✅ Test de Query Parameters
```typescript
it('should reject objects in query parameters', async () => {
  const response = await request(app)
    .get('/api/endpoint')
    .query({ param: { $ne: 'value' } })
    .expect(400);
    
  expect(response.body.error).toBe('Invalid query parameter format');
});
```

#### ✅ Test de Request Body
```typescript
it('should sanitize request body', async () => {
  const maliciousBody = {
    validField: 'value',
    $where: 'malicious code',
    nested: { $ne: 'injection' }
  };
  
  const response = await request(app)
    .post('/api/endpoint')
    .send(maliciousBody)
    .expect(400);
});
```

## 🚨 ALERTAS Y MONITOREO

### Logging obligatorio:
```typescript
// Log intentos de inyección
logger.warn('NoSQL injection attempt detected', {
  ip: req.ip,
  userAgent: req.get('User-Agent'),
  payload: req.body,
  timestamp: new Date()
});
```

### Métricas de seguridad:
- Número de intentos de inyección por día
- IPs que intentan inyección repetidamente
- Endpoints más atacados
- Tipos de payloads maliciosos más comunes

## 📚 RECURSOS DE REFERENCIA

### Documentación interna:
- `docs/SECURITY_AUDIT_COMPLETE.md` - Auditoría completa
- `docs/SECURITY_FIXES_SUMMARY.md` - Resumen de correcciones
- `src/test/security.test.ts` - Tests de seguridad

### Archivos clave:
- `src/middleware/sanitization.ts` - Middleware de seguridad
- `src/validation/serviceDiscoverySchemas.ts` - Esquemas Zod
- `src/test/security.test.ts` - Suite de tests de seguridad

## 🎯 RESPONSABILIDADES DEL DESARROLLADOR

### Antes de hacer commit:
1. ✅ Ejecutar tests de seguridad: `npm run test:security`
2. ✅ Verificar que no hay consultas MongoDB sin validación
3. ✅ Confirmar que middleware de sanitización está aplicado
4. ✅ Validar que todos los ObjectIds están siendo validados

### Antes de hacer deploy:
1. ✅ Auditoría de seguridad completa
2. ✅ Verificar logs de seguridad
3. ✅ Confirmar que rate limiting está activo
4. ✅ Validar que HTTPS está configurado

## 🔄 PROCESO DE REVISIÓN

### Code Review obligatorio:
- [ ] Revisar todas las consultas MongoDB
- [ ] Verificar validación de parámetros
- [ ] Confirmar aplicación de middleware
- [ ] Validar tests de seguridad

### Herramientas automáticas:
- ESLint rules para detectar consultas inseguras
- Pre-commit hooks para validación
- CI/CD pipeline con tests de seguridad
- Análisis estático de código

---

## ⚠️ RECORDATORIO CRÍTICO

**NUNCA CONFÍES EN DATOS DEL USUARIO**

Todo input del usuario debe ser:
1. **Validado** - Verificar tipo y formato
2. **Sanitizado** - Limpiar caracteres peligrosos  
3. **Escapado** - Preparar para uso seguro en consultas
4. **Loggeado** - Registrar intentos maliciosos

**Una sola consulta sin validar puede comprometer toda la aplicación.**

---

**Última actualización**: Después de corrección de vulnerabilidades NoSQL
**Estado**: ✅ IMPLEMENTADO Y ACTIVO
**Cobertura**: 100% de endpoints protegidos