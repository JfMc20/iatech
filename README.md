# IATECH Monorepo

Este repositorio contiene tanto el frontend como el backend de la plataforma IATECH, unificados en un monorepo para facilitar el desarrollo, la integración continua y el despliegue.

## Estructura del Proyecto

```
IATECH/
├── frontediatech/          # Frontend Next.js 14 Application
│   ├── src/                # Source code
│   ├── public/             # Static assets
│   ├── .storybook/         # Storybook configuration
│   └── package.json        # Frontend dependencies
├── backendiatech/          # Backend Express.js + MongoDB API
│   ├── src/                # Source code
│   ├── dist/               # Compiled output
│   ├── logs/               # Application logs
│   └── package.json        # Backend dependencies
├── .github/                # GitHub Actions workflows
└── README.md               # This file
```

## Tecnologías Principales

### Frontend (frontediatech/)
- **Framework**: Next.js 14 con App Router
- **UI**: Tailwind CSS + Shadcn/ui
- **State Management**: Zustand + React Query
- **Testing**: Vitest + Testing Library
- **Storybook**: Component documentation
- **TypeScript**: Type safety

### Backend (backendiatech/)
- **Framework**: Express.js con TypeScript
- **Database**: MongoDB con Mongoose
- **Authentication**: JWT + Google OAuth
- **Architecture**: Clean Architecture (Controllers → Services → Models)
- **Security**: NoSQL injection protection, input validation
- **Testing**: Jest + Supertest

## Desarrollo Local

### Prerrequisitos
- Node.js 18+
- MongoDB (local o Atlas)
- Git

### Configuración Inicial

1. **Clonar el repositorio**:
   ```bash
   git clone https://github.com/JfMc20/IATECH.git
   cd IATECH
   ```

2. **Configurar el Backend**:
   ```bash
   cd backendiatech
   npm install
   cp .env.example .env
   # Configurar variables de entorno en .env
   npm run dev
   ```

3. **Configurar el Frontend**:
   ```bash
   cd frontediatech
   npm install
   cp .env.example .env.local
   # Configurar variables de entorno en .env.local
   npm run dev
   ```

### Scripts Disponibles

#### Frontend
- `npm run dev` - Servidor de desarrollo
- `npm run build` - Build de producción
- `npm run start` - Servidor de producción
- `npm run storybook` - Documentación de componentes
- `npm test` - Ejecutar tests

#### Backend
- `npm run dev` - Servidor de desarrollo con hot reload
- `npm run build` - Compilar TypeScript
- `npm run start` - Servidor de producción
- `npm test` - Ejecutar tests
- `npm run test:security` - Tests de seguridad

## Integración Continua

Este proyecto utiliza GitHub Actions para:
- ✅ Verificación de código en cada PR
- ✅ Ejecución de tests automatizados
- ✅ Análisis de calidad de código
- 🔄 Despliegue automático (próximamente)

## URLs de Producción

- **Frontend**: https://iatech-frontend.vercel.app
- **Backend**: https://iatech-backend.onrender.com
- **Storybook**: https://iatech-storybook.vercel.app

## Contribución

1. Fork el repositorio
2. Crea una rama feature: `git checkout -b feat/nueva-funcionalidad`
3. Commit tus cambios: `git commit -m 'feat: agregar nueva funcionalidad'`
4. Push a la rama: `git push origin feat/nueva-funcionalidad`
5. Abre un Pull Request

## Arquitectura del Sistema

### Frontend Architecture
- **App Router**: Routing basado en archivos
- **API Routes**: Proxy al backend con validación
- **Components**: Arquitectura modular con Storybook
- **State**: Zustand para estado global, React Query para servidor

### Backend Architecture (Clean Architecture)
```
Routes → Controllers → Services → Models
```
- **Routes**: Definición de endpoints
- **Controllers**: Manejo HTTP y validación
- **Services**: Lógica de negocio pura
- **Models**: Acceso a datos (MongoDB)

### Seguridad
- 🔒 Protección contra inyección NoSQL
- 🔒 Validación exhaustiva de inputs
- 🔒 Autenticación JWT + OAuth
- 🔒 Rate limiting y sanitización

## Licencia

Este proyecto es propiedad de IATECH. Todos los derechos reservados.

---

**Mantenido por**: Equipo de Desarrollo IATECH  
**Última actualización**: Enero 2025