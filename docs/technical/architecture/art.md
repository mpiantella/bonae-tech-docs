Great choice — this vertical has strong product-market fit given Venezuela's large pool of educated professionals and high demand for skills that unlock remote work income.

---

## Market Context

- Target users are often on **3G/mobile connections**, sometimes with daily power outages
- Many learners want certifications that are **internationally recognized** (for remote jobs or emigration)
- Instructors are often **Venezuelan diaspora** or local professionals earning in USD
- Payment must support **USDT, Zelle, Zinli, or Binance P2P** — no Stripe, no PayPal
- Content must be **resilient to disconnection** mid-session

---

## Architecture Philosophy

Given the constraints, the architecture should follow these principles:

- **Offline-first**: content must be consumable without an active connection
- **Edge-cached**: media served as close to Venezuela as possible
- **Lightweight**: no heavy JS bundles, optimized video streaming (adaptive bitrate)
- **Mobile-first PWA**: installable, works on low-end Android devices
- **Async-friendly**: avoid real-time dependencies where possible (prefer async Q&A over live video)

---

## Cloud Provider Recommendation

Venezuela sits in northern South America. The closest regions with meaningful availability are:

| Provider | Closest Region | Notes |
|---|---|---|
| **AWS** | `sa-east-1` (São Paulo) | Best overall ecosystem, ~80ms latency to VE |
| **Cloudflare** | Edge PoPs in Bogotá, São Paulo | Best for CDN, Workers, R2 storage — strong regional coverage |
| **Google Cloud** | `southamerica-east1` (São Paulo) | Good for ML/AI features if needed later |
| **Azure** | Brazil South | Viable but fewer LATAM services |

**Recommended stack**: **AWS São Paulo** as the primary cloud + **Cloudflare** for CDN, edge caching, and DDoS protection. Cloudflare has PoPs that serve Venezuela reasonably well and their free/cheap tier makes it accessible for early-stage products.

---

## System Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                   CLIENT LAYER                      │
│         PWA (React) — Offline-first via             │
│         Service Worker + IndexedDB cache            │
└────────────────────┬────────────────────────────────┘
                     │ HTTPS
┌────────────────────▼────────────────────────────────┐
│              EDGE LAYER (Cloudflare)                │
│   CDN • WAF • Rate Limiting • Video streaming       │
│   Cloudflare R2 (video/assets storage)              │
│   Cloudflare Workers (lightweight edge logic)       │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│            API GATEWAY (AWS — sa-east-1)            │
│              AWS API Gateway + ALB                  │
└──────┬──────────────┬──────────────┬────────────────┘
       │              │              │
┌──────▼───┐   ┌──────▼───┐   ┌─────▼──────┐
│  Auth    │   │ Course   │   │  Payment   │
│ Service  │   │ Service  │   │  Service   │
└──────────┘   └──────────┘   └────────────┘
       │              │              │
┌──────▼──────────────▼──────────────▼────────────────┐
│                  DATA LAYER                         │
│  PostgreSQL (RDS) • Redis (ElastiCache)             │
│  S3 (raw video upload) → transcoded to R2           │
└─────────────────────────────────────────────────────┘
```

---

## Clean Architecture — Service Breakdown

Each service follows the same layered pattern:

```
/service-name
  /domain          → entities, value objects, interfaces
  /application     → use cases
  /infrastructure  → DB repos, external clients, adapters
  /delivery        → HTTP handlers (REST or GraphQL)
```

---

### 1. Auth Service (Go)

Handles registration, login, JWT issuance, and role management (student, instructor, admin).

```go
// domain/entities/user.go
package domain

import "time"

type Role string

const (
    RoleStudent    Role = "student"
    RoleInstructor Role = "instructor"
    RoleAdmin      Role = "admin"
)

type User struct {
    ID           string
    Email        string
    PasswordHash string
    Role         Role
    CreatedAt    time.Time
    IsActive     bool
}
```

```go
// application/usecases/login.go
package application

import (
    "errors"
    "github.com/yourorg/auth/domain"
)

type LoginUseCase struct {
    userRepo   domain.UserRepository
    jwtService domain.JWTService
    hasher     domain.PasswordHasher
}

func (uc *LoginUseCase) Execute(email, password string) (string, error) {
    user, err := uc.userRepo.FindByEmail(email)
    if err != nil || user == nil {
        return "", errors.New("invalid credentials")
    }
    if !uc.hasher.Verify(password, user.PasswordHash) {
        return "", errors.New("invalid credentials")
    }
    return uc.jwtService.Sign(user.ID, string(user.Role))
}
```

```go
// domain/interfaces.go
package domain

type UserRepository interface {
    FindByEmail(email string) (*User, error)
    Save(user *User) error
}

type JWTService interface {
    Sign(userID, role string) (string, error)
    Verify(token string) (*Claims, error)
}

type PasswordHasher interface {
    Hash(password string) (string, error)
    Verify(password, hash string) bool
}
```

---

### 2. Course Service (Go)

Manages courses, modules, lessons, and progress tracking. The key offline consideration here is that lesson metadata and content URLs are bundled so the PWA can cache them.

```go
// domain/entities/course.go
package domain

type Course struct {
    ID           string
    Title        string
    Description  string
    InstructorID string
    Modules      []Module
    IsPublished  bool
    PriceUSDT    float64
}

type Module struct {
    ID       string
    CourseID string
    Title    string
    Order    int
    Lessons  []Lesson
}

type Lesson struct {
    ID            string
    ModuleID      string
    Title         string
    VideoURL      string // Cloudflare R2 signed URL
    DurationSecs  int
    IsDownloadable bool   // controls offline availability
    Order         int
}
```

```go
// application/usecases/get_course_manifest.go
package application

// GetCourseManifest returns everything the PWA needs
// to cache the course for offline consumption
type GetCourseManifestUseCase struct {
    courseRepo  domain.CourseRepository
    accessRepo  domain.AccessRepository
    cdnService  domain.CDNService
}

type CourseManifest struct {
    Course  domain.Course
    Assets  []AssetURL // pre-signed CDN URLs for offline download
}

func (uc *GetCourseManifestUseCase) Execute(userID, courseID string) (*CourseManifest, error) {
    hasAccess, err := uc.accessRepo.HasAccess(userID, courseID)
    if err != nil || !hasAccess {
        return nil, ErrUnauthorized
    }

    course, err := uc.courseRepo.FindByID(courseID)
    if err != nil {
        return nil, err
    }

    var assets []AssetURL
    for _, module := range course.Modules {
        for _, lesson := range module.Lessons {
            if lesson.IsDownloadable {
                url, _ := uc.cdnService.SignedURL(lesson.VideoURL, 48) // 48hr expiry
                assets = append(assets, AssetURL{LessonID: lesson.ID, URL: url})
            }
        }
    }

    return &CourseManifest{Course: *course, Assets: assets}, nil
}
```

---

### 3. Payment Service (Go)

Given Venezuela's constraints, this service integrates with **Binance Pay**, **Zinli**, and manual USDT confirmation (common pattern in LATAM crypto payments).

```go
// domain/entities/payment.go
package domain

import "time"

type PaymentMethod string

const (
    PaymentUSDT    PaymentMethod = "usdt_trc20"
    PaymentZinli   PaymentMethod = "zinli"
    PaymentZelle   PaymentMethod = "zelle"
)

type PaymentStatus string

const (
    StatusPending  PaymentStatus = "pending"
    StatusVerified PaymentStatus = "verified"
    StatusFailed   PaymentStatus = "failed"
)

type Payment struct {
    ID            string
    UserID        string
    CourseID      string
    AmountUSD     float64
    Method        PaymentMethod
    Status        PaymentStatus
    TxReference   string    // wallet tx hash or transfer reference
    CreatedAt     time.Time
    VerifiedAt    *time.Time
}
```

```go
// application/usecases/initiate_payment.go
package application

type InitiatePaymentUseCase struct {
    paymentRepo    domain.PaymentRepository
    courseRepo     domain.CourseRepository
    notifier       domain.NotificationService
}

// Venezuela reality: many payments are manually verified
// by an admin checking a wallet or Zinli screenshot.
// This use case creates a pending payment and notifies admin.
func (uc *InitiatePaymentUseCase) Execute(req InitiatePaymentRequest) (*domain.Payment, error) {
    course, err := uc.courseRepo.FindByID(req.CourseID)
    if err != nil {
        return nil, err
    }

    payment := &domain.Payment{
        ID:          generateID(),
        UserID:      req.UserID,
        CourseID:    req.CourseID,
        AmountUSD:   course.PriceUSDT,
        Method:      req.Method,
        Status:      domain.StatusPending,
        TxReference: req.TxReference,
    }

    if err := uc.paymentRepo.Save(payment); err != nil {
        return nil, err
    }

    // Notify admin to verify — async via SQS queue
    uc.notifier.NotifyAdminPaymentPending(payment)

    return payment, nil
}
```

---

### 4. PWA Client (React + TypeScript)

The offline-first strategy is the most critical frontend concern.

```typescript
// infrastructure/offline/courseCache.ts
const CACHE_NAME = 'course-v1';

export async function cacheCourseManifest(manifest: CourseManifest): Promise<void> {
  const cache = await caches.open(CACHE_NAME);
  
  // Cache lesson video URLs and metadata
  const fetchPromises = manifest.assets.map(asset =>
    fetch(asset.url).then(response => cache.put(asset.lessonId, response))
  );

  await Promise.allSettled(fetchPromises); // don't fail if one asset misses
  
  // Store metadata in IndexedDB for offline UI rendering
  await db.courseManifests.put(manifest.course);
}
```

```typescript
// application/usecases/getLessonContent.ts
export async function getLessonContent(lessonId: string): Promise<LessonContent> {
  if (navigator.onLine) {
    try {
      return await api.lessons.getContent(lessonId);
    } catch {
      return getFromCache(lessonId); // graceful fallback
    }
  }
  return getFromCache(lessonId); // offline path
}

async function getFromCache(lessonId: string): Promise<LessonContent> {
  const cache = await caches.open(CACHE_NAME);
  const cached = await cache.match(lessonId);
  if (!cached) throw new Error('Content not available offline');
  return cached.json();
}
```

```typescript
// Adaptive video player — switches quality based on connection
export function useAdaptiveVideo(lessonId: string) {
  const [quality, setQuality] = useState<'240p' | '480p' | '720p'>('480p');

  useEffect(() => {
    const connection = (navigator as any).connection;
    if (connection) {
      const effectiveType = connection.effectiveType;
      if (effectiveType === '2g') setQuality('240p');
      else if (effectiveType === '3g') setQuality('480p');
      else setQuality('720p');
    }
  }, []);

  return { quality };
}
```

---

## Video Pipeline

Raw video goes through a transcoding pipeline before being served:

```
Instructor uploads → S3 (sa-east-1)
        ↓
AWS MediaConvert (transcode to 240p / 480p / 720p HLS)
        ↓
Cloudflare R2 (store all variants, globally cached)
        ↓
Cloudflare Stream or signed R2 URLs → PWA player (HLS.js)
```

HLS (HTTP Live Streaming) is ideal here because it supports adaptive bitrate — the player automatically drops to 240p if bandwidth degrades mid-lesson.

---

## Infrastructure as Code (summary)

Key AWS resources to provision via Terraform or CDK:

- **ECS Fargate** — run Go microservices (no EC2 management)
- **RDS PostgreSQL** — primary data store, `sa-east-1`
- **ElastiCache Redis** — session cache, rate limiting
- **S3** — raw video upload bucket
- **SQS** — async queues (payment notifications, email jobs)
- **MediaConvert** — video transcoding jobs
- **Secrets Manager** — store DB credentials, JWT secrets

Cloudflare handles: R2 (video storage/CDN), Workers (edge auth token validation), WAF, and DNS.

---

## What This Gets You

| Constraint | Solution |
|---|---|
| Low bandwidth | HLS adaptive bitrate + 240p fallback |
| Offline usage | PWA Service Worker + IndexedDB + pre-cached videos |
| No Stripe/PayPal | Manual USDT/Zinli flow + admin verification queue |
| Latency to VE | Cloudflare edge + AWS São Paulo |
| Power outages | Progress sync on reconnect, resume from last position |
| Low-end devices | Lightweight PWA, no native app store dependency |

---

Want me to go deeper on any specific piece — the video pipeline, the payment verification flow, the PWA service worker strategy, or the IaC setup?