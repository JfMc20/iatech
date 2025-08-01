# Project Structure & Organization

## Root Directory Structure
```
iatech-ecosystem/
├── backendiatech/       # Backend API (Express.js + MongoDB)
│   ├── src/             # Source code
│   ├── dist/            # Compiled JavaScript output
│   ├── logs/            # Application logs
│   ├── backups/         # Database backups
│   └── test-reports/    # Test coverage reports
└── frontediatech/       # Frontend Application (Next.js 14)
    ├── src/             # Source code
    ├── .next/           # Next.js build output
    ├── public/          # Static assets
    ├── docs/            # Documentation
    ├── .storybook/      # Storybook configuration
    └── storybook-static/ # Storybook build output
```

## Source Code Architecture (`src/`) - ✅ REFACTORIZADA (Clean Architecture)

### 🏗️ Nueva Arquitectura por Capas
```
┌─────────────────┐
│     Routes      │ ← Solo definición de rutas y middleware
├─────────────────┤
│   Controllers   │ ← Manejo HTTP, validación, respuestas
├─────────────────┤
│    Services     │ ← Lógica de negocio pura (sin dependencias HTTP)
├─────────────────┤
│     Models      │ ← Acceso a datos (MongoDB)
└─────────────────┘
```

### 📂 Estructura Refactorizada
```
src/
├── config/              # Configuration modules
│   ├── database.ts      # MongoDB connection setup
│   ├── logger.ts        # Winston logging configuration
│   ├── monitoring.ts    # Performance monitoring setup
│   └── index.ts         # Centralized config exports
├── controllers/         # HTTP Controllers (solo manejo request/response)
│   ├── serviceDiscoveryController.ts  # ✅ REFACTORIZADO
│   └── inquiryController.ts
├── services/            # ✅ NUEVA CAPA - Lógica de negocio pura
│   ├── serviceDiscoveryService.ts     # ✅ NUEVO - Business logic
│   ├── cloudStorage.ts  # Cloudinary integration
│   └── emailService.ts  # Email notifications
├── middleware/          # Express middleware (modularizado)
│   ├── auth.ts          # JWT authentication
│   ├── errorHandler.ts  # Global error handling
│   ├── sanitization.ts  # NoSQL injection protection
│   ├── serviceDiscoveryMiddleware.ts  # ✅ NUEVO - Middleware específico
│   ├── monitoring.ts    # Performance monitoring
│   ├── upload.ts        # File upload handling
│   └── inquiryValidation.ts
├── models/              # Mongoose data models
│   ├── User.ts          # User authentication model
│   ├── ServicePackage.ts # Service packages model
│   ├── ServiceDiscovery.ts # ✅ Service discovery models
│   ├── CustomInquiry.ts # Custom project inquiries
│   ├── ChatSession.ts   # Chat system model
│   └── index.ts         # Model exports
├── routes/              # API route definitions (solo routing)
│   ├── auth.ts          # Authentication endpoints
│   ├── packages.ts      # Service package endpoints
│   ├── serviceDiscovery.ts # ✅ REFACTORIZADO - Clean routes
│   ├── inquiries.ts     # Custom inquiry endpoints
│   ├── health.ts        # Health check endpoints
│   └── index.ts         # Route aggregation
├── validation/          # Esquemas de validación Zod
│   ├── serviceDiscoverySchemas.ts # ✅ Service discovery validation
│   ├── emailService.ts  # Email notifications
│   ├── backupService.ts # Database backup automation
│   └── monitoringService.ts # System monitoring
├── scripts/             # Utility scripts
│   ├── createAdmin.ts   # Admin user creation
│   ├── seedDatabase.ts  # Database seeding
│   └── securityAudit.ts # Security checks
├── __tests__/           # Test files
│   ├── routes/          # Route testing
│   ├── helpers/         # Test utilities
│   └── *.test.ts        # Unit tests
└── index.ts             # Application entry point
```

## Architectural Patterns

### MVC Pattern
- **Models**: Data layer with Mongoose schemas and validation
- **Controllers**: Business logic and request handling
- **Routes**: API endpoint definitions and middleware application

### Middleware Pipeline
- Security headers (helmet)
- CORS configuration
- Rate limiting and speed limiting
- Authentication verification
- Request validation
- Performance monitoring
- Error handling

### Service Layer
- External API integrations (Cloudinary, email)
- Business logic abstraction
- Utility functions and helpers
- Background job processing

## File Naming Conventions
- **Models**: PascalCase (e.g., `ServicePackage.ts`)
- **Controllers**: camelCase with Controller suffix (e.g., `inquiryController.ts`)
- **Routes**: camelCase (e.g., `auth.ts`, `packages.ts`)
- **Middleware**: camelCase (e.g., `errorHandler.ts`)
- **Services**: camelCase with Service suffix (e.g., `emailService.ts`)
- **Tests**: Match source file with `.test.ts` suffix

## Configuration Management
- Environment-specific `.env` files
- Centralized configuration in `src/config/`
- Type-safe configuration with TypeScript interfaces
- Validation of required environment variables

## Testing Structure
- Unit tests alongside source files in `__tests__/`
- Integration tests in `src/test/`
- Test helpers and utilities in `__tests__/helpers/`
- Separate test database configuration
- Coverage reports in `test-reports/`

## Logging & Monitoring
- Structured logging with Winston
- Log files in `logs/` directory with rotation
- Performance metrics collection
- Health check endpoints for system monitoring
- Automated backup system with retention policies
## Fronte
nd Architecture (`frontediatech/src/`)
```
src/
├── app/                 # Next.js App Router
│   ├── (auth)/          # Auth route group
│   ├── (public)/        # Public routes
│   ├── admin/           # Admin dashboard
│   ├── api/             # API routes (proxy to backend)
│   ├── dashboard/       # User dashboard
│   ├── packages/        # Package catalog
│   ├── service-discovery/ # Service discovery quiz
│   └── globals.css      # Global styles
├── components/          # Reusable components
│   ├── ui/              # Shadcn/ui base components
│   ├── common/          # Common components
│   ├── features/        # Feature-specific components
│   ├── forms/           # Form components
│   ├── layout/          # Layout components
│   ├── providers/       # Context providers
│   └── sections/        # Page sections
├── hooks/               # Custom React hooks
├── lib/                 # Utilities and configuration
├── store/               # Zustand stores
├── styles/              # Additional styles
├── types/               # TypeScript type definitions
└── __tests__/           # Test files
```

## Key Configuration Files
- `package.json` - Dependencies and scripts
- `next.config.js` - Next.js configuration
- `tsconfig.json` - TypeScript configuration
- `eslint.config.mjs` - ESLint configuration
- `vitest.config.ts` - Test configuration
- `.env.local` - Environment variables
- `components.json` - Shadcn/ui configuration

## Development Tools & Commands
- **Storybook** - Component documentation and testing (`npm run storybook`)
- **Vitest** - Unit testing framework (`npm test`)
- **ESLint + Prettier** - Code quality and formatting (`npm run lint`)
- **TypeScript** - Type safety
- **Tailwind CSS** - Utility-first styling
- **Shadcn/ui** - Component library

### Essential Commands
```bash
# Frontend Development
npm run dev              # Start development server
npm run build           # Build for production
npm run storybook       # Component library
npm test                # Run tests
npm run lint            # Check code quality

# Backend Integration
# Backend URL: https://iatech-backend.onrender.com
# Health Check: /health
# API Base: /api/*
```
#
# 🎯 Principios de Refactorización Aplicados

### ✅ **Separation of Concerns**
- **Routes**: Solo definición de endpoints y aplicación de middleware
- **Controllers**: Manejo de HTTP (request/response), delegación a servicios
- **Services**: Lógica de negocio pura, sin dependencias HTTP
- **Middleware**: Validación, seguridad, logging específico por módulo

### ✅ **Clean Architecture Benefits**
- **🔍 Debugging más fácil**: Cada responsabilidad en su lugar
- **🧪 Testing mejorado**: Lógica de negocio separada de HTTP
- **📈 Escalabilidad**: Fácil agregar nuevos endpoints
- **🔄 Reutilización**: Servicios pueden usarse desde otros lugares
- **🛡️ Mantenibilidad**: Cambios aislados por responsabilidad

### 🔧 Ejemplo: Service Discovery Module

#### **Antes (Monolítico)**
```typescript
// serviceDiscovery.ts - TODO mezclado
├── Importaciones mezcladas
├── Middleware inline
├── Validaciones mezcladas
├── Rutas + lógica + validación
└── Difícil de debuggear (50+ líneas)
```

#### **Después (Separado por responsabilidades)**
```typescript
// serviceDiscoveryService.ts - Lógica de negocio
export class ServiceDiscoveryService {
  static async getActiveQuizQuestions() { /* Pure business logic */ }
  static async submitQuizResponse(data) { /* Pure business logic */ }
  static async getBusinessAssessment(id) { /* Pure business logic */ }
}

// serviceDiscoveryController.ts - HTTP Handler
export const getQuizQuestions = async (req, res) => {
  const questions = await ServiceDiscoveryService.getActiveQuizQuestions()
  res.json({ success: true, data: questions })
}

// serviceDiscoveryMiddleware.ts - Middleware específico
export const validateQuizSubmission = validateBody(submitQuizSchema)
export const logServiceDiscoveryOperation = (operation) => { /* Logging */ }

// serviceDiscovery.ts - Solo rutas
router.post('/submit',
  logServiceDiscoveryOperation('SUBMIT_QUIZ'),
  validateQuizSubmission,
  submitQuizResponse
)
```

## 🚀 Beneficios Medibles

### Antes vs Después
- **Archivos**: 1 monolítico → 4 especializados
- **Líneas por archivo**: 50+ → 15-30 promedio
- **Responsabilidades**: Mezcladas → Separadas
- **Testing**: Difícil → Fácil (lógica pura)
- **Debugging**: Complejo → Simple (responsabilidad clara)
- **Escalabilidad**: Limitada → Alta (fácil agregar features)

### Métricas de Calidad
- **Cohesión**: ⬆️ Alta (cada archivo una responsabilidad)
- **Acoplamiento**: ⬇️ Bajo (dependencias claras)
- **Mantenibilidad**: ⬆️ Alta (cambios aislados)
- **Testabilidad**: ⬆️ Alta (lógica separada de HTTP)

## 📋 Checklist para Nuevos Módulos

### Al agregar nuevas funcionalidades:
1. **Service Layer**: ✅ Implementar lógica de negocio pura
2. **Controller Layer**: ✅ Agregar HTTP handlers
3. **Middleware Layer**: ✅ Agregar validación/seguridad específica
4. **Routes Layer**: ✅ Definir endpoints limpios
5. **Tests**: ✅ Tests por capa
6. **Documentation**: ✅ Actualizar docs

### Estructura de archivos recomendada:
```
src/
├── services/newModuleService.ts      # Lógica de negocio
├── controllers/newModuleController.ts # HTTP handlers
├── middleware/newModuleMiddleware.ts  # Middleware específico
├── routes/newModule.ts               # Definición de rutas
├── validation/newModuleSchemas.ts    # Validación Zod
└── __tests__/newModule/              # Tests por capa
```