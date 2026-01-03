# Product Requirements Document (PRD)
## Restaurant Finder Application

**Version:** 1.0
**Last Updated:** January 2, 2026
**Product Owner:** Workshop Team
**Target Release:** IPNSummit 2026 Workshop

---

## 1. Executive Summary

Restaurant Finder is a web-based application designed to help users discover restaurants near their location. Built as an educational platform for the "Becoming a 100x Dev" workshop, it demonstrates modern web development practices, AI-assisted coding, and serves as a foundation for hands-on learning exercises.

### Vision
Empower users to find great dining options near them with a simple, intuitive interface while providing developers with a production-quality codebase for learning modern web development practices.

---

## 2. Product Objectives

### Primary Goals
1. **User Experience**: Provide a fast, intuitive restaurant discovery experience
2. **Educational Value**: Serve as a teaching tool for AI-assisted development
3. **Code Quality**: Demonstrate best practices in modern web development
4. **Scalability**: Build a foundation that can be extended with real APIs

### Success Metrics
- Page load time < 2 seconds
- Search results returned in < 1 second
- Mobile responsive design (supports screens 320px+)
- 100% test coverage for critical paths
- Zero ESLint errors in production builds

---

## 3. Target Audience

### Primary Users
- **Workshop Participants**: Developers learning AI-assisted coding
- **End Users**: People looking for nearby restaurants

### User Personas

#### Persona 1: "Alex the Workshop Participant"
- **Role**: Junior to mid-level developer
- **Goals**: Learn modern web development and AI coding tools
- **Pain Points**: Understanding how to structure a full-stack application
- **Needs**: Clear, well-documented code with learning exercises

#### Persona 2: "Maria the Foodie"
- **Role**: Restaurant explorer
- **Goals**: Find new dining options quickly
- **Pain Points**: Too many steps to discover nearby restaurants
- **Needs**: Simple search, clear results, distance information

---

## 4. Features & Requirements

### 4.1 Core Features (MVP - Implemented)

#### F1: Location-Based Search
**Priority:** P0 (Critical)

**Description:** Users can search for restaurants by entering an address, city, or zip code.

**Requirements:**
- Input field accepts free-form text (address, city, zip code)
- Mock geocoding converts addresses to coordinates
- Falls back to San Francisco default if location unknown
- Displays loading state during search

**Acceptance Criteria:**
- ✅ User can enter any text in search field
- ✅ System returns results within 1 second
- ✅ Invalid inputs show appropriate fallback
- ✅ Loading indicator appears during search

#### F2: Restaurant Results Display
**Priority:** P0 (Critical)

**Description:** Display the 5 nearest restaurants sorted by distance.

**Requirements:**
- Show restaurant name, cuisine, rating, price range
- Display distance in kilometers (1 decimal place)
- Show address and phone number
- Grid layout: 1 column (mobile), 2 columns (tablet), 3 columns (desktop)

**Acceptance Criteria:**
- ✅ Results show top 5 nearest restaurants
- ✅ Distance calculated using Haversine formula
- ✅ Responsive grid layout works on all screen sizes
- ✅ All restaurant data fields render correctly

#### F3: Distance Calculation
**Priority:** P0 (Critical)

**Description:** Calculate great-circle distance between user location and restaurants.

**Requirements:**
- Use Haversine formula for accuracy
- Return distance in kilometers
- Sort results by ascending distance

**Acceptance Criteria:**
- ✅ Distance calculations accurate to 0.1km
- ✅ Results always sorted nearest to farthest
- ✅ Algorithm handles edge cases (same location, antipodal points)

### 4.2 Future Features (Not Implemented)

#### F4: Opening Hours Display
**Priority:** P1 (High)

**Description:** Show restaurant opening and closing hours.

**Requirements:**
- Display hours in 12-hour format (e.g., "11:00 AM - 10:00 PM")
- Indicate if restaurant is currently open/closed
- Show "Open Now" badge for operating restaurants

**Status:** Data available in backend, UI not implemented (Workshop Exercise 1)

#### F5: Real Map Integration
**Priority:** P1 (High)

**Description:** Interactive map showing restaurant locations with markers.

**Requirements:**
- Integrate Google Maps or Mapbox API
- Show user location marker
- Display restaurant markers with info windows
- Allow clicking marker to see restaurant details

**Status:** Placeholder component exists (Workshop Exercise 3)

#### F6: Geolocation Support
**Priority:** P1 (High)

**Description:** Auto-detect user's current location using browser geolocation API.

**Requirements:**
- "Use My Location" button
- Request browser location permission
- Auto-populate search with coordinates
- Fallback to manual entry if denied

**Status:** Not implemented (Workshop Exercise 3)

#### F7: Favorites System
**Priority:** P2 (Medium)

**Description:** Allow users to save favorite restaurants.

**Requirements:**
- Toggle favorite icon on restaurant cards
- Persist favorites (localStorage or database)
- View saved favorites in separate section
- Remove from favorites

**Status:** API endpoint created but unused (Workshop Exercise 2)

#### F8: Advanced Filtering
**Priority:** P2 (Medium)

**Description:** Filter restaurants by cuisine, rating, and price range.

**Requirements:**
- Cuisine dropdown (Italian, Chinese, Mexican, etc.)
- Minimum rating slider (1-5 stars)
- Price range selector ($, $$, $$$, $$$$)
- Apply filters without page reload

**Status:** Backend logic exists in POST endpoint, UI not implemented

---

## 5. Technical Specifications

### 5.1 Technology Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| **Framework** | Next.js | 14.2.3 |
| **Language** | TypeScript | 5.x |
| **Styling** | Tailwind CSS | 3.x |
| **Testing** | Jest + React Testing Library | Latest |
| **Linting** | ESLint + Prettier | Latest |
| **Deployment** | Vercel | - |

### 5.2 Architecture

```
┌─────────────────────────────────────────────┐
│           Client (Browser)                   │
│  ┌────────────┐  ┌──────────────────────┐   │
│  │ SearchForm │  │  RestaurantCard[]    │   │
│  └────────────┘  └──────────────────────┘   │
└─────────────┬───────────────────────────────┘
              │
              │ HTTP GET /api/restaurants?address=...
              │
┌─────────────▼───────────────────────────────┐
│         Next.js API Routes                   │
│  ┌──────────────────────────────────────┐   │
│  │  GET /api/restaurants                │   │
│  │  - mockGeocode(address)              │   │
│  │  - calculateDistance()               │   │
│  │  - sort & return top 5               │   │
│  └──────────────────────────────────────┘   │
└─────────────┬───────────────────────────────┘
              │
              │ Read from file system
              │
┌─────────────▼───────────────────────────────┐
│      data/restaurants.json                   │
│      (25 mock restaurants in SF)             │
└─────────────────────────────────────────────┘
```

### 5.3 Data Models

#### Restaurant Interface
```typescript
interface Restaurant {
  id: string;
  name: string;
  address: string;
  cuisine: string;
  rating: number;           // 1.0 - 5.0
  priceRange: string;       // $, $$, $$$, $$$$
  openingHours: string;     // 24-hour format "HH:mm"
  closingHours: string;     // 24-hour format "HH:mm"
  latitude: number;
  longitude: number;
  phone: string;
  description: string;
  distance?: number;        // Added dynamically (km)
}
```

### 5.4 API Specifications

#### GET /api/restaurants

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `address` | string | No | Address, city, or zip code |
| `lat` | number | No | Latitude coordinate |
| `lng` | number | No | Longitude coordinate |

**Response:**
```json
{
  "restaurants": [
    {
      "id": "1",
      "name": "Golden Dragon",
      "address": "123 Main Street, San Francisco, CA 94102",
      "cuisine": "Chinese",
      "rating": 4.5,
      "priceRange": "$$",
      "openingHours": "11:00",
      "closingHours": "22:00",
      "latitude": 37.7849,
      "longitude": -122.4094,
      "phone": "(415) 555-0101",
      "description": "Authentic Cantonese cuisine",
      "distance": 0.5
    }
  ],
  "searchLocation": {
    "latitude": 37.7749,
    "longitude": -122.4194,
    "address": "San Francisco"
  }
}
```

**Error Responses:**
- `400 Bad Request`: Invalid coordinates
- `500 Internal Server Error`: Server processing error

---

## 6. User Stories

### Epic 1: Restaurant Discovery

**US-1.1: Search by Address**
```
As a user
I want to search for restaurants by entering my address
So that I can find nearby dining options without manually entering coordinates

Acceptance Criteria:
- I can type any address format (street, city, zip)
- Results appear within 1 second
- I see a loading indicator during search
- Invalid addresses fall back to default location
```

**US-1.2: View Restaurant Details**
```
As a user
I want to see detailed information about each restaurant
So that I can make an informed dining decision

Acceptance Criteria:
- I see restaurant name, cuisine type, rating
- I see price range, address, phone number
- I see distance from my location
- Information is clearly formatted and readable
```

**US-1.3: Mobile Restaurant Search**
```
As a mobile user
I want the app to work perfectly on my phone
So that I can search for restaurants on the go

Acceptance Criteria:
- Layout adapts to my screen size
- Touch targets are appropriately sized
- Text is readable without zooming
- Page loads quickly on cellular networks
```

### Epic 2: Developer Experience (Workshop)

**US-2.1: Code Exploration**
```
As a workshop participant
I want well-documented, clean code
So that I can understand the architecture and learn best practices

Acceptance Criteria:
- Code has clear comments and type definitions
- File structure follows Next.js conventions
- README includes setup instructions
- CLAUDE.md guides AI coding assistant
```

**US-2.2: Practice Exercises**
```
As a workshop participant
I want intentional gaps in the implementation
So that I can practice adding features with AI assistance

Acceptance Criteria:
- Dead code is clearly marked with TODO comments
- Incomplete features have implementation hints
- Test files guide expected behavior
- Exercises range from beginner to advanced
```

---

## 7. Design Requirements

### 7.1 User Interface

**Design Principles:**
- **Simplicity**: Single-page interface, no complex navigation
- **Clarity**: Clear visual hierarchy, readable typography
- **Responsiveness**: Mobile-first design approach
- **Accessibility**: Semantic HTML, ARIA labels, keyboard navigation

**Color Palette:**
- Primary: Red (#EF4444) - Tailwind red-500
- Background: Gray gradient (#F9FAFB to #FFFFFF)
- Text: Gray-900 (#111827)
- Borders: Gray-300 (#D1D5DB)

**Typography:**
- System fonts (Apple system, Segoe UI, etc.)
- Headings: Bold weight
- Body: Normal weight
- Code: Monospace for technical content

### 7.2 Responsive Breakpoints

| Breakpoint | Width | Layout |
|------------|-------|--------|
| Mobile | < 640px | 1 column grid |
| Tablet | 640px - 1024px | 2 column grid |
| Desktop | > 1024px | 3 column grid |

---

## 8. Quality Requirements

### 8.1 Performance

| Metric | Target | Current |
|--------|--------|---------|
| First Contentful Paint | < 1.5s | ✅ |
| Time to Interactive | < 2.5s | ✅ |
| Search Response Time | < 1s | ✅ |
| Lighthouse Performance Score | > 90 | ✅ |

### 8.2 Testing

**Test Coverage Targets:**
- Unit Tests: > 80% coverage
- Integration Tests: All API routes
- Component Tests: All user-facing components
- E2E Tests: Critical user flows

**Test Categories:**
```
__tests__/
├── api/
│   └── restaurants/
│       └── route.test.ts
├── components/
│   ├── RestaurantCard.test.tsx
│   └── SearchForm.test.tsx
└── utils/
    └── distance.test.ts
```

### 8.3 Code Quality

**Standards:**
- ESLint: Zero errors in production
- Prettier: All files formatted
- TypeScript: Strict mode enabled
- No `any` types in production code

---

## 9. Constraints & Assumptions

### Constraints
1. **Mock Data Only**: Uses static JSON file (no real database)
2. **San Francisco Only**: Mock data limited to SF Bay Area
3. **No Authentication**: No user accounts or login
4. **Client-Side Only**: No persistent server-side storage

### Assumptions
1. Users have modern browsers (last 2 versions)
2. JavaScript enabled
3. Internet connection available
4. Users comfortable with English interface

---

## 10. Dependencies & Integrations

### Current Dependencies
```json
{
  "next": "14.2.3",
  "react": "^18",
  "typescript": "^5",
  "tailwindcss": "^3.3.0"
}
```

### Future Integration Opportunities
- **Google Maps API**: Real map visualization
- **Google Places API**: Real restaurant data
- **Authentication**: NextAuth.js for user accounts
- **Database**: PostgreSQL/MongoDB for persistent storage
- **Analytics**: Google Analytics or Vercel Analytics

---

## 11. Risks & Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Mock data limitations | Medium | High | Document as educational constraint |
| Browser compatibility | Low | Low | Use standard web APIs only |
| Performance on mobile | Medium | Low | Optimize bundle size, lazy loading |
| Workshop time constraints | High | Medium | Prepare multiple exercise difficulty levels |

---

## 12. Release Plan

### Phase 1: MVP (Current - Completed)
- ✅ Basic location search
- ✅ Restaurant results display
- ✅ Distance calculation
- ✅ Responsive design
- ✅ Deployed to Vercel

### Phase 2: Workshop Enhancements (TBD)
- Opening hours display (Exercise 1)
- Dead code removal (Exercise 2)
- Map integration (Exercise 3)
- Advanced filtering (Exercise 4)
- Unit test expansion (Exercise 5)

### Phase 3: Production Features (Future)
- Real API integration
- User authentication
- Favorites persistence
- Review and ratings system
- Restaurant recommendations

---

## 13. Success Criteria

### Workshop Success
- [ ] All participants complete at least 2 exercises
- [ ] Code quality maintained throughout modifications
- [ ] Deployment successful with zero errors
- [ ] Participants understand AI-assisted development workflow

### Product Success
- [ ] < 2s page load time on 3G networks
- [ ] 0 critical accessibility issues
- [ ] 100% of core user flows tested
- [ ] Positive feedback from workshop participants

---

## 14. Appendix

### A. Workshop Exercise Summary

| Exercise | Difficulty | Estimated Time | Skills Practiced |
|----------|-----------|----------------|------------------|
| 1: Display Opening Hours | Beginner | 15 min | UI components, data display |
| 2: Remove Dead Code | Beginner | 20 min | Code analysis, refactoring |
| 3: Add Geolocation | Intermediate | 30 min | Browser APIs, error handling |
| 4: Map Integration | Advanced | 45 min | Third-party APIs, state management |
| 5: Unit Testing | Intermediate | 30 min | Test writing, TDD practices |

### B. Reference Links

- [Next.js Documentation](https://nextjs.org/docs)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [React Testing Library](https://testing-library.com/react)
- [Vercel Deployment](https://vercel.com/docs)

### C. Glossary

- **Haversine Formula**: Mathematical formula to calculate great-circle distances between two points on a sphere
- **Mock Data**: Fake data used for development and testing
- **Geocoding**: Converting addresses to geographic coordinates
- **API Route**: Server-side endpoint in Next.js app

---

**Document History:**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-01-02 | Claude Sonnet 4.5 | Initial PRD creation |

