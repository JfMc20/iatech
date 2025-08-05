# GitHub Configuration - IATECH Monorepo

Este directorio contiene toda la configuración de GitHub para el monorepo IATECH, incluyendo workflows de CI/CD, templates y automatización.

## 📁 Estructura

```
.github/
├── workflows/              # GitHub Actions workflows
│   ├── ci.yml             # Workflow básico de CI
│   └── ci-complete.yml    # Workflow completo con todos los jobs
├── ISSUE_TEMPLATE/        # Templates para issues
│   ├── bug_report.md      # Template para reportes de bugs
│   └── feature_request.md # Template para solicitudes de features
├── dependabot.yml         # Configuración de Dependabot
├── pull_request_template.md # Template para Pull Requests
└── README.md              # Este archivo
```

## 🔄 Workflows de CI/CD

### ci.yml - Workflow Básico
- **Trigger**: Push y PR a `main`
- **Propósito**: Verificación básica y placeholder para desarrollo futuro
- **Jobs**: 
  - Verificación de código
  - Configuración de Node.js
  - Placeholders para instalación y tests

### ci-complete.yml - Workflow Completo
- **Trigger**: Push y PR a `main` y `develop`
- **Jobs**:
  - **Frontend CI**: Build, tests, linting, Storybook
  - **Backend CI**: Build, tests, seguridad, con MongoDB
  - **Security**: Análisis de vulnerabilidades
  - **Monorepo Structure**: Verificación de estructura

## 🤖 Dependabot

Configurado para actualizar automáticamente:
- **Frontend dependencies** (npm): Lunes 09:00
- **Backend dependencies** (npm): Lunes 09:00  
- **GitHub Actions**: Lunes 09:00

## 📋 Templates

### Issues
- **Bug Report**: Template estructurado para reportes de errores
- **Feature Request**: Template para solicitudes de nuevas funcionalidades

### Pull Requests
- Template completo con checklist para:
  - General
  - Frontend específico
  - Backend específico
  - Seguridad

## 🚀 Uso

### Para Desarrolladores

1. **Crear Issue**: Usa los templates apropiados
2. **Crear PR**: El template se aplicará automáticamente
3. **CI/CD**: Los workflows se ejecutan automáticamente

### Para Mantenedores

1. **Revisar PRs**: Usar el checklist del template
2. **Monitorear CI**: Verificar que todos los jobs pasen
3. **Dependabot**: Revisar y aprobar actualizaciones semanales

## 🔧 Configuración Futura

### Próximas Mejoras
- [ ] Deploy automático a staging/production
- [ ] Integración con Vercel/Render
- [ ] Tests E2E con Playwright
- [ ] Análisis de código con SonarCloud
- [ ] Notificaciones Slack/Discord
- [ ] Release automation

### Variables de Entorno Necesarias
```bash
# Para CI completo (configurar en GitHub Secrets)
MONGODB_URI=mongodb://localhost:27017/iatech-test
JWT_SECRET=test-secret-for-ci
NODE_ENV=test

# Para deploy (futuro)
VERCEL_TOKEN=xxx
RENDER_API_KEY=xxx
```

## 📊 Métricas y Monitoreo

Los workflows proporcionan:
- ✅ Verificación de build exitoso
- ✅ Cobertura de tests
- ✅ Análisis de seguridad
- ✅ Verificación de estructura
- ✅ Información de versiones

## 🔒 Seguridad

- Análisis automático de vulnerabilidades
- Verificación de dependencias
- Tests de seguridad específicos
- Validación de estructura del monorepo

---

**Mantenido por**: Equipo IATECH  
**Última actualización**: Enero 2025