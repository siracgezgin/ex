# 🏛️ Mimari Yapı ve Teknoloji Analizi

Bu dokümantasyon, Angular Futbol Ligi Simülasyonu projesinin mimari yapısını ve kullanılan teknolojilerin neden seçildiğini açıklar.

## 📊 Genel Mimari Görünümü

```
┌─────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                       │
├─────────────────────────────────────────────────────────────┤
│  Angular Components (Smart & Dumb)                         │
│  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐│
│  │ League Dashboard│ │ Standings Table │ │ Match Results   ││
│  │ (Smart)         │ │ (Dumb)          │ │ (Dumb)          ││
│  └─────────────────┘ └─────────────────┘ └─────────────────┘│
└─────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│                    STATE MANAGEMENT                         │
├─────────────────────────────────────────────────────────────┤
│  NgRx Store                                                 │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────┐│
│  │ Actions     │ │ Reducers    │ │ Effects     │ │Selectors││
│  │             │ │             │ │             │ │         ││
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────┘│
└─────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│                    BUSINESS LOGIC                           │
├─────────────────────────────────────────────────────────────┤
│  Angular Services                                           │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ LeagueSimulationService                                 ││
│  │ • Takım Başlatma                                        ││
│  │ • Fikstür Oluşturma (Round Robin)                      ││
│  │ • Maç Simülasyonu                                       ││
│  │ • Sıralama Hesaplama                                    ││
│  │ • Monte Carlo Simülasyonu                               ││
│  └─────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│                    DATA MODELS                              │
├─────────────────────────────────────────────────────────────┤
│  TypeScript Interfaces & Enums                             │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────┐│
│  │ Team        │ │ Match       │ │ LeagueState │ │Constants││
│  │ TeamStats   │ │ MatchResult │ │ WeekMatches │ │         ││
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────┘│
└─────────────────────────────────────────────────────────────┘
```

## 🧩 Katman Analizi

### 1. Presentation Layer (UI Katmanı)

#### Smart vs Dumb Components Pattern

**Smart Components (Container Components):**
- **LeagueDashboardComponent**: State yönetimi, user interactions
- Store ile direkt iletişim
- Business logic koordinasyonu
- Observable subscriptions

**Dumb Components (Presentational Components):**
- **StandingsTableComponent**: Sadece veri gösterimi
- **MatchResultsComponent**: Pure UI, props ile veri alır
- Input/Output pattern ile parent ile iletişim
- Reusable ve testable

```typescript
// Smart Component
@Component({
  selector: 'app-league-dashboard',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class LeagueDashboardComponent {
  standings$ = this.store.select(selectStandings);
  
  constructor(private store: Store) {}
  
  playNextWeek() {
    this.store.dispatch(playNextWeek());
  }
}

// Dumb Component
@Component({
  selector: 'app-standings-table',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class StandingsTableComponent {
  @Input() standings: TeamStats[] = [];
  @Output() teamSelected = new EventEmitter<Team>();
  
  onTeamClick(team: Team) {
    this.teamSelected.emit(team);
  }
}
```

**Neden Bu Pattern?**
- **Separation of Concerns**: UI ve business logic ayrımı
- **Reusability**: Dumb componentler yeniden kullanılabilir
- **Testability**: Dumb componentler kolayca test edilir
- **Performance**: OnPush change detection optimizasyonu

### 2. State Management Layer (NgRx Store)

#### Redux Pattern Implementation

**Actions (What happened?):**
```typescript
export const initializeLeague = createAction('[League] Initialize League');
export const playNextWeek = createAction('[League] Play Next Week');
export const playNextWeekSuccess = createAction(
  '[League] Play Next Week Success',
  props<{ matches: Match[]; currentWeek: number }>()
);
```

**Reducers (How state changes?):**
```typescript
export const leagueReducer = createReducer(
  initialState,
  on(LeagueActions.playNextWeekSuccess, (state, { matches, currentWeek }) => ({
    ...state,
    matches: updatedMatches,
    standings: calculateStandings(state.teams, updatedMatches),
    currentWeek,
    loading: false
  }))
);
```

**Effects (Side Effects):**
```typescript
playNextWeek$ = createEffect(() =>
  this.actions$.pipe(
    ofType(LeagueActions.playNextWeek),
    withLatestFrom(this.store.select(selectCurrentWeek)),
    mergeMap(([action, currentWeek]) => {
      return this.leagueService.playWeekMatches(matches, currentWeek).pipe(
        map(updatedMatches => 
          LeagueActions.playNextWeekSuccess({ matches: updatedMatches, currentWeek: currentWeek + 1 })
        )
      );
    })
  )
);
```

**Selectors (How to query state?):**
```typescript
export const selectLeagueState = createFeatureSelector<LeagueState>('league');
export const selectStandings = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.standings
);
export const selectCurrentWeek = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.currentWeek
);
```

**Neden NgRx?**
- **Predictable State**: Tek yönlü veri akışı
- **Time Travel Debugging**: NgRx DevTools ile debug
- **Immutable Updates**: State değişmez, yeni state oluşturur
- **Side Effect Management**: Effects ile async işlemler
- **Scalability**: Büyük uygulamalarda state yönetimi

### 3. Business Logic Layer (Services)

#### Domain-Driven Design Approach

**LeagueSimulationService:**
```typescript
@Injectable({
  providedIn: 'root'
})
export class LeagueSimulationService {
  // Domain logic - Takım başlatma
  initializeTeams(): Observable<Team[]> { }
  
  // Domain logic - Fikstür oluşturma
  generateFixture(teams: Team[]): Observable<Match[]> { }
  
  // Domain logic - Maç simülasyonu
  playWeekMatches(matches: Match[], week: number): Observable<Match[]> { }
  
  // Domain logic - Şampiyonluk hesaplama
  calculateChampionshipChances(teams: Team[], remainingMatches: Match[]): Observable<{ [teamId: number]: number }> { }
}
```

**Core Algorithms:**

1. **Round Robin Tournament Algorithm:**
   - N takım için (N-1) hafta
   - Her hafta N/2 maç
   - Takım rotasyonu ile adil eşleşme

2. **Match Simulation Engine:**
   - Takım gücü bazlı probabilistic scoring
   - Ev sahibi avantajı (%30 bonus)
   - Weighted random distribution

3. **Monte Carlo Championship Simulation:**
   - 1000 simülasyon iteration
   - Kalan maçları rastgele simüle etme
   - İstatistiksel şampiyonluk ihtimali hesaplama

**Neden Service Layer?**
- **Single Responsibility**: Her servis tek sorumluluğa sahip
- **Dependency Injection**: Angular DI ile loose coupling
- **Testability**: Mock'lanabilir servisler
- **Reusability**: Birden fazla component kullanabilir

### 4. Data Models Layer (Type System)

#### TypeScript Type Safety

**Core Entities:**
```typescript
// Team entity
interface Team {
  id: number;           // Unique identifier
  name: string;         // Team name
  played: number;       // Games played
  won: number;         // Games won
  drawn: number;       // Games drawn
  lost: number;        // Games lost
  goalsFor: number;    // Goals scored
  goalsAgainst: number; // Goals conceded
  goalDifference: number; // Goal difference
  points: number;       // Total points
  strength: number;     // Team strength (1-5)
}

// Match entity
interface Match {
  id: number;
  week: number;
  homeTeam: { id: number; name: string; };
  awayTeam: { id: number; name: string; };
  homeScore: number | null;
  awayScore: number | null;
  isPlayed: boolean;
  result?: MatchResult;
}

// Enum for type safety
enum MatchResult {
  HOME_WIN = 'HOME_WIN',
  AWAY_WIN = 'AWAY_WIN',
  DRAW = 'DRAW'
}
```

**Value Objects:**
```typescript
// League constants
const LEAGUE_CONSTANTS = {
  POINTS: { WIN: 3, DRAW: 1, LOSS: 0 },
  TEAM_COUNT: 4,
  TOTAL_WEEKS: 6,
  TEAM_NAMES: ['Galatasaray', 'Fenerbahçe', 'Beşiktaş', 'Trabzonspor']
};

// Computed types
interface TeamStats {
  team: Team;
  position: number;
  championshipChance?: number;
  isChampionshipPossible?: boolean;
}
```

**Neden Strong Typing?**
- **Compile-time Safety**: Runtime hatalarını önler
- **IntelliSense**: IDE desteği ve auto-completion
- **Refactoring Safety**: Tip değişiklikleri otomatik güncellenir
- **Self-documenting**: Kod kendini belgeler

## 🔄 Data Flow Pattern

### Unidirectional Data Flow

```
User Action → Component → Store (Action) → Effect → Service → 
Backend/Logic → Effect → Store (Reducer) → Component → UI Update
```

**Örnek Flow: Maç Oynatma**

1. **User Interaction:**
   ```typescript
   // User clicks "Play Next Week" button
   playNextWeek() {
     this.store.dispatch(playNextWeek());
   }
   ```

2. **Action Dispatch:**
   ```typescript
   // Action dispatched to store
   export const playNextWeek = createAction('[League] Play Next Week');
   ```

3. **Effect Processing:**
   ```typescript
   // Effect catches action and calls service
   playNextWeek$ = createEffect(() =>
     this.actions$.pipe(
       ofType(LeagueActions.playNextWeek),
       mergeMap(() => this.leagueService.playWeekMatches(...))
     )
   );
   ```

4. **Service Logic:**
   ```typescript
   // Service executes business logic
   playWeekMatches(matches: Match[], week: number): Observable<Match[]> {
     // Simulate matches for the week
     return of(updatedMatches).pipe(delay(800));
   }
   ```

5. **Success Action:**
   ```typescript
   // Service result triggers success action
   map(matches => LeagueActions.playNextWeekSuccess({ matches, currentWeek }))
   ```

6. **Reducer Update:**
   ```typescript
   // Reducer updates state immutably
   on(LeagueActions.playNextWeekSuccess, (state, { matches, currentWeek }) => ({
     ...state,
     matches: updatedMatches,
     currentWeek,
     standings: calculateStandings(...)
   }))
   ```

7. **UI Update:**
   ```typescript
   // Component receives new state via selectors
   standings$ = this.store.select(selectStandings);
   // Template automatically updates via async pipe
   ```

## 🎨 UI Architecture

### Component Hierarchy

```
AppComponent
└── LeagueDashboardComponent (Smart)
    ├── StandingsTableComponent (Dumb)
    │   └── TeamRowComponent (Dumb)
    ├── MatchResultsComponent (Dumb)
    │   ├── WeekTabsComponent (Dumb)
    │   └── MatchCardComponent (Dumb)
    └── ChampionAnnouncementComponent (Dumb)
```

### PrimeNG Integration

**Why PrimeNG?**
- **Enterprise-ready**: Professional UI components
- **Accessibility**: WCAG 2.1 compliance
- **Theming**: Customizable design system
- **Performance**: Optimized for Angular
- **Rich Components**: DataTable, Charts, Dialogs

```typescript
// PrimeNG modules
imports: [
  CardModule,        // Card containers
  ButtonModule,      // Action buttons
  ProgressBarModule, // Loading indicators
  MessageModule,     // Error messages
  DialogModule,      // Modal dialogs
  TableModule        // Data tables
]
```

### Tailwind CSS Integration

**Why Tailwind CSS?**
- **Utility-first**: Rapid prototyping
- **Design System**: Consistent spacing, colors
- **Responsive**: Mobile-first approach
- **Tree-shaking**: Only used utilities in build
- **Customization**: Extended color palette

```css
/* Custom team colors */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .team-galatasaray {
    @apply bg-orange-500 text-white;
  }
  
  .team-fenerbahce {
    @apply bg-yellow-400 text-blue-900;
  }
  
  .team-besiktas {
    @apply bg-gray-900 text-white;
  }
  
  .team-trabzonspor {
    @apply bg-red-800 text-white;
  }
}
```

## ⚡ Performance Optimizations

### 1. Change Detection Strategy

```typescript
@Component({
  selector: 'app-standings-table',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class StandingsTableComponent {
  // Only updates when inputs change or events trigger
}
```

### 2. TrackBy Functions

```typescript
trackByTeamId(index: number, team: Team): number {
  return team.id; // Angular uses this for efficient DOM updates
}

trackByMatchId(index: number, match: Match): number {
  return match.id;
}
```

### 3. Async Pipe Usage

```html
<!-- Automatic subscription management -->
<div *ngFor="let team of standings$ | async; trackBy: trackByTeamId">
  {{ team.name }}
</div>
```

### 4. Lazy Loading

```typescript
// Future enhancement - lazy load match history
const routes: Routes = [
  {
    path: 'history',
    loadChildren: () => import('./history/history.module').then(m => m.HistoryModule)
  }
];
```

## 🧪 Testing Strategy

### Unit Testing Architecture

```typescript
// Service testing
describe('LeagueSimulationService', () => {
  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(LeagueSimulationService);
  });

  it('should generate correct number of matches', () => {
    const teams = mockTeams(4);
    const matches = service.generateFixture(teams);
    expect(matches.length).toBe(12); // 4 teams, double round-robin
  });
});

// Component testing
describe('LeagueDashboardComponent', () => {
  let store: MockStore;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [provideMockStore({ initialState })]
    });
    store = TestBed.inject(MockStore);
  });

  it('should dispatch playNextWeek action', () => {
    spyOn(store, 'dispatch');
    component.playNextWeek();
    expect(store.dispatch).toHaveBeenCalledWith(playNextWeek());
  });
});
```

### Integration Testing

```typescript
// E2E testing with Cypress
describe('League Simulation', () => {
  it('should complete a full season', () => {
    cy.visit('/');
    cy.contains('1. Hafta Ligi Başlat').click();
    cy.get('[data-cy=play-all-season]').click();
    cy.contains('ŞAMPİYON').should('be.visible');
  });
});
```

## 🔐 Type Safety & Error Handling

### Compile-time Safety

```typescript
// Union types for type safety
type LeagueStatus = 'not-started' | 'active' | 'finished';
type MatchStatus = 'scheduled' | 'playing' | 'finished';

// Mapped types for transformations
type TeamUpdate = Partial<Pick<Team, 'goalsFor' | 'goalsAgainst' | 'points'>>;

// Generic constraints
interface Repository<T extends { id: number }> {
  findById(id: number): T | undefined;
  update(entity: T): void;
}
```

### Runtime Error Handling

```typescript
// Service level error handling
private handleError<T>(operation = 'operation', result?: T) {
  return (error: any): Observable<T> => {
    console.error(`${operation} failed: ${error.message}`);
    // Could send to logging service
    return of(result as T);
  };
}

// Effect level error handling
initializeLeague$ = createEffect(() =>
  this.actions$.pipe(
    ofType(LeagueActions.initializeLeague),
    mergeMap(() =>
      this.service.initializeTeams().pipe(
        map(teams => LeagueActions.initializeLeagueSuccess({ teams })),
        catchError(error => of(LeagueActions.setError({ error: error.message })))
      )
    )
  )
);
```

## 🚀 Scalability Considerations

### Future Enhancements

1. **Multiple Leagues**: Support for different leagues
2. **Player Management**: Individual player statistics
3. **Real-time Updates**: WebSocket integration
4. **Historical Data**: Season archives
5. **Advanced Analytics**: Performance metrics
6. **User Management**: Multiple users, favorites
7. **Mobile App**: Ionic/Capacitor integration

### Architecture Benefits

- **Modular**: Easy to add new features
- **Testable**: Comprehensive testing strategy
- **Maintainable**: Clean code principles
- **Scalable**: Handles increasing complexity
- **Type-safe**: Prevents runtime errors
- **Performance**: Optimized for speed

## 📋 Yazılım Geliştirme Standartları

### **Proje Yapısı Standartları:**
- **Common**: Genel servisler, interceptors, guards
- **Core**: Component-specific interface, service, store tanımları
- **Modules**: Sayfa componentleri
- **Shared**: Ortak kullanılan componentler, pipe'lar
- **Assets**: CSS tanımları ve görseller
- **Environments**: API base, port, prefix tanımları

### **Component Yapısı Standartları:**
```typescript
// Component .ts içerisindeki blok hiyerarşisi:
// 1. Değişkenler
// 2. Constructor
// 3. Lifecycle Metodlar
// 4. Custom Metodlar
// 5. Submit metodu (varsa)
// 6. OnDestroy
```

### **İsimlendirme Standartları:**
- **Methodlar**: camelCase (getUserInfo, calculateTotalPrice)
- **Sınıflar**: PascalCase (UserService, AppComponent)
- **Değişkenler**: camelCase (userName, totalAmount)
- **Interface'ler**: PascalCase (IUser, Product)
- **Dosya İsimleri**: kebab-case (user-profile.component.ts)
- **Component Selectors**: kebab-case (app-user-profile)
- **Boolean Değerler**: is/has/can ile başla (isLoggedIn, hasAccess)

### **Kod Kalitesi Standartları:**
- Console.log ve dummy data temizlenmelidir
- Anlamsız yorum satırları kaldırılmalıdır
- ESLint kurallarına uyulmalıdır
- Prettier ile formatlanmalıdır
- Unit testler yazılmalıdır

Bu mimari yaklaşım sayesinde proje gelecekte kolayca genişletilebilir ve yeni özellikler eklenebilir. Her katman kendi sorumluluğuna sahip olduğu için değişiklikler izole edilebilir ve test edilebilir.

**Modern Angular uygulaması için ideal bir architecture! 🏗️**
