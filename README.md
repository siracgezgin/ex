# 🔌 API Dokümantasyonu

> **Futbol Ligi Simülasyonu - Servisler, Modeller ve API Referansı**

Bu dokümantasyon, projedeki tüm servisler, veri modelleri ve API'ları detaylı olarak açıklar.

## 📋 İçindekiler

- [📊 Veri Modelleri](#-veri-modelleri)
- [🔧 Servisler](#-servisler)
- [🎯 NgRx Actions](#-ngrx-actions)
- [📈 NgRx Selectors](#-ngrx-selectors)
- [⚡ NgRx Effects](#-ngrx-effects)
- [🔄 NgRx Reducers](#-ngrx-reducers)
- [🎨 Component API'ları](#-component-apilari)
- [📝 Type Definitions](#-type-definitions)
- [🔍 Utility Functions](#-utility-functions)

---

## 📊 Veri Modelleri

### 🏆 Team Model

```typescript
export interface Team {
  id: number;           // Takım ID'si (benzersiz)
  name: string;         // Takım adı
  strength: number;     // Takım gücü (0.3-1.0 arası)
  color: string;        // Takım ana rengi (hex)
  logo: string;         // Logo dosya yolu
}

export interface TeamStats {
  team: Team;           // Takım bilgisi
  position: number;     // Lig sıralaması (1-4)
  played: number;       // Oynanan maç sayısı
  won: number;          // Galibiyet sayısı
  drawn: number;        // Beraberlik sayısı
  lost: number;         // Mağlubiyet sayısı
  goalsFor: number;     // Attığı gol sayısı
  goalsAgainst: number; // Yediği gol sayısı
  goalDifference: number; // Averaj (goalsFor - goalsAgainst)
  points: number;       // Toplam puan
}
```

**Örnek Kullanım:**
```typescript
const team: Team = {
  id: 1,
  name: 'Galatasaray',
  strength: 0.8,
  color: '#FFD700',
  logo: 'assets/logos/galatasaray.png'
};

const teamStats: TeamStats = {
  team: team,
  position: 1,
  played: 6,
  won: 4,
  drawn: 1,
  lost: 1,
  goalsFor: 12,
  goalsAgainst: 6,
  goalDifference: 6,
  points: 13
};
```

### ⚽ Match Model

```typescript
export interface Match {
  id: number;                    // Maç ID'si (benzersiz)
  homeTeam: Team;               // Ev sahibi takım
  awayTeam: Team;               // Deplasman takımı
  homeScore: number | null;     // Ev sahibi skoru
  awayScore: number | null;     // Deplasman skoru
  isPlayed: boolean;            // Maç oynandı mı?
  week: number;                 // Hafta numarası (1-6)
  date: Date;                   // Maç tarihi
}

export interface WeekMatches {
  week: number;         // Hafta numarası
  matches: Match[];     // O haftaki maçlar
}

export interface MatchResult {
  homeScore: number;    // Ev sahibi skoru
  awayScore: number;    // Deplasman skoru
  isPlayed: boolean;    // Maç oynandı mı?
}
```

**Örnek Kullanım:**
```typescript
const match: Match = {
  id: 1,
  homeTeam: galatasaray,
  awayTeam: fenerbahce,
  homeScore: 2,
  awayScore: 1,
  isPlayed: true,
  week: 1,
  date: new Date('2024-01-15')
};

const weekMatches: WeekMatches = {
  week: 1,
  matches: [match1, match2]
};
```

### 🏅 League State Model

```typescript
export interface LeagueState {
  teams: Team[];              // Takım listesi
  matches: Match[];           // Tüm maçlar
  currentWeek: number;        // Mevcut hafta (1-6)
  totalWeeks: number;         // Toplam hafta sayısı (6)
  standings: TeamStats[];     // Puan tablosu
  weeklyMatches: WeekMatches[]; // Haftalık maçlar
  isSeasonFinished: boolean;  // Sezon bitti mi?
  champion: Team | null;      // Şampiyon takım
  loading: boolean;           // Yükleniyor mu?
  error: string | null;       // Hata mesajı
}

export const initialLeagueState: LeagueState = {
  teams: [],
  matches: [],
  currentWeek: 1,
  totalWeeks: 6,
  standings: [],
  weeklyMatches: [],
  isSeasonFinished: false,
  champion: null,
  loading: false,
  error: null
};
```

### 🎯 League Constants

```typescript
export const LEAGUE_CONSTANTS = {
  TOTAL_TEAMS: 4,       // Toplam takım sayısı
  TOTAL_WEEKS: 6,       // Toplam hafta sayısı
  MATCHES_PER_WEEK: 2,  // Haftalık maç sayısı
  POINTS_WIN: 3,        // Galibiyet puanı
  POINTS_DRAW: 1,       // Beraberlik puanı
  POINTS_LOSS: 0,       // Mağlubiyet puanı
  MIN_STRENGTH: 0.3,    // Minimum takım gücü
  MAX_STRENGTH: 1.0     // Maksimum takım gücü
};
```

---

## 🔧 Servisler

### 🎮 LeagueSimulationService

**Amaç:** Lig simülasyonu ve maç hesaplamaları

```typescript
@Injectable({
  providedIn: 'root'
})
export class LeagueSimulationService {
  
  /**
   * Tek bir maçı simüle eder
   * @param homeTeam - Ev sahibi takım
   * @param awayTeam - Deplasman takımı
   * @returns MatchResult - Maç sonucu
   */
  simulateMatch(homeTeam: Team, awayTeam: Team): MatchResult {
    const homeStrength = homeTeam.strength;
    const awayStrength = awayTeam.strength;
    
    // Ev sahibi avantajı (+0.1)
    const adjustedHomeStrength = homeStrength + 0.1;
    
    // Skor hesaplama
    const homeScore = this.calculateScore(adjustedHomeStrength, true);
    const awayScore = this.calculateScore(awayStrength, false);
    
    return {
      homeScore,
      awayScore,
      isPlayed: true
    };
  }
  
  /**
   * Belirli haftanın maçlarını simüle eder
   * @param weekMatches - Haftalık maçlar
   * @returns Observable<Match[]> - Simüle edilmiş maçlar
   */
  simulateWeekMatches(weekMatches: WeekMatches): Observable<Match[]> {
    return of(weekMatches.matches.map(match => ({
      ...match,
      ...this.simulateMatch(match.homeTeam, match.awayTeam)
    })));
  }
  
  /**
   * Puan tablosunu hesaplar
   * @param teams - Takım listesi
   * @param matches - Tüm maçlar
   * @returns TeamStats[] - Puan tablosu
   */
  calculateStandings(teams: Team[], matches: Match[]): TeamStats[] {
    return teams.map(team => this.calculateTeamStats(team, matches));
  }
  
  /**
   * Takım istatistiklerini hesaplar
   * @param team - Takım
   * @param matches - Tüm maçlar
   * @returns TeamStats - Takım istatistikleri
   */
  private calculateTeamStats(team: Team, matches: Match[]): TeamStats {
    const teamMatches = matches.filter(match => 
      match.homeTeam.id === team.id || match.awayTeam.id === team.id
    );
    
    let played = 0, won = 0, drawn = 0, lost = 0;
    let goalsFor = 0, goalsAgainst = 0;
    
    teamMatches.forEach(match => {
      if (match.isPlayed && match.homeScore !== null && match.awayScore !== null) {
        played++;
        
        const isHome = match.homeTeam.id === team.id;
        const teamScore = isHome ? match.homeScore : match.awayScore;
        const opponentScore = isHome ? match.awayScore : match.homeScore;
        
        goalsFor += teamScore;
        goalsAgainst += opponentScore;
        
        if (teamScore > opponentScore) won++;
        else if (teamScore === opponentScore) drawn++;
        else lost++;
      }
    });
    
    const points = won * LEAGUE_CONSTANTS.POINTS_WIN + 
                   drawn * LEAGUE_CONSTANTS.POINTS_DRAW + 
                   lost * LEAGUE_CONSTANTS.POINTS_LOSS;
    
    return {
      team,
      position: 0, // Sıralama ayrıca hesaplanacak
      played,
      won,
      drawn,
      lost,
      goalsFor,
      goalsAgainst,
      goalDifference: goalsFor - goalsAgainst,
      points
    };
  }
  
  /**
   * Skor hesaplama algoritması
   * @param strength - Takım gücü
   * @param isHome - Ev sahibi mi?
   * @returns number - Skor
   */
  private calculateScore(strength: number, isHome: boolean): number {
    const baseScore = Math.floor(strength * 3); // 0-3 arası skor
    const randomFactor = Math.random();
    
    if (randomFactor < 0.1) return 0; // %10 şans 0 gol
    if (randomFactor < 0.4) return Math.max(0, baseScore - 1); // %30 şans -1 gol
    if (randomFactor < 0.8) return baseScore; // %40 şans normal skor
    return Math.min(5, baseScore + 1); // %20 şans +1 gol (max 5)
  }
}
```

---

## 🎯 NgRx Actions

### 📝 Action Definitions

```typescript
// league.actions.ts
export const LeagueActions = createActionGroup({
  source: 'League',
  events: {
    // Lig başlatma
    'Initialize League': emptyProps(),
    'Initialize League Success': props<{ teams: Team[]; matches: Match[] }>(),
    'Initialize League Failure': props<{ error: string }>(),
    
    // Haftalık maçlar
    'Play Specific Week': props<{ week: number }>(),
    'Play Specific Week Success': props<{ matches: Match[] }>(),
    'Play Specific Week Failure': props<{ error: string }>(),
    
    // Tüm sezon
    'Play All Season': emptyProps(),
    'Play All Season Success': props<{ matches: Match[]; champion: Team }>(),
    'Play All Season Failure': props<{ error: string }>(),
    
    // Lig sıfırlama
    'Reset League': emptyProps(),
    'Reset League Success': emptyProps(),
    
    // Maç sonucu düzenleme
    'Edit Match Result': props<{ matchId: number; homeScore: number; awayScore: number }>(),
    'Edit Match Result Success': props<{ match: Match }>(),
    'Edit Match Result Failure': props<{ error: string }>(),
    
    // UI durumu
    'Set Loading': props<{ loading: boolean }>(),
    'Set Error': props<{ error: string | null }>()
  }
});
```

### 🎯 Action Usage Examples

```typescript
// Component'te kullanım
export class LeagueDashboardComponent {
  constructor(private store: Store<AppState>) {}
  
  // Lig başlatma
  initializeLeague() {
    this.store.dispatch(LeagueActions.initializeLeague());
  }
  
  // Haftalık maç oynatma
  playSpecificWeek(week: number) {
    this.store.dispatch(LeagueActions.playSpecificWeek({ week }));
  }
  
  // Tüm sezonu oynatma
  playAllSeason() {
    this.store.dispatch(LeagueActions.playAllSeason());
  }
  
  // Lig sıfırlama
  resetLeague() {
    this.store.dispatch(LeagueActions.resetLeague());
  }
  
  // Maç sonucu düzenleme
  editMatchResult(matchId: number, homeScore: number, awayScore: number) {
    this.store.dispatch(LeagueActions.editMatchResult({ 
      matchId, 
      homeScore, 
      awayScore 
    }));
  }
}
```

---

## 📈 NgRx Selectors

### 🎯 Selector Definitions

```typescript
// league.selectors.ts
export const selectLeagueState = createFeatureSelector<LeagueState>('league');

// Base selectors
export const selectTeams = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.teams
);

export const selectMatches = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.matches
);

export const selectCurrentWeek = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.currentWeek
);

export const selectIsSeasonFinished = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.isSeasonFinished
);

export const selectChampion = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.champion
);

export const selectLoading = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.loading
);

export const selectError = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.error
);

// Complex selectors
export const selectCurrentStandings = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.standings
);

export const selectWeeklyMatches = createSelector(
  selectLeagueState,
  (state: LeagueState) => state.weeklyMatches
);

export const selectCurrentWeekMatches = createSelector(
  selectWeeklyMatches,
  selectCurrentWeek,
  (weeklyMatches, currentWeek) => 
    weeklyMatches.find(wm => wm.week === currentWeek)
);

export const selectPlayedMatches = createSelector(
  selectMatches,
  (matches) => matches.filter(match => match.isPlayed).length
);

export const selectTotalMatches = createSelector(
  selectMatches,
  (matches) => matches.length
);

export const selectLeagueProgress = createSelector(
  selectPlayedMatches,
  selectTotalMatches,
  (played, total) => total > 0 ? (played / total) * 100 : 0
);
```

### 🎯 Selector Usage Examples

```typescript
// Component'te kullanım
export class LeagueDashboardComponent implements OnInit {
  // Observable selectors
  currentStandings$ = this.store.select(selectCurrentStandings);
  currentWeekMatches$ = this.store.select(selectCurrentWeekMatches);
  isSeasonFinished$ = this.store.select(selectIsSeasonFinished);
  champion$ = this.store.select(selectChampion);
  loading$ = this.store.select(selectLoading);
  error$ = this.store.select(selectError);
  
  // Computed values
  playedMatches$ = this.store.select(selectPlayedMatches);
  totalMatches$ = this.store.select(selectTotalMatches);
  leagueProgress$ = this.store.select(selectLeagueProgress);
  
  constructor(private store: Store<AppState>) {}
  
  ngOnInit() {
    // Selector'ları subscribe et
    this.currentStandings$.subscribe(standings => {
      console.log('Current standings:', standings);
    });
  }
}
```

---

## ⚡ NgRx Effects

### 🔄 Effect Definitions

```typescript
// league.effects.ts
@Injectable()
export class LeagueEffects {
  
  constructor(
    private actions$: Actions,
    private leagueService: LeagueSimulationService
  ) {}
  
  // Lig başlatma effect
  initializeLeague$ = createEffect(() =>
    this.actions$.pipe(
      ofType(LeagueActions.initializeLeague),
      switchMap(() =>
        this.leagueService.initializeLeague().pipe(
          map(({ teams, matches }) => 
            LeagueActions.initializeLeagueSuccess({ teams, matches })
          ),
          catchError(error => 
            of(LeagueActions.initializeLeagueFailure({ 
              error: error.message 
            }))
          )
        )
      )
    )
  );
  
  // Haftalık maç oynatma effect
  playSpecificWeek$ = createEffect(() =>
    this.actions$.pipe(
      ofType(LeagueActions.playSpecificWeek),
      switchMap(({ week }) =>
        this.leagueService.playSpecificWeek(week).pipe(
          map(matches => 
            LeagueActions.playSpecificWeekSuccess({ matches })
          ),
          catchError(error => 
            of(LeagueActions.playSpecificWeekFailure({ 
              error: error.message 
            }))
          )
        )
      )
    )
  );
  
  // Tüm sezon oynatma effect
  playAllSeason$ = createEffect(() =>
    this.actions$.pipe(
      ofType(LeagueActions.playAllSeason),
      switchMap(() =>
        this.leagueService.playAllSeason().pipe(
          map(({ matches, champion }) => 
            LeagueActions.playAllSeasonSuccess({ matches, champion })
          ),
          catchError(error => 
            of(LeagueActions.playAllSeasonFailure({ 
              error: error.message 
            }))
          )
        )
      )
    )
  );
  
  // Lig sıfırlama effect
  resetLeague$ = createEffect(() =>
    this.actions$.pipe(
      ofType(LeagueActions.resetLeague),
      switchMap(() =>
        this.leagueService.resetLeague().pipe(
          map(() => LeagueActions.resetLeagueSuccess()),
          catchError(error => 
            of(LeagueActions.resetLeagueFailure({ 
              error: error.message 
            }))
          )
        )
      )
    )
  );
  
  // Maç sonucu düzenleme effect
  editMatchResult$ = createEffect(() =>
    this.actions$.pipe(
      ofType(LeagueActions.editMatchResult),
      switchMap(({ matchId, homeScore, awayScore }) =>
        this.leagueService.editMatchResult(matchId, homeScore, awayScore).pipe(
          map(match => 
            LeagueActions.editMatchResultSuccess({ match })
          ),
          catchError(error => 
            of(LeagueActions.editMatchResultFailure({ 
              error: error.message 
            }))
          )
        )
      )
    )
  );
}
```

---

## 🔄 NgRx Reducers

### 📊 Reducer Implementation

```typescript
// league.reducer.ts
export const leagueReducer = createReducer(
  initialLeagueState,
  
  // Lig başlatma
  on(LeagueActions.initializeLeague, (state) => ({
    ...state,
    loading: true,
    error: null
  })),
  
  on(LeagueActions.initializeLeagueSuccess, (state, { teams, matches }) => ({
    ...state,
    teams,
    matches,
    standings: calculateStandings(teams, matches),
    weeklyMatches: groupMatchesByWeek(matches),
    loading: false,
    error: null,
    currentWeek: 1,
    isSeasonFinished: false,
    champion: null
  })),
  
  on(LeagueActions.initializeLeagueFailure, (state, { error }) => ({
    ...state,
    loading: false,
    error
  })),
  
  // Haftalık maç oynatma
  on(LeagueActions.playSpecificWeek, (state) => ({
    ...state,
    loading: true,
    error: null
  })),
  
  on(LeagueActions.playSpecificWeekSuccess, (state, { matches }) => {
    const updatedMatches = [...state.matches, ...matches];
    const updatedStandings = calculateStandings(state.teams, updatedMatches);
    const isFinished = updatedMatches.length >= state.totalWeeks * 2;
    const champion = isFinished ? updatedStandings[0]?.team : null;
    
    return {
      ...state,
      matches: updatedMatches,
      standings: updatedStandings,
      weeklyMatches: groupMatchesByWeek(updatedMatches),
      currentWeek: Math.min(state.currentWeek + 1, state.totalWeeks),
      isSeasonFinished: isFinished,
      champion,
      loading: false,
      error: null
    };
  }),
  
  on(LeagueActions.playSpecificWeekFailure, (state, { error }) => ({
    ...state,
    loading: false,
    error
  })),
  
  // Tüm sezon oynatma
  on(LeagueActions.playAllSeason, (state) => ({
    ...state,
    loading: true,
    error: null
  })),
  
  on(LeagueActions.playAllSeasonSuccess, (state, { matches, champion }) => ({
    ...state,
    matches,
    standings: calculateStandings(state.teams, matches),
    weeklyMatches: groupMatchesByWeek(matches),
    currentWeek: state.totalWeeks,
    isSeasonFinished: true,
    champion,
    loading: false,
    error: null
  })),
  
  on(LeagueActions.playAllSeasonFailure, (state, { error }) => ({
    ...state,
    loading: false,
    error
  })),
  
  // Lig sıfırlama
  on(LeagueActions.resetLeague, (state) => ({
    ...state,
    loading: true,
    error: null
  })),
  
  on(LeagueActions.resetLeagueSuccess, (state) => ({
    ...state,
    matches: [],
    standings: [],
    weeklyMatches: [],
    currentWeek: 1,
    isSeasonFinished: false,
    champion: null,
    loading: false,
    error: null
  })),
  
  // Maç sonucu düzenleme
  on(LeagueActions.editMatchResult, (state) => ({
    ...state,
    loading: true,
    error: null
  })),
  
  on(LeagueActions.editMatchResultSuccess, (state, { match }) => {
    const updatedMatches = state.matches.map(m => 
      m.id === match.id ? match : m
    );
    const updatedStandings = calculateStandings(state.teams, updatedMatches);
    
    return {
      ...state,
      matches: updatedMatches,
      standings: updatedStandings,
      weeklyMatches: groupMatchesByWeek(updatedMatches),
      loading: false,
      error: null
    };
  }),
  
  on(LeagueActions.editMatchResultFailure, (state, { error }) => ({
    ...state,
    loading: false,
    error
  })),
  
  // UI durumu
  on(LeagueActions.setLoading, (state, { loading }) => ({
    ...state,
    loading
  })),
  
  on(LeagueActions.setError, (state, { error }) => ({
    ...state,
    error
  }))
);
```

---

## 🎨 Component API'ları

### 🏆 LeagueDashboardComponent

```typescript
@Component({
  selector: 'app-league-dashboard',
  standalone: true,
  imports: [CommonModule, CardModule, ButtonModule, ProgressBarModule],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class LeagueDashboardComponent implements OnInit, OnDestroy {
  
  // Public properties
  currentStandings$: Observable<TeamStats[]>;
  currentWeekMatches$: Observable<WeekMatches | undefined>;
  isSeasonFinished$: Observable<boolean>;
  champion$: Observable<Team | null>;
  loading$: Observable<boolean>;
  error$: Observable<string | null>;
  
  // Private properties
  private destroy$ = new Subject<void>();
  
  constructor(
    private store: Store<AppState>,
    private cdr: ChangeDetectorRef
  ) {
    // Selector'ları initialize et
    this.currentStandings$ = this.store.select(selectCurrentStandings);
    this.currentWeekMatches$ = this.store.select(selectCurrentWeekMatches);
    this.isSeasonFinished$ = this.store.select(selectIsSeasonFinished);
    this.champion$ = this.store.select(selectChampion);
    this.loading$ = this.store.select(selectLoading);
    this.error$ = this.store.select(selectError);
  }
  
  // Public methods
  ngOnInit(): void {
    this.initializeLeague();
  }
  
  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }
  
  // Lig başlatma
  initializeLeague(): void {
    this.store.dispatch(LeagueActions.initializeLeague());
  }
  
  // Haftalık maç oynatma
  playSpecificWeek(week: number): void {
    this.store.dispatch(LeagueActions.playSpecificWeek({ week }));
  }
  
  // Tüm sezonu oynatma
  playAllSeason(): void {
    this.store.dispatch(LeagueActions.playAllSeason());
  }
  
  // Lig sıfırlama
  resetLeague(): void {
    this.store.dispatch(LeagueActions.resetLeague());
  }
  
  // Hafta seçimi
  selectWeek(week: number): void {
    this.selectedWeekForMatches = week;
  }
  
  // Utility methods
  getPlayedMatches(): number {
    // Oynanan maç sayısını hesapla
  }
  
  getTotalMatches(): number {
    // Toplam maç sayısını hesapla
  }
  
  getNextWeekButtonText(): string {
    // Sonraki hafta buton metnini hesapla
  }
  
  isSeasonFinished(): boolean {
    // Sezon bitti mi kontrol et
  }
  
  getSeasonStatus(): string {
    // Sezon durumunu hesapla
  }
  
  getLeagueControlStatus(): string {
    // Lig kontrol durumunu hesapla
  }
}
```

### 📊 StandingsTableComponent

```typescript
@Component({
  selector: 'app-standings-table',
  standalone: true,
  imports: [CommonModule, TableModule],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class StandingsTableComponent implements OnInit {
  
  @Input() standings: TeamStats[] = [];
  @Input() isSeasonFinished: boolean = false;
  @Output() teamSelected = new EventEmitter<Team>();
  
  // Properties
  sortField: string = 'position';
  sortDirection: 'asc' | 'desc' = 'asc';
  filteredStandings: TeamStats[] = [];
  
  constructor(private cdr: ChangeDetectorRef) {}
  
  ngOnInit(): void {
    this.filteredStandings = [...this.standings];
  }
  
  // Public methods
  sortBy(field: string): void {
    if (this.sortField === field) {
      this.sortDirection = this.sortDirection === 'asc' ? 'desc' : 'asc';
    } else {
      this.sortField = field;
      this.sortDirection = 'asc';
    }
    
    this.applySorting();
  }
  
  onTeamClick(team: Team): void {
    this.teamSelected.emit(team);
  }
  
  // Utility methods
  getPositionClass(position: number): string {
    // Pozisyon sınıfını hesapla
  }
  
  getTeamColor(team: Team): string {
    // Takım rengini hesapla
  }
  
  getGoalPercentage(goalsFor: number, goalsAgainst: number): number {
    // Gol yüzdesini hesapla
  }
  
  getPointsPercentage(points: number, maxPoints: number): number {
    // Puan yüzdesini hesapla
  }
  
  private applySorting(): void {
    // Sıralama uygula
  }
}
```

### ⚽ MatchResultsComponent

```typescript
@Component({
  selector: 'app-match-results',
  standalone: true,
  imports: [CommonModule, CardModule, ButtonModule, InputNumberModule],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class MatchResultsComponent implements OnInit {
  
  @Input() weeklyMatches: WeekMatches[] = [];
  @Input() selectedWeek: number = 1;
  @Output() weekChanged = new EventEmitter<number>();
  @Output() matchResultEdited = new EventEmitter<{ matchId: number; homeScore: number; awayScore: number }>();
  
  // Properties
  currentWeekMatches: WeekMatches | undefined;
  editingMatch: Match | null = null;
  
  constructor(private cdr: ChangeDetectorRef) {}
  
  ngOnInit(): void {
    this.updateCurrentWeekMatches();
  }
  
  ngOnChanges(changes: SimpleChanges): void {
    if (changes['selectedWeek'] || changes['weeklyMatches']) {
      this.updateCurrentWeekMatches();
    }
  }
  
  // Public methods
  selectWeek(week: number): void {
    this.selectedWeek = week;
    this.weekChanged.emit(week);
    this.updateCurrentWeekMatches();
  }
  
  editMatch(match: Match): void {
    this.editingMatch = { ...match };
  }
  
  saveMatchResult(): void {
    if (this.editingMatch) {
      this.matchResultEdited.emit({
        matchId: this.editingMatch.id,
        homeScore: this.editingMatch.homeScore || 0,
        awayScore: this.editingMatch.awayScore || 0
      });
      this.editingMatch = null;
    }
  }
  
  cancelEdit(): void {
    this.editingMatch = null;
  }
  
  // Utility methods
  getMatchResultClass(match: Match): string {
    // Maç sonucu sınıfını hesapla
  }
  
  getWeekStatus(week: number): string {
    // Hafta durumunu hesapla
  }
  
  private updateCurrentWeekMatches(): void {
    this.currentWeekMatches = this.weeklyMatches.find(wm => wm.week === this.selectedWeek);
    this.cdr.markForCheck();
  }
}
```

---

## 📝 Type Definitions

### 🎯 Utility Types

```typescript
// Generic types
export type SortDirection = 'asc' | 'desc';
export type SortField = 'position' | 'team' | 'played' | 'won' | 'drawn' | 'lost' | 'goalsFor' | 'goalsAgainst' | 'goalDifference' | 'points';

// Event types
export interface TeamSelectedEvent {
  team: Team;
  position: number;
}

export interface MatchEditedEvent {
  matchId: number;
  homeScore: number;
  awayScore: number;
}

// Configuration types
export interface LeagueConfig {
  totalTeams: number;
  totalWeeks: number;
  matchesPerWeek: number;
  pointsWin: number;
  pointsDraw: number;
  pointsLoss: number;
}

// API response types
export interface ApiResponse<T> {
  data: T;
  success: boolean;
  message?: string;
  error?: string;
}

export interface LeagueInitializationResponse {
  teams: Team[];
  matches: Match[];
  standings: TeamStats[];
}
```

### 🎯 Enum Definitions

```typescript
export enum LeagueStatus {
  NOT_STARTED = 'NOT_STARTED',
  IN_PROGRESS = 'IN_PROGRESS',
  FINISHED = 'FINISHED'
}

export enum MatchStatus {
  NOT_PLAYED = 'NOT_PLAYED',
  PLAYED = 'PLAYED',
  CANCELLED = 'CANCELLED'
}

export enum TeamPosition {
  CHAMPION = 1,
  EUROPA_LEAGUE = 2,
  CONFERENCE_LEAGUE = 3,
  RELEGATION = 4
}
```

---

## 🔍 Utility Functions

### 🎯 Helper Functions

```typescript
// league.utils.ts

/**
 * Maçları haftalara göre gruplar
 * @param matches - Tüm maçlar
 * @returns WeekMatches[] - Haftalık maçlar
 */
export function groupMatchesByWeek(matches: Match[]): WeekMatches[] {
  const weeklyMatches: { [week: number]: Match[] } = {};
  
  matches.forEach(match => {
    if (!weeklyMatches[match.week]) {
      weeklyMatches[match.week] = [];
    }
    weeklyMatches[match.week].push(match);
  });
  
  return Object.keys(weeklyMatches).map(week => ({
    week: parseInt(week),
    matches: weeklyMatches[parseInt(week)]
  })).sort((a, b) => a.week - b.week);
}

/**
 * Puan tablosunu hesaplar
 * @param teams - Takım listesi
 * @param matches - Tüm maçlar
 * @returns TeamStats[] - Puan tablosu
 */
export function calculateStandings(teams: Team[], matches: Match[]): TeamStats[] {
  const teamStats = teams.map(team => calculateTeamStats(team, matches));
  
  // Sıralama: Puan > Averaj > Attığı gol
  return teamStats.sort((a, b) => {
    if (b.points !== a.points) return b.points - a.points;
    if (b.goalDifference !== a.goalDifference) return b.goalDifference - a.goalDifference;
    return b.goalsFor - a.goalsFor;
  }).map((stats, index) => ({
    ...stats,
    position: index + 1
  }));
}

/**
 * Takım istatistiklerini hesaplar
 * @param team - Takım
 * @param matches - Tüm maçlar
 * @returns TeamStats - Takım istatistikleri
 */
export function calculateTeamStats(team: Team, matches: Match[]): TeamStats {
  const teamMatches = matches.filter(match => 
    match.homeTeam.id === team.id || match.awayTeam.id === team.id
  );
  
  let played = 0, won = 0, drawn = 0, lost = 0;
  let goalsFor = 0, goalsAgainst = 0;
  
  teamMatches.forEach(match => {
    if (match.isPlayed && match.homeScore !== null && match.awayScore !== null) {
      played++;
      
      const isHome = match.homeTeam.id === team.id;
      const teamScore = isHome ? match.homeScore : match.awayScore;
      const opponentScore = isHome ? match.awayScore : match.homeScore;
      
      goalsFor += teamScore;
      goalsAgainst += opponentScore;
      
      if (teamScore > opponentScore) won++;
      else if (teamScore === opponentScore) drawn++;
      else lost++;
    }
  });
  
  const points = won * LEAGUE_CONSTANTS.POINTS_WIN + 
                 drawn * LEAGUE_CONSTANTS.POINTS_DRAW + 
                 lost * LEAGUE_CONSTANTS.POINTS_LOSS;
  
  return {
    team,
    position: 0, // Sıralama ayrıca hesaplanacak
    played,
    won,
    drawn,
    lost,
    goalsFor,
    goalsAgainst,
    goalDifference: goalsFor - goalsAgainst,
    points
  };
}

/**
 * UEFA pozisyon sınıfını hesaplar
 * @param position - Takım pozisyonu
 * @returns string - CSS sınıfı
 */
export function getPositionClass(position: number): string {
  switch (position) {
    case 1:
      return 'bg-gradient-to-r from-yellow-50 to-amber-100 border-l-4 border-yellow-500 hover:from-yellow-100 hover:to-amber-200';
    case 2:
      return 'bg-gradient-to-r from-green-50 to-emerald-100 border-l-4 border-green-500 hover:from-green-100 hover:to-emerald-200';
    case 3:
      return 'bg-gradient-to-r from-blue-50 to-indigo-100 border-l-4 border-blue-500 hover:from-blue-100 hover:to-indigo-200';
    case 4:
      return 'bg-gradient-to-r from-red-50 to-rose-100 border-l-4 border-red-500 hover:from-red-100 hover:to-rose-200';
    default:
      return 'hover:bg-gray-50';
  }
}

/**
 * Takım rengini hesaplar
 * @param team - Takım
 * @returns string - CSS rengi
 */
export function getTeamColor(team: Team): string {
  return team.color || '#6b7280';
}

/**
 * Gol yüzdesini hesaplar
 * @param goalsFor - Attığı gol
 * @param goalsAgainst - Yediği gol
 * @returns number - Yüzde
 */
export function getGoalPercentage(goalsFor: number, goalsAgainst: number): number {
  const total = goalsFor + goalsAgainst;
  return total > 0 ? (goalsFor / total) * 100 : 0;
}

/**
 * Puan yüzdesini hesaplar
 * @param points - Mevcut puan
 * @param maxPoints - Maksimum puan
 * @returns number - Yüzde
 */
export function getPointsPercentage(points: number, maxPoints: number): number {
  return maxPoints > 0 ? (points / maxPoints) * 100 : 0;
}
```

---

## 🎉 Sonuç

Bu API dokümantasyonu, projedeki tüm servisler, modeller ve API'ları kapsamlı olarak açıklar. Her bir bileşenin nasıl kullanılacağı, hangi parametreleri aldığı ve ne döndürdüğü detaylı olarak belirtilmiştir.

### 🌟 API Avantajları

1. **Type Safety** - TypeScript ile tam tip güvenliği
2. **Reactive Programming** - RxJS ile asenkron veri yönetimi
3. **State Management** - NgRx ile merkezi state yönetimi
4. **Modular Design** - Her servis bağımsız ve test edilebilir
5. **Clean API** - Anlaşılır ve kullanımı kolay API'lar

### 🚀 Gelecek Geliştirmeler

- [ ] **API Documentation** (Swagger/OpenAPI)
- [ ] **GraphQL** integration
- [ ] **Real-time API** (WebSocket)
- [ ] **API Versioning**
- [ ] **Rate Limiting**

---

**🎯 Bu API dokümantasyonu, geliştiricilerin projeyi kolayca anlayıp geliştirebilmesi için hazırlanmıştır.**
