# URL Configuration Guidelines - IATECH Frontend

## ⚠️ CRITICAL: URL Construction Best Practices

### Environment Variables Configuration

#### ✅ CORRECT Configuration
```bash
# .env.local
NEXT_PUBLIC_API_URL=https://iatech-backend.onrender.com
NEXT_PUBLIC_BACKEND_URL=https://iatech-backend.onrender.com
```

#### ❌ INCORRECT Configuration
```bash
# DON'T DO THIS - Causes double /api paths
NEXT_PUBLIC_API_URL=https://iatech-backend.onrender.com/api
NEXT_PUBLIC_BACKEND_URL=https://iatech-backend.onrender.com/api
```

### URL Construction Patterns

#### ✅ CORRECT Pattern
```typescript
// In API routes (src/app/api/*/route.ts)
const BACKEND_URL = process.env.NEXT_PUBLIC_API_URL || 'https://iatech-backend.onrender.com'
const url = `${BACKEND_URL}/api/packages` // Explicitly add /api

// In hooks/services that need direct backend access
const backendUrl = process.env.NEXT_PUBLIC_API_URL || 'https://iatech-backend.onrender.com'
window.location.href = `${backendUrl}/api/auth/google`

// For POST endpoints - Safe JSON parsing
export async function POST(request: NextRequest) {
  try {
    const contentLength = request.headers.get('content-length')
    const contentType = request.headers.get('content-type')
    
    let body = {}
    
    if (contentLength && contentLength !== '0' && contentType?.includes('application/json')) {
      try {
        body = await request.json()
      } catch (jsonError) {
        return NextResponse.json({ success: false, message: 'Invalid JSON' }, { status: 400 })
      }
    }
    
    // Continue with fetch to backend...
  } catch (error) {
    // Handle errors...
  }
}
```

#### ❌ INCORRECT Pattern
```typescript
// DON'T DO THIS - Results in /api/api/packages
const BACKEND_URL = process.env.NEXT_PUBLIC_API_URL || 'https://iatech-backend.onrender.com/api'
const url = `${BACKEND_URL}/api/packages` // Double /api
```

### Backend Route Structure
```
Backend Server (https://iatech-backend.onrender.com)
├── /health (health checks)
├── /api/
│   ├── /auth/
│   ├── /packages/
│   ├── /inquiries/
│   ├── /service-discovery/
│   └── /setup/
```

### Frontend API Route Structure
```
Frontend API Routes (/api/*)
├── /api/packages/ (proxies to backend /api/packages)
├── /api/setup/ (proxies to backend /api/setup)
└── Direct backend calls for auth (bypass proxy)
```

## Files That Need URL Configuration

### 1. Environment Files
- `frontediatech/.env.local`
- `frontediatech/.env.example`

### 2. API Route Files (Proxy to Backend)
**Packages API:**
- `src/app/api/packages/route.ts`
- `src/app/api/packages/featured/route.ts`
- `src/app/api/packages/[id]/route.ts`
- `src/app/api/packages/[id]/related/route.ts`
- `src/app/api/packages/search/route.ts`
- `src/app/api/packages/search/suggestions/route.ts`

**Service Discovery API:**
- `src/app/api/service-discovery/questions/route.ts`
- `src/app/api/service-discovery/submit/route.ts`
- `src/app/api/service-discovery/assessment/[id]/route.ts`
- `src/app/api/service-discovery/progress/route.ts`
- `src/app/api/service-discovery/progress/[sessionId]/route.ts`
- `src/app/api/service-discovery/create-inquiry/route.ts`

**Setup API:**
- `src/app/api/setup/seed/route.ts`
- `src/app/api/setup/seed-service-discovery/route.ts`

### 3. Direct Backend Access Files
- `src/hooks/use-google-auth.ts` (OAuth redirects)
- `src/lib/config.ts`
- `src/lib/env.ts`

### 4. Display Files (for debugging)
- `src/app/dashboard/page.tsx`
- `src/app/setup/page.tsx`

## Testing Checklist

Before making URL changes, verify:

1. **Packages API**: `GET /api/packages` works
2. **Featured Packages**: `GET /api/packages/featured` works  
3. **Google OAuth**: `/api/auth/google` redirect works
4. **Setup endpoints**: `/api/setup/*` work
5. **Service Discovery Questions**: `GET /api/service-discovery/questions` works
6. **Service Discovery Submit**: `POST /api/service-discovery/submit` works
7. **Service Discovery Assessment**: `GET /api/service-discovery/assessment/[id]` works
8. **Service Discovery Progress**: `GET/POST /api/service-discovery/progress/*` works

## Common Issues & Solutions

### Issue: Double /api in URLs
**Symptom**: `https://iatech-backend.onrender.com/api/api/packages`
**Solution**: Remove `/api` from environment variables, add explicitly in code

### Issue: OAuth redirect fails
**Symptom**: `404` on Google OAuth callback
**Solution**: Ensure `NEXT_PUBLIC_API_URL` doesn't include `/api`

### Issue: Package API returns 404
**Symptom**: Frontend can't load packages
**Solution**: Verify backend is running and `/api/packages` endpoint exists

### Issue: JSON parsing errors in POST endpoints
**Symptom**: `SyntaxError: Unexpected end of JSON input`
**Solution**: Check content-length and content-type headers before parsing JSON

## Emergency Rollback

If URLs break, quickly restore with:

```bash
# .env.local - Safe fallback configuration
NEXT_PUBLIC_API_URL=https://iatech-backend.onrender.com
NEXT_PUBLIC_BACKEND_URL=https://iatech-backend.onrender.com
```

## Validation Commands

```bash
# Test backend connectivity
curl https://iatech-backend.onrender.com/health

# Test API endpoint
curl https://iatech-backend.onrender.com/api/packages

# Test frontend proxy
curl http://localhost:3000/api/packages
```

---

**⚠️ REMEMBER**: Always test authentication flow after URL changes!