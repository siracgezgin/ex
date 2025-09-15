# 🏗️ Mimari Dokümantasyonu

> **Futbol Ligi Simülasyonu - Angular 20 Mimari Detayları**

Bu dokümantasyon, projenin mimari yapısını, tasarım desenlerini ve veri akışını detaylı olarak açıklar. Angular 20'nin en son özelliklerini kullanarak geliştirilmiş, modern ve ölçeklenebilir bir uygulama mimarisi sunar.

## 📋 İçindekiler

- [🎯 Mimari Genel Bakış](#-mimari-genel-bakış)
- [🏛️ Mimari Desenler](#️-mimari-desenler)
- [🔄 Veri Akışı](#-veri-akışı)
- [📊 State Yönetimi](#-state-yönetimi)
- [🎨 UI/UX Mimari](#-uiux-mimari)
- [🔧 Servis Katmanı](#-servis-katmanı)
- [📱 Responsive Tasarım](#-responsive-tasarım)
- [⚡ Performans Optimizasyonları](#-performans-optimizasyonları)
- [🧪 Test Mimarisi](#-test-mimarisi)
- [🔐 Güvenlik Mimarisi](#-güvenlik-mimarisi)
- [📈 Monitoring ve Logging](#-monitoring-ve-logging)
- [🚀 Deployment Mimarisi](#-deployment-mimarisi)

---

## 🎯 Mimari Genel Bakış

### 🏗️ Genel Mimari

Bu proje, **Clean Architecture** prensiplerini takip eden, **layered architecture** (katmanlı mimari) kullanır. Her katman kendi sorumluluğuna odaklanır ve diğer katmanlarla gevşek bağlı (loosely coupled) bir şekilde etkileşim kurar.

```
┌─────────────────────────────────────────────────────────────┐
│                    Presentation Layer                       │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │
│  │   Dashboard │ │ Standings   │ │ Match       │          │
│  │ Component   │ │ Component   │ │ Results     │          │
│  │             │ │             │ │ Component   │          │
│  └─────────────┘ └─────────────┘ └─────────────┘          │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │
│  │ Champion    │ │ Loading     │ │ Error       │          │
│  │ Celebration │ │ Spinner     │ │ Handler     │          │
│  └─────────────┘ └─────────────┘ └─────────────┘          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    State Management                         │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │
│  │   Actions   │ │  Reducers   │ │  Selectors  │          │
│  │             │ │             │ │             │          │
│  └─────────────┘ └─────────────┘ └─────────────┘          │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │
│  │   Effects   │ │   Store     │ │   DevTools  │          │
│  │             │ │             │ │             │          │
│  └─────────────┘ └─────────────┘ └─────────────┘          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Service Layer                            │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │
│  │ Simulation  │ │   Effects   │ │   Models    │          │
│  │ Service     │ │             │ │             │          │
│  └─────────────┘ └─────────────┘ └─────────────┘          │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │
│  │ Utility     │ │   Constants │ │   Types     │          │
│  │ Services    │ │             │ │             │          │
│  └─────────────┘ └─────────────┘ └─────────────┘          │
└─────────────────────────────────────────────────────────────┘
```

### 🎯 Mimari Prensipleri

#### **1. Separation of Concerns (Sorumlulukların Ayrılması)**
Her katman kendi sorumluluğuna odaklanır:
- **Presentation Layer**: UI/UX ve kullanıcı etkileşimi
- **State Management**: Veri yönetimi ve durum kontrolü
- **Service Layer**: İş mantığı ve veri işleme

#### **2. Single Responsibility Principle (Tek Sorumluluk Prensibi)**
Her component, servis ve modül tek bir işten sorumludur:
- `LeagueDashboardComponent`: Ana dashboard yönetimi
- `StandingsTableComponent`: Puan tablosu görüntüleme
- `LeagueSimulationService`: Lig simülasyon mantığı

#### **3. Dependency Injection (Bağımlılık Enjeksiyonu)**
Loose coupling için DI kullanımı:
```typescript
constructor(
  private leagueService: LeagueSimulationService,
  private store: Store<AppState>
) {}
```

#### **4. Reactive Programming (Reaktif Programlama)**
RxJS ile asenkron veri yönetimi:
```typescript
this.currentStandings$ = this.store.select(selectCurrentStandings);
```

#### **5. Immutable State (Değişmez Durum)**
NgRx ile değişmez state yönetimi:
```typescript
on(LeagueActions.playSpecificWeekSuccess, (state, { matches }) => ({
  ...state, // Yeni state objesi oluştur
  matches: [...state.matches, ...matches]
}))
```

#### **6. OnPush Change Detection**
Performans optimizasyonu için OnPush stratejisi:
```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
```

### 🏛️ Mimari Katmanları Detayı

#### **Presentation Layer (Sunum Katmanı)**
- **Amaç**: Kullanıcı arayüzü ve etkileşim
- **Teknolojiler**: Angular Components, PrimeNG, Tailwind CSS
- **Sorumluluklar**:
  - Kullanıcı girişlerini yakalama
  - Veri görüntüleme
  - UI state yönetimi
  - Responsive tasarım

#### **State Management Layer (Durum Yönetimi Katmanı)**
- **Amaç**: Uygulama durumunu merkezi olarak yönetme
- **Teknolojiler**: NgRx (Store, Effects, Actions, Reducers, Selectors)
- **Sorumluluklar**:
  - Veri akışını yönetme
  - Side effect'leri işleme
  - State'i güncelleme
  - Veri seçimi ve filtreleme

#### **Service Layer (Servis Katmanı)**
- **Amaç**: İş mantığı ve veri işleme
- **Teknolojiler**: Angular Services, RxJS
- **Sorumluluklar**:
  - Lig simülasyonu
  - Veri hesaplamaları
  - Utility fonksiyonları
  - Business logic

---

## 🏛️ Mimari Desenler

### 1. **Standalone Components Architecture**

#### **Avantajları:**
- **Modüler yapı** - Her component bağımsız
- **Tree-shaking** - Kullanılmayan kodlar otomatik temizlenir
- **Hızlı geliştirme** - NgModule yapılandırması gerekmez
- **Kolay test** - Component'ler izole test edilebilir

#### **Uygulama:**
```typescript
@Component({
  selector: 'app-league-dashboard',
  standalone: true,
  imports: [
    CommonModule,
    CardModule,
    ButtonModule,
    ProgressBarModule,
    StandingsTableComponent,
    MatchResultsComponent
  ],
  changeDetection: ChangeDetectionStrategy.OnPush,
  templateUrl: './league-dashboard.component.html',
  styleUrls: ['./league-dashboard.component.scss']
})
export class LeagueDashboardComponent {
  // Component logic
}
```

### 2. **NgRx State Management Pattern**

#### **Redux Pattern Uygulaması:**
```
Action → Effect → Service → Reducer → Store → Selector → Component
```

#### **State Yapısı:**
```typescript
interface LeagueState {
  teams: Team[];              // Takım listesi
  matches: Match[];           // Tüm maçlar
  currentWeek: number;        // Mevcut hafta
  totalWeeks: number;         // Toplam hafta (6)
  standings: TeamStats[];     // Puan tablosu
  weeklyMatches: WeekMatches[]; // Haftalık maçlar
  isSeasonFinished: boolean;  // Sezon bitti mi?
  champion: Team | null;      // Şampiyon
  loading: boolean;           // Yükleniyor mu?
  error: string | null;       // Hata mesajı
}
```

### 3. **Service Layer Pattern**

#### **Business Logic Separation:**
```typescript
@Injectable({
  providedIn: 'root'
})
export class LeagueSimulationService {
  // Lig simülasyon mantığı
  simulateMatch(homeTeam: Team, awayTeam: Team): MatchResult {
    // Maç simülasyonu
  }
  
  calculateStandings(teams: Team[], matches: Match[]): TeamStats[] {
    // Puan tablosu hesaplama
  }
}
```

### 4. **Component Communication Pattern**

#### **Parent-Child Communication:**
```typescript
// Parent Component
@Component({
  template: `
    <app-standings-table 
      [standings]="currentStandings"
      [isSeasonFinished]="isSeasonFinished"
      (teamSelected)="onTeamSelected($event)">
    </app-standings-table>
  `
})
export class LeagueDashboardComponent {
  @Input() standings: TeamStats[] = [];
  @Output() teamSelected = new EventEmitter<Team>();
}
```

---

## 🔄 Veri Akışı

### 📊 Veri Akış Diyagramı

```
User Action
    │
    ▼
┌─────────────┐
│ Component   │ ──► Action Dispatch
└─────────────┘
    │
    ▼
┌─────────────┐
│   Effect    │ ──► Service Call
└─────────────┘
    │
    ▼
┌─────────────┐
│  Service    │ ──► Business Logic
└─────────────┘
    │
    ▼
┌─────────────┐
│  Reducer    │ ──► State Update
└─────────────┘
    │
    ▼
┌─────────────┐
│   Store     │ ──► State Storage
└─────────────┘
    │
    ▼
┌─────────────┐
│  Selector   │ ──► Data Selection
└─────────────┘
    │
    ▼
┌─────────────┐
│ Component   │ ──► UI Update
└─────────────┘
```

### 🎯 Örnek Veri Akışı: Lig Başlatma

#### 1. **User Action**
```typescript
// Kullanıcı "1. Hafta Ligi Başlat" butonuna tıklar
playSpecificWeek(1) {
  this.store.dispatch(playSpecificWeek({ week: 1 }));
}
```

#### 2. **Action Dispatch**
```typescript
// league.actions.ts
export const playSpecificWeek = createAction(
  '[League] Play Specific Week',
  props<{ week: number }>()
);
```

#### 3. **Effect Processing**
```typescript
// league.effects.ts
playSpecificWeek$ = createEffect(() =>
  this.actions$.pipe(
    ofType(LeagueActions.playSpecificWeek),
    switchMap(({ week }) =>
      this.leagueService.playSpecificWeek(week).pipe(
        map(matches => LeagueActions.playSpecificWeekSuccess({ matches })),
        catchError(error => of(LeagueActions.playSpecificWeekFailure({ error })))
      )
    )
  )
);
```

#### 4. **Service Call**
```typescript
// league-simulation.service.ts
playSpecificWeek(week: number): Observable<Match[]> {
  const weekMatches = this.getWeekMatches(week);
  return this.simulateWeekMatches(weekMatches);
}
```

#### 5. **Reducer Update**
```typescript
// league.reducer.ts
on(LeagueActions.playSpecificWeekSuccess, (state, { matches }) => ({
  ...state,
  matches: [...state.matches, ...matches],
  currentWeek: state.currentWeek + 1,
  standings: calculateStandings(state.teams, [...state.matches, ...matches])
}))
```

#### 6. **Selector & Component Update**
```typescript
// league.selectors.ts
export const selectCurrentStandings = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.standings
);

// Component
this.currentStandings$ = this.store.select(selectCurrentStandings);
```

---

## 📊 State Yönetimi

### 🏪 NgRx Store Yapısı

#### **Store Konfigürasyonu:**
```typescript
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideStore({
      league: leagueReducer
    }),
    provideEffects([LeagueEffects]),
    provideStoreDevtools({
      maxAge: 25,
      logOnly: environment.production
    })
  ]
};
```

#### **State Interface:**
```typescript
export interface LeagueState {
  // Takım verileri
  teams: Team[];
  
  // Maç verileri
  matches: Match[];
  weeklyMatches: WeekMatches[];
  
  // Lig durumu
  currentWeek: number;
  totalWeeks: number;
  isSeasonFinished: boolean;
  
  // Hesaplanmış veriler
  standings: TeamStats[];
  champion: Team | null;
  
  // UI durumu
  loading: boolean;
  error: string | null;
}
```

### 🎯 Selector Pattern

#### **Base Selector:**
```typescript
export const selectLeagueState = createFeatureSelector<LeagueState>('league');
```

#### **Derived Selectors:**
```typescript
export const selectTeams = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.teams
);

export const selectCurrentStandings = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.standings
);

export const selectIsSeasonFinished = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.isSeasonFinished
);
```

#### **Complex Selectors:**
```typescript
export const selectChampion = createSelector(
  selectCurrentStandings,
  selectIsSeasonFinished,
  (standings, isFinished) => 
    isFinished && standings.length > 0 ? standings[0].team : null
);
```

---

## 🎨 UI/UX Mimari

### 🎯 Component Hierarchy

```
AppComponent
└── LeagueDashboardComponent
    ├── StandingsTableComponent
    ├── MatchResultsComponent
    └── ChampionCelebrationComponent
```

### 🎨 Styling Architecture

#### **1. Tailwind CSS (Utility-First)**
```css
/* Utility sınıfları */
.bg-gradient-to-br.from-slate-50.to-indigo-100
.hover:bg-gray-50.transition-all.duration-300
.text-center.font-bold.text-lg
```

#### **2. SCSS (Component Styles)**
```scss
// Component-specific styles
.standings-table {
  .standings-grid-header {
    display: grid;
    grid-template-columns: 50px 60px 200px 50px 50px 50px 50px 50px 50px 70px;
  }
  
  .standings-grid-row {
    display: grid;
    grid-template-columns: 50px 60px 200px 50px 50px 50px 50px 50px 50px 70px;
    align-items: center;
    padding: 0.75rem 0;
    border-bottom: 1px solid #e5e7eb;
  }
}
```

#### **3. CSS Variables (Global)**
```css
:root {
  --primary-color: #3b82f6;
  --success-color: #16a34a;
  --warning-color: #d97706;
  --danger-color: #dc2626;
  
  --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1);
}
```

### 🎭 Animation Architecture

#### **CSS Animations:**
```css
@keyframes fadeIn {
  from { 
    opacity: 0; 
    transform: translateY(10px); 
  }
  to { 
    opacity: 1; 
    transform: translateY(0); 
  }
}

@keyframes slideUp {
  from { 
    transform: translateY(20px); 
    opacity: 0; 
  }
  to { 
    transform: translateY(0); 
    opacity: 1; 
  }
}

@keyframes trophyBounce {
  0%, 20%, 50%, 80%, 100% {
    transform: translateY(0);
  }
  40% {
    transform: translateY(-10px);
  }
  60% {
    transform: translateY(-5px);
  }
}
```

#### **Animation Classes:**
```css
.fade-in {
  animation: fadeIn 0.5s ease-in-out;
}

.slide-up {
  animation: slideUp 0.6s ease-out;
}

.trophy-bounce {
  animation: trophyBounce 2s ease-in-out infinite;
}

.hover-lift:hover {
  transform: translateY(-4px);
  transition: transform 0.3s ease;
}
```

---

## 🔧 Servis Katmanı

### 🎯 Service Architecture

#### **LeagueSimulationService:**
```typescript
@Injectable({
  providedIn: 'root'
})
export class LeagueSimulationService {
  
  // Maç simülasyonu
  simulateMatch(homeTeam: Team, awayTeam: Team): MatchResult {
    const homeStrength = homeTeam.strength;
    const awayStrength = awayTeam.strength;
    
    // Basit simülasyon algoritması
    const homeScore = this.calculateScore(homeStrength, true);
    const awayScore = this.calculateScore(awayStrength, false);
    
    return {
      homeScore,
      awayScore,
      isPlayed: true
    };
  }
  
  // Haftalık maçları simüle et
  simulateWeekMatches(weekMatches: WeekMatches): Observable<Match[]> {
    return of(weekMatches.matches.map(match => ({
      ...match,
      ...this.simulateMatch(match.homeTeam, match.awayTeam)
    })));
  }
  
  // Puan tablosu hesaplama
  calculateStandings(teams: Team[], matches: Match[]): TeamStats[] {
    return teams.map(team => this.calculateTeamStats(team, matches));
  }
}
```

### 🔄 Dependency Injection

#### **Service Registration:**
```typescript
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    LeagueSimulationService,
    // Diğer servisler...
  ]
};
```

#### **Service Injection:**
```typescript
// Component'te kullanım
constructor(
  private leagueService: LeagueSimulationService,
  private store: Store<AppState>
) {}
```

---

## 📱 Responsive Tasarım

### 📐 Breakpoint Strategy

#### **Mobile First Approach:**
```css
/* Base styles (Mobile) */
.champion-name {
  font-size: 2rem;
  text-align: center;
}

/* Tablet */
@media (min-width: 768px) {
  .champion-name {
    font-size: 2.5rem;
  }
  
  .champion-stats {
    flex-direction: row;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .champion-name {
    font-size: 3rem;
  }
  
  .card-body {
    padding: 1.25rem;
  }
}
```

### 🎯 Grid System

#### **Standings Table Grid:**
```scss
.standings-table {
  .standings-grid-header,
  .standings-grid-row {
    display: grid;
    grid-template-columns: 
      50px    // Sıra
      60px    // Logo
      200px   // Takım adı
      50px    // O
      50px    // G
      50px    // B
      50px    // M
      50px    // A
      50px    // Y
      70px;   // Puan
    
    @media (max-width: 768px) {
      grid-template-columns: 
        40px    // Sıra
        50px    // Logo
        150px   // Takım adı
        40px    // O
        40px    // G
        40px    // B
        40px    // M
        40px    // A
        40px    // Y
        60px;   // Puan
    }
  }
}
```

---

## ⚡ Performans Optimizasyonları

### 🚀 Change Detection Strategy

#### **OnPush Strategy:**
```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class LeagueDashboardComponent {
  // Component sadece input değişikliklerinde güncellenir
}
```

#### **Manual Change Detection:**
```typescript
constructor(private cdr: ChangeDetectorRef) {}

updateView() {
  this.cdr.markForCheck();
}
```

### 🎯 Lazy Loading

#### **Route-based Lazy Loading:**
```typescript
const routes: Routes = [
  {
    path: 'league',
    loadComponent: () => import('./league/league-dashboard.component')
      .then(m => m.LeagueDashboardComponent)
  }
];
```

### 📦 Bundle Optimization

#### **Tree Shaking:**
```typescript
// Sadece kullanılan PrimeNG modülleri import edilir
import { CardModule } from 'primeng/card';
import { ButtonModule } from 'primeng/button';
// Tüm PrimeNG import edilmez
```

#### **Code Splitting:**
```typescript
// Dynamic imports
const StandingsTableComponent = () => import('./standings-table.component');
```

---

## 🧪 Test Mimarisi

### 🎯 Test Strategy

#### **Unit Tests:**
```typescript
describe('LeagueDashboardComponent', () => {
  let component: LeagueDashboardComponent;
  let fixture: ComponentFixture<LeagueDashboardComponent>;
  let store: MockStore;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [LeagueDashboardComponent],
      providers: [
        provideMockStore({
          initialState: mockLeagueState
        })
      ]
    });
  });

  it('should calculate standings correctly', () => {
    // Test implementation
  });
});
```

#### **Integration Tests:**
```typescript
describe('League Simulation Integration', () => {
  it('should play all matches and determine champion', () => {
    // Integration test
  });
});
```

### 🔧 Test Utilities

#### **Mock Data:**
```typescript
export const mockTeams: Team[] = [
  { id: 1, name: 'Galatasaray', strength: 0.8, color: '#FFD700', logo: 'gs-logo.png' },
  { id: 2, name: 'Fenerbahçe', strength: 0.7, color: '#FFD700', logo: 'fb-logo.png' }
];
```

---

## 🎉 Sonuç

Bu mimari dokümantasyonu, projenin teknik yapısını ve tasarım kararlarını detaylı olarak açıklar. Angular 20'nin modern özelliklerini kullanarak, ölçeklenebilir, sürdürülebilir ve performanslı bir uygulama geliştirilmiştir.

### 🌟 Mimari Avantajları

1. **Modüler Yapı** - Her component bağımsız ve test edilebilir
2. **Scalable Architecture** - Yeni özellikler kolayca eklenebilir
3. **Performance Optimized** - OnPush strategy ve lazy loading
4. **Maintainable Code** - Clean architecture prensipleri
5. **Modern Angular** - En son Angular 20 özellikleri

### 🚀 Gelecek Geliştirmeler

- [ ] **Micro-frontend** architecture
- [ ] **Server-side rendering** (SSR)
- [ ] **Progressive Web App** (PWA)
- [ ] **Real-time updates** (WebSocket)
- [ ] **Advanced caching** strategies

---

## 🔐 Güvenlik Mimarisi

### 🛡️ Güvenlik Katmanları

#### **1. Input Validation (Giriş Doğrulama)**
```typescript
// Component seviyesinde validation
export class MatchResultsComponent {
  editMatch(match: Match): void {
    // Input validation
    if (!match || !match.id) {
      throw new Error('Invalid match data');
    }
    
    if (match.homeScore < 0 || match.awayScore < 0) {
      throw new Error('Scores cannot be negative');
    }
    
    this.editingMatch = { ...match };
  }
}
```

#### **2. Type Safety (Tip Güvenliği)**
```typescript
// Strict TypeScript konfigürasyonu
interface Team {
  readonly id: number;        // Immutable ID
  readonly name: string;      // Immutable name
  strength: number;          // Mutable strength
  color: string;             // Mutable color
  logo: string;              // Mutable logo
}

// Readonly interfaces for data integrity
interface ReadonlyMatch {
  readonly id: number;
  readonly homeTeam: Team;
  readonly awayTeam: Team;
  readonly week: number;
  readonly date: Date;
}
```

#### **3. Error Handling (Hata Yönetimi)**
```typescript
// Global error handler
@Injectable()
export class GlobalErrorHandler implements ErrorHandler {
  handleError(error: any): void {
    console.error('Global error:', error);
    
    // Log to external service
    this.logError(error);
    
    // Show user-friendly message
    this.showErrorMessage();
  }
  
  private logError(error: any): void {
    // Send to logging service (Sentry, LogRocket, etc.)
  }
  
  private showErrorMessage(): void {
    // Show toast notification
  }
}
```

#### **4. Data Sanitization (Veri Temizleme)**
```typescript
// XSS koruması için data sanitization
import { DomSanitizer } from '@angular/platform-browser';

export class StandingsTableComponent {
  constructor(private sanitizer: DomSanitizer) {}
  
  sanitizeTeamName(name: string): SafeHtml {
    return this.sanitizer.sanitize(SecurityContext.HTML, name);
  }
}
```

### 🔒 Güvenlik Best Practices

#### **1. Content Security Policy (CSP)**
```html
<!-- index.html -->
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; 
               script-src 'self' 'unsafe-inline'; 
               style-src 'self' 'unsafe-inline';">
```

#### **2. HTTPS Enforcement**
```typescript
// Service worker ile HTTPS enforcement
if (location.protocol !== 'https:' && location.hostname !== 'localhost') {
  location.replace('https:' + window.location.href.substring(window.location.protocol.length));
}
```

#### **3. Environment Variables**
```typescript
// environment.ts
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000/api',
  enableDevTools: true,
  logLevel: 'debug'
};

// environment.prod.ts
export const environment = {
  production: true,
  apiUrl: 'https://api.futbol-ligi.com',
  enableDevTools: false,
  logLevel: 'error'
};
```

---

## 📈 Monitoring ve Logging

### 📊 Monitoring Stratejisi

#### **1. Performance Monitoring**
```typescript
// Performance monitoring service
@Injectable()
export class PerformanceMonitoringService {
  
  measureComponentLoad(componentName: string): void {
    const startTime = performance.now();
    
    // Component load logic
    
    const endTime = performance.now();
    const loadTime = endTime - startTime;
    
    this.logPerformance(componentName, 'load', loadTime);
  }
  
  measureApiCall(apiName: string, duration: number): void {
    this.logPerformance(apiName, 'api', duration);
  }
  
  private logPerformance(name: string, type: string, duration: number): void {
    if (duration > 1000) { // Log slow operations
      console.warn(`Slow ${type}: ${name} took ${duration}ms`);
    }
  }
}
```

#### **2. Error Tracking**
```typescript
// Error tracking service
@Injectable()
export class ErrorTrackingService {
  
  trackError(error: Error, context?: any): void {
    const errorInfo = {
      message: error.message,
      stack: error.stack,
      context: context,
      timestamp: new Date().toISOString(),
      userAgent: navigator.userAgent,
      url: window.location.href
    };
    
    // Send to error tracking service
    this.sendToErrorService(errorInfo);
  }
  
  private sendToErrorService(errorInfo: any): void {
    // Send to Sentry, LogRocket, etc.
    console.error('Error tracked:', errorInfo);
  }
}
```

#### **3. User Analytics**
```typescript
// Analytics service
@Injectable()
export class AnalyticsService {
  
  trackEvent(eventName: string, properties?: any): void {
    const event = {
      name: eventName,
      properties: properties,
      timestamp: new Date().toISOString(),
      sessionId: this.getSessionId()
    };
    
    this.sendToAnalytics(event);
  }
  
  trackPageView(pageName: string): void {
    this.trackEvent('page_view', { page: pageName });
  }
  
  trackUserAction(action: string, target: string): void {
    this.trackEvent('user_action', { action, target });
  }
  
  private sendToAnalytics(event: any): void {
    // Send to Google Analytics, Mixpanel, etc.
    console.log('Analytics event:', event);
  }
}
```

### 📝 Logging Stratejisi

#### **1. Structured Logging**
```typescript
// Logger service
@Injectable()
export class LoggerService {
  
  private logLevel: LogLevel = LogLevel.INFO;
  
  debug(message: string, data?: any): void {
    if (this.logLevel <= LogLevel.DEBUG) {
      this.log('DEBUG', message, data);
    }
  }
  
  info(message: string, data?: any): void {
    if (this.logLevel <= LogLevel.INFO) {
      this.log('INFO', message, data);
    }
  }
  
  warn(message: string, data?: any): void {
    if (this.logLevel <= LogLevel.WARN) {
      this.log('WARN', message, data);
    }
  }
  
  error(message: string, data?: any): void {
    if (this.logLevel <= LogLevel.ERROR) {
      this.log('ERROR', message, data);
    }
  }
  
  private log(level: string, message: string, data?: any): void {
    const logEntry = {
      level,
      message,
      data,
      timestamp: new Date().toISOString(),
      component: this.getComponentName()
    };
    
    console.log(JSON.stringify(logEntry));
  }
}

enum LogLevel {
  DEBUG = 0,
  INFO = 1,
  WARN = 2,
  ERROR = 3
}
```

#### **2. Component Logging**
```typescript
// Component'te logging kullanımı
export class LeagueDashboardComponent implements OnInit {
  constructor(private logger: LoggerService) {}
  
  ngOnInit(): void {
    this.logger.info('League Dashboard initialized');
  }
  
  playSpecificWeek(week: number): void {
    this.logger.info('Playing specific week', { week });
    
    try {
      this.store.dispatch(LeagueActions.playSpecificWeek({ week }));
    } catch (error) {
      this.logger.error('Error playing week', { week, error });
    }
  }
}
```

---

## 🚀 Deployment Mimarisi

### 🏗️ Build ve Deployment Stratejisi

#### **1. Multi-Environment Build**
```json
// angular.json
{
  "configurations": {
    "development": {
      "buildOptimizer": false,
      "optimization": false,
      "vendorChunk": true,
      "extractLicenses": false,
      "sourceMap": true,
      "namedChunks": true
    },
    "staging": {
      "buildOptimizer": true,
      "optimization": true,
      "vendorChunk": false,
      "extractLicenses": true,
      "sourceMap": false,
      "namedChunks": false,
      "fileReplacements": [
        {
          "replace": "src/environments/environment.ts",
          "with": "src/environments/environment.staging.ts"
        }
      ]
    },
    "production": {
      "buildOptimizer": true,
      "optimization": true,
      "vendorChunk": false,
      "extractLicenses": true,
      "sourceMap": false,
      "namedChunks": false,
      "fileReplacements": [
        {
          "replace": "src/environments/environment.ts",
          "with": "src/environments/environment.prod.ts"
        }
      ]
    }
  }
}
```

#### **2. Docker Containerization**
```dockerfile
# Multi-stage build
FROM node:20-alpine AS build

WORKDIR /app

# Copy package files
COPY package*.json ./
RUN npm ci --only=production

# Copy source code
COPY . .

# Build application
RUN npm run build --configuration production

# Production stage
FROM nginx:alpine

# Copy built app
COPY --from=build /app/dist/futbol-ligi-simulasyonu /usr/share/nginx/html

# Copy nginx config
COPY nginx.conf /etc/nginx/nginx.conf

# Expose port
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
```

#### **3. CI/CD Pipeline**
```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run test
      - run: npm run lint
      - run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run build --configuration production
      - name: Deploy to Netlify
        uses: nwtgck/actions-netlify@v1.2
        with:
          publish-dir: './dist/futbol-ligi-simulasyonu'
          production-branch: main
          github-token: ${{ secrets.GITHUB_TOKEN }}
          deploy-message: "Deploy from GitHub Actions"
```

### 🌐 Hosting Stratejisi

#### **1. Static Hosting (Netlify/Vercel)**
```bash
# Netlify deployment
netlify deploy --prod --dir=dist/futbol-ligi-simulasyonu

# Vercel deployment
vercel --prod
```

#### **2. CDN Configuration**
```typescript
// CDN configuration
export const cdnConfig = {
  baseUrl: 'https://cdn.futbol-ligi.com',
  assets: {
    images: '/images',
    fonts: '/fonts',
    scripts: '/scripts'
  },
  cache: {
    images: '1y',
    fonts: '1y',
    scripts: '1d'
  }
};
```

#### **3. Performance Optimization**
```typescript
// Service worker for caching
// ngsw-config.json
{
  "index": "/index.html",
  "assetGroups": [
    {
      "name": "app",
      "installMode": "prefetch",
      "resources": {
        "files": [
          "/favicon.ico",
          "/index.html",
          "/*.css",
          "/*.js"
        ]
      }
    },
    {
      "name": "assets",
      "installMode": "lazy",
      "updateMode": "prefetch",
      "resources": {
        "files": [
          "/assets/**"
        ]
      }
    }
  ]
}
```

### 📊 Monitoring ve Analytics

#### **1. Performance Monitoring**
```typescript
// Web Vitals monitoring
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';

function sendToAnalytics(metric: any) {
  // Send to Google Analytics, etc.
  console.log(metric);
}

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getFCP(sendToAnalytics);
getLCP(sendToAnalytics);
getTTFB(sendToAnalytics);
```

#### **2. Error Monitoring**
```typescript
// Error boundary for React-like error handling
@Injectable()
export class ErrorBoundaryService {
  
  handleError(error: any): void {
    // Log error
    console.error('Application error:', error);
    
    // Send to monitoring service
    this.sendToMonitoring(error);
    
    // Show user-friendly message
    this.showErrorMessage();
  }
  
  private sendToMonitoring(error: any): void {
    // Send to Sentry, LogRocket, etc.
  }
  
  private showErrorMessage(): void {
    // Show toast notification
  }
}
```

---

## 🎉 Sonuç

Bu kapsamlı mimari dokümantasyonu, projenin teknik yapısını ve tasarım kararlarını detaylı olarak açıklar. Angular 20'nin modern özelliklerini kullanarak, ölçeklenebilir, sürdürülebilir ve performanslı bir uygulama geliştirilmiştir.

### 🌟 Mimari Avantajları

1. **Modüler Yapı** - Her component bağımsız ve test edilebilir
2. **Scalable Architecture** - Yeni özellikler kolayca eklenebilir
3. **Performance Optimized** - OnPush strategy ve lazy loading
4. **Maintainable Code** - Clean architecture prensipleri
5. **Modern Angular** - En son Angular 20 özellikleri
6. **Security First** - Güvenlik katmanları ve best practices
7. **Monitoring Ready** - Kapsamlı logging ve monitoring
8. **Deployment Ready** - CI/CD ve containerization

### 🚀 Gelecek Geliştirmeler

- [ ] **Micro-frontend** architecture
- [ ] **Server-side rendering** (SSR)
- [ ] **Progressive Web App** (PWA)
- [ ] **Real-time updates** (WebSocket)
- [ ] **Advanced caching** strategies
- [ ] **Machine Learning** integration
- [ ] **Multi-language** support
- [ ] **Advanced analytics** dashboard

### 📚 Öğrenme Kaynakları

- [Angular Architecture Guide](https://angular.io/guide/architecture)
- [NgRx Documentation](https://ngrx.io/docs)
- [Clean Architecture Principles](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Angular Performance Guide](https://angular.io/guide/performance-checklist)

---

**🎯 Bu mimari, modern web geliştirme standartlarını karşılayan, ölçeklenebilir ve sürdürülebilir bir yapı sunar.**
