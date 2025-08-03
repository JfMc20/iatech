# Service Discovery System

## Overview
Sistema inteligente de recomendaciones que transforma la búsqueda pasiva de servicios en una evaluación guiada y personalizada que genera roadmaps específicos.

## Current Implementation ✅ - REFACTORIZADO (Clean Architecture)
- **Quiz Engine**: Preguntas dinámicas con tipos múltiples
- **Scoring Algorithm**: Factores ponderados (Categoría 30%, Presupuesto 25%, Madurez Digital 20%, Urgencia 15%, Complejidad 10%)
- **Business Assessment**: Generación automática de evaluaciones con roadmap
- **API Endpoints**: ✅ REFACTORIZADA - Estructura limpia por capas
- **Frontend Components**: Componentes básicos implementados
- **Clean Architecture**: ✅ NUEVA - Separación de responsabilidades implementada

## API Endpoints
```typescript
GET    /api/service-discovery/questions          // Obtener preguntas
POST   /api/service-discovery/submit            // Enviar respuestas
GET    /api/service-discovery/assessment-by-id?id=... // Obtener evaluación (query param)
POST   /api/service-discovery/progress          // Guardar progreso
GET    /api/service-discovery/progress/:sessionId // Cargar progreso
POST   /api/service-discovery/create-inquiry    // Convertir a consulta
```

**⚠️ ROUTING UPDATE**: Assessment endpoint changed from dynamic route to query parameter due to Next.js App Router compatibility issues.

## Key Data Models
```typescript
interface QuizQuestion {
  _id: ObjectId;
  type: 'single_choice' | 'multiple_choice' | 'scale' | 'text';
  category: 'business_profile' | 'current_situation' | 'goals' | 'budget' | 'timeline';
  text: string;
  options?: QuizOption[];
  required: boolean;
  order: number;
}

interface BusinessAssessment {
  _id: ObjectId;
  sessionId: string;
  userId?: ObjectId;
  businessProfile: BusinessProfile;
  recommendations: ServiceRecommendation[];
  roadmap: RoadmapPhase[];
  totalInvestment: number;
  estimatedROI: number;
  createdAt: Date;
}
```

## Enhancement Opportunities

### Current Limitations
1. **Authentication**: Endpoints don't require auth, userId optional
2. **Chat Integration**: No contextual connection with chat system  
3. **Static Experience**: Roadmap not interactive, no dynamic adjustments
4. **No Feedback Loop**: Missing algorithm improvement cycle

### Planned Enhancements (Tasks 21-27)
1. **Smart Authentication**: Optional intelligent auth middleware
2. **Contextual Chat**: "Talk to Expert" button with pre-loaded context
3. **Interactive Roadmap**: Visual timeline with expandable phases
4. **Continuous Feedback**: Post-assessment feedback system
5. **Smart Notifications**: Abandoned session recovery
6. **Advanced Analytics**: Quiz-specific KPIs with visualizations

## Integration Points
- **Auth Module**: Hybrid JWT/OAuth2 authentication
- **Chat System**: Automatic context from assessment
- **Package System**: Direct links and tracking
- **Inquiry System**: Smooth assessment → inquiry conversion
- **Notification System**: Proactive alerts and follow-up
- **Analytics Module**: Advanced metrics and insights

## Expected Improvements
- **Completion Rate**: 60% → 85% (+42%)
- **Conversion to Inquiry**: 15% → 37.5% (+150%)
- **Response Time**: 4 hours → 15 minutes (+93%)
- **Roadmap Engagement**: 2 min → 8 min (+300%)## 🏗️ Nuev
a Arquitectura Refactorizada

### Estructura por Capas
```
┌─────────────────────────────────┐
│ serviceDiscovery.ts (Routes)    │ ← Solo definición de endpoints
├─────────────────────────────────┤
│ serviceDiscoveryController.ts   │ ← HTTP handlers, delegación
├─────────────────────────────────┤
│ serviceDiscoveryService.ts      │ ← Lógica de negocio pura
├─────────────────────────────────┤
│ serviceDiscoveryMiddleware.ts   │ ← Middleware específico
├─────────────────────────────────┤
│ serviceDiscoverySchemas.ts      │ ← Validación Zod
└─────────────────────────────────┘
```

### Beneficios de la Refactorización
- **🔍 Debugging**: Cada error se localiza fácilmente por capa
- **🧪 Testing**: Lógica de negocio testeable sin HTTP
- **📈 Escalabilidad**: Fácil agregar nuevos endpoints
- **🔄 Reutilización**: ServiceDiscoveryService reutilizable
- **🛡️ Mantenimiento**: Cambios aislados por responsabilidad

### Ejemplo de Flujo Refactorizado
```typescript
// 1. Route Definition (serviceDiscovery.ts)
router.post('/submit',
  logServiceDiscoveryOperation('SUBMIT_QUIZ'),
  validateQuizSubmission,
  submitQuizResponse
)

// 2. HTTP Handler (serviceDiscoveryController.ts)
export const submitQuizResponse = async (req, res) => {
  const result = await ServiceDiscoveryService.submitQuizResponse({
    answers: req.body.answers,
    sessionId: req.body.sessionId,
    userId: req.user?._id
  })
  res.json({ success: true, data: result })
}

// 3. Business Logic (serviceDiscoveryService.ts)
export class ServiceDiscoveryService {
  static async submitQuizResponse(data: QuizSubmissionData) {
    // Pure business logic - no HTTP dependencies
    const quizResponse = new QuizResponse(data)
    await quizResponse.save()
    return { id: quizResponse._id.toString() }
  }
}
```