# 🛠️ Geliştirme Rehberi

> **Futbol Ligi Simülasyonu - Geliştirme, Test ve Deployment Rehberi**

Bu dokümantasyon, projenin geliştirme süreçlerini, test stratejilerini ve deployment adımlarını detaylı olarak açıklar.

## 📋 İçindekiler

- [🚀 Geliştirme Ortamı](#-geliştirme-ortamı)
- [📦 Proje Kurulumu](#-proje-kurulumu)
- [🔧 Geliştirme Araçları](#-geliştirme-araçları)
- [🧪 Test Stratejisi](#-test-stratejisi)
- [📊 Performans Optimizasyonu](#-performans-optimizasyonu)
- [🎨 Stil Geliştirme](#-stil-geliştirme)
- [🔍 Debugging](#-debugging)
- [📱 Responsive Geliştirme](#-responsive-geliştirme)
- [🚀 Build ve Deployment](#-build-ve-deployment)
- [📚 Best Practices](#-best-practices)
- [🐛 Troubleshooting](#-troubleshooting)

---

## 🚀 Geliştirme Ortamı

### 📋 Sistem Gereksinimleri

#### **Minimum Gereksinimler:**
- **Node.js**: 18.x veya üzeri
- **npm**: 9.x veya üzeri
- **Angular CLI**: 20.x
- **RAM**: 8GB (önerilen 16GB)
- **Disk**: 2GB boş alan

#### **Önerilen Geliştirme Ortamı:**
- **OS**: Windows 11, macOS 12+, Ubuntu 20.04+
- **IDE**: Visual Studio Code
- **Browser**: Chrome 120+, Firefox 120+, Safari 16+
- **Git**: 2.40+

### 🎯 Node.js Kurulumu

#### **Windows:**
```bash
# Node.js resmi sitesinden indirin
# https://nodejs.org/

# veya Chocolatey ile
choco install nodejs

# veya winget ile
winget install OpenJS.NodeJS
```

#### **macOS:**
```bash
# Homebrew ile
brew install node

# veya nvm ile
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install 20
nvm use 20
```

#### **Linux (Ubuntu/Debian):**
```bash
# NodeSource repository ekleyin
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

# Node.js kurun
sudo apt-get install -y nodejs
```

### 🎯 Angular CLI Kurulumu

```bash
# Global olarak Angular CLI kurun
npm install -g @angular/cli@20

# Versiyonu kontrol edin
ng version

# Yardım komutlarını görün
ng help
```

---

## 📦 Proje Kurulumu

### 🚀 İlk Kurulum

#### **1. Projeyi Klonlayın:**
```bash
git clone <repository-url>
cd angular-football-league-simulation-main
```

#### **2. Bağımlılıkları Yükleyin:**
```bash
npm install
```

#### **3. Geliştirme Sunucusunu Başlatın:**
```bash
npm start
# veya
ng serve
```

#### **4. Tarayıcıda Açın:**
```
http://localhost:4200
```

### 🔧 Konfigürasyon Dosyaları

#### **package.json:**
```json
{
  "name": "futbol-ligi-simulasyonu",
  "version": "1.0.0",
  "scripts": {
    "start": "ng serve",
    "build": "ng build",
    "build:prod": "ng build --configuration production",
    "test": "ng test",
    "test:watch": "ng test --watch",
    "test:coverage": "ng test --code-coverage",
    "lint": "ng lint",
    "e2e": "ng e2e",
    "watch": "ng build --watch"
  },
  "dependencies": {
    "@angular/core": "^20.0.0",
    "@angular/common": "^20.0.0",
    "@ngrx/store": "^20.0.0",
    "@ngrx/effects": "^20.0.0",
    "rxjs": "^7.8.1",
    "primeng": "^19.0.0",
    "tailwindcss": "^3.0.0"
  },
  "devDependencies": {
    "@angular/cli": "^20.0.0",
    "@angular-devkit/build-angular": "^20.0.0",
    "typescript": "^5.9.0",
    "karma": "^6.4.0",
    "jasmine": "^5.1.0"
  }
}
```

#### **angular.json:**
```json
{
  "projects": {
    "futbol-ligi-simulasyonu": {
      "projectType": "application",
      "schematics": {
        "@schematics/angular:component": {
          "style": "scss",
          "standalone": true
        }
      },
      "root": "",
      "sourceRoot": "src",
      "prefix": "app",
      "architect": {
        "build": {
          "builder": "@angular-devkit/build-angular:browser",
          "options": {
            "outputPath": "dist/futbol-ligi-simulasyonu",
            "index": "src/index.html",
            "main": "src/main.ts",
            "polyfills": "src/polyfills.ts",
            "tsConfig": "tsconfig.app.json",
            "assets": ["src/favicon.ico", "src/assets"],
            "styles": ["src/styles.scss"],
            "scripts": []
          },
          "configurations": {
            "production": {
              "budgets": [
                {
                  "type": "initial",
                  "maximumWarning": "500kb",
                  "maximumError": "1mb"
                },
                {
                  "type": "anyComponentStyle",
                  "maximumWarning": "2kb",
                  "maximumError": "4kb"
                }
              ],
              "outputHashing": "all"
            }
          }
        }
      }
    }
  }
}
```

---

## 🔧 Geliştirme Araçları

### 🎯 VS Code Eklentileri

#### **Gerekli Eklentiler:**
```json
{
  "recommendations": [
    "angular.ng-template",
    "ms-vscode.vscode-typescript-next",
    "bradlc.vscode-tailwindcss",
    "esbenp.prettier-vscode",
    "ms-vscode.vscode-json",
    "formulahendry.auto-rename-tag",
    "christian-kohler.path-intellisense",
    "ms-vscode.vscode-eslint",
    "bradlc.vscode-tailwindcss"
  ]
}
```

#### **VS Code Ayarları:**
```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.preferences.importModuleSpecifier": "relative",
  "emmet.includeLanguages": {
    "typescript": "html"
  },
  "tailwindCSS.includeLanguages": {
    "typescript": "html"
  }
}
```

### 🎯 TypeScript Konfigürasyonu

#### **tsconfig.json:**
```json
{
  "compileOnSave": false,
  "compilerOptions": {
    "baseUrl": "./",
    "outDir": "./dist/out-tsc",
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "sourceMap": true,
    "declaration": false,
    "downlevelIteration": true,
    "experimentalDecorators": true,
    "moduleResolution": "node",
    "importHelpers": true,
    "target": "ES2022",
    "module": "ES2022",
    "useDefineForClassFields": false,
    "lib": ["ES2022", "dom"]
  },
  "angularCompilerOptions": {
    "enableI18nLegacyMessageIdFormat": false,
    "strictInjectionParameters": true,
    "strictInputAccessModifiers": true,
    "strictTemplates": true
  }
}
```

### 🎯 ESLint Konfigürasyonu

#### **.eslintrc.json:**
```json
{
  "root": true,
  "ignorePatterns": ["projects/**/*"],
  "overrides": [
    {
      "files": ["*.ts"],
      "extends": [
        "eslint:recommended",
        "@typescript-eslint/recommended",
        "@angular-eslint/template/process-inline-templates"
      ],
      "rules": {
        "@angular-eslint/directive-selector": [
          "error",
          {
            "type": "attribute",
            "prefix": "app",
            "style": "camelCase"
          }
        ],
        "@angular-eslint/component-selector": [
          "error",
          {
            "type": "element",
            "prefix": "app",
            "style": "kebab-case"
          }
        ]
      }
    },
    {
      "files": ["*.html"],
      "extends": ["@angular-eslint/template/recommended"],
      "rules": {}
    }
  ]
}
```

---

## 🧪 Test Stratejisi

### 🎯 Test Türleri

#### **1. Unit Tests (Birim Testler)**
```typescript
// league-dashboard.component.spec.ts
describe('LeagueDashboardComponent', () => {
  let component: LeagueDashboardComponent;
  let fixture: ComponentFixture<LeagueDashboardComponent>;
  let store: MockStore<AppState>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [LeagueDashboardComponent],
      providers: [
        provideMockStore({
          initialState: {
            league: {
              teams: mockTeams,
              matches: mockMatches,
              standings: mockStandings,
              currentWeek: 1,
              totalWeeks: 6,
              isSeasonFinished: false,
              champion: null,
              loading: false,
              error: null
            }
          }
        })
      ]
    });
    
    fixture = TestBed.createComponent(LeagueDashboardComponent);
    component = fixture.componentInstance;
    store = TestBed.inject(MockStore);
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should calculate played matches correctly', () => {
    component.currentWeekMatches = mockWeekMatches;
    const playedMatches = component.getPlayedMatches();
    expect(playedMatches).toBe(2);
  });

  it('should return correct button text for first week', () => {
    spyOn(component, 'getPlayedMatches').and.returnValue(0);
    const buttonText = component.getNextWeekButtonText();
    expect(buttonText).toBe('1. Hafta Ligi Başlat');
  });

  it('should dispatch playSpecificWeek action', () => {
    spyOn(store, 'dispatch');
    component.playSpecificWeek(1);
    expect(store.dispatch).toHaveBeenCalledWith(
      LeagueActions.playSpecificWeek({ week: 1 })
    );
  });
});
```

#### **2. Integration Tests (Entegrasyon Testleri)**
```typescript
// league-simulation.integration.spec.ts
describe('League Simulation Integration', () => {
  let service: LeagueSimulationService;
  let store: Store<AppState>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [
        LeagueSimulationService,
        provideStore({ league: leagueReducer }),
        provideEffects([LeagueEffects])
      ]
    });
    
    service = TestBed.inject(LeagueSimulationService);
    store = TestBed.inject(Store);
  });

  it('should play all matches and determine champion', (done) => {
    // Test data
    const teams = mockTeams;
    const matches = mockMatches;
    
    // Dispatch initialize league
    store.dispatch(LeagueActions.initializeLeagueSuccess({ teams, matches }));
    
    // Play all season
    store.dispatch(LeagueActions.playAllSeason());
    
    // Wait for effects to complete
    setTimeout(() => {
      const state = store.selectSnapshot(selectLeagueState);
      expect(state.isSeasonFinished).toBe(true);
      expect(state.champion).toBeTruthy();
      expect(state.standings.length).toBe(4);
      done();
    }, 1000);
  });
});
```

#### **3. E2E Tests (End-to-End Testler)**
```typescript
// e2e/src/app.e2e-spec.ts
describe('Futbol Ligi Simülasyonu', () => {
  beforeEach(() => {
    cy.visit('/');
  });

  it('should display league dashboard', () => {
    cy.get('app-league-dashboard').should('be.visible');
    cy.get('h1').should('contain', 'Futbol Ligi Simülasyonu');
  });

  it('should start league and play matches', () => {
    // Start league
    cy.get('button').contains('1. Hafta Ligi Başlat').click();
    
    // Wait for matches to load
    cy.get('app-match-results').should('be.visible');
    
    // Check standings table
    cy.get('app-standings-table').should('be.visible');
    cy.get('tbody tr').should('have.length', 4);
  });

  it('should display champion celebration', () => {
    // Play all season
    cy.get('button').contains('Tüm Sezonu Oyna').click();
    
    // Wait for season to finish
    cy.get('app-champion-celebration', { timeout: 10000 }).should('be.visible');
    cy.get('.champion-name').should('be.visible');
  });
});
```

### 🎯 Test Komutları

```bash
# Unit testleri çalıştır
npm test

# Test coverage raporu
npm run test:coverage

# E2E testleri çalıştır
npm run e2e

# Test watch mode
npm run test:watch

# Lint kontrolü
npm run lint
```

### 🎯 Test Coverage

#### **Coverage Konfigürasyonu:**
```json
// karma.conf.js
module.exports = function (config) {
  config.set({
    coverageReporter: {
      dir: require('path').join(__dirname, './coverage/futbol-ligi-simulasyonu'),
      subdir: '.',
      reporters: [
        { type: 'html' },
        { type: 'text-summary' },
        { type: 'lcov' }
      ],
      check: {
        global: {
          statements: 80,
          branches: 80,
          functions: 80,
          lines: 80
        }
      }
    }
  });
};
```

---

## 📊 Performans Optimizasyonu

### 🚀 Change Detection Optimizasyonu

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

detachView() {
  this.cdr.detach();
}

reattachView() {
  this.cdr.reattach();
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

#### **Dynamic Component Loading:**
```typescript
async loadChampionCelebration() {
  const { ChampionCelebrationComponent } = await import('./champion-celebration.component');
  // Component'i dinamik olarak yükle
}
```

### 📦 Bundle Optimization

#### **Tree Shaking:**
```typescript
// Sadece kullanılan PrimeNG modülleri import edin
import { CardModule } from 'primeng/card';
import { ButtonModule } from 'primeng/button';
// Tüm PrimeNG import etmeyin
```

#### **Code Splitting:**
```typescript
// Dynamic imports
const StandingsTableComponent = () => import('./standings-table.component');
```

### 🎯 Memory Management

#### **Subscription Management:**
```typescript
export class LeagueDashboardComponent implements OnDestroy {
  private destroy$ = new Subject<void>();
  
  ngOnInit() {
    this.store.select(selectCurrentStandings)
      .pipe(takeUntil(this.destroy$))
      .subscribe(standings => {
        // Handle standings
      });
  }
  
  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

#### **Object Pooling:**
```typescript
// Büyük objeler için object pooling
class MatchPool {
  private pool: Match[] = [];
  
  getMatch(): Match {
    return this.pool.pop() || new Match();
  }
  
  returnMatch(match: Match) {
    this.pool.push(match);
  }
}
```

---

## 🎨 Stil Geliştirme

### 🎯 Tailwind CSS

#### **Utility Classes:**
```css
/* Responsive design */
.responsive-grid {
  @apply grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4;
}

/* Hover effects */
.hover-lift {
  @apply transition-transform duration-300 hover:transform hover:-translate-y-1;
}

/* Gradient backgrounds */
.gradient-bg {
  @apply bg-gradient-to-br from-blue-50 to-indigo-100;
}
```

#### **Custom Components:**
```css
/* Custom button styles */
.btn-primary {
  @apply bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded transition-colors duration-200;
}

.btn-secondary {
  @apply bg-gray-600 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded transition-colors duration-200;
}
```

### 🎯 SCSS

#### **Variables:**
```scss
// _variables.scss
$primary-color: #3b82f6;
$secondary-color: #6b7280;
$success-color: #16a34a;
$warning-color: #d97706;
$danger-color: #dc2626;

$border-radius: 0.375rem;
$box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
$transition: all 0.3s ease;
```

#### **Mixins:**
```scss
// _mixins.scss
@mixin flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

@mixin hover-lift {
  transition: transform 0.3s ease;
  
  &:hover {
    transform: translateY(-2px);
  }
}

@mixin responsive-grid($columns: 1) {
  display: grid;
  grid-template-columns: repeat($columns, 1fr);
  gap: 1rem;
  
  @media (min-width: 768px) {
    grid-template-columns: repeat($columns * 2, 1fr);
  }
  
  @media (min-width: 1024px) {
    grid-template-columns: repeat($columns * 3, 1fr);
  }
}
```

### 🎯 CSS Animations

#### **Keyframes:**
```scss
// _animations.scss
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes slideIn {
  from {
    transform: translateX(-100%);
  }
  to {
    transform: translateX(0);
  }
}

@keyframes bounce {
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
```scss
.fade-in {
  animation: fadeIn 0.6s ease-out;
}

.slide-in {
  animation: slideIn 0.5s ease-out;
}

.bounce {
  animation: bounce 2s infinite;
}
```

---

## 🔍 Debugging

### 🎯 Angular DevTools

#### **Kurulum:**
```bash
# Chrome Extension
# https://chrome.google.com/webstore/detail/angular-devtools

# veya npm ile
npm install -g @angular/devtools
```

#### **Kullanım:**
```typescript
// Component'te debugging
export class LeagueDashboardComponent {
  ngOnInit() {
    // DevTools'ta görünecek
    console.log('Component initialized');
    
    // State'i inspect et
    this.store.select(selectLeagueState).subscribe(state => {
      console.log('Current state:', state);
    });
  }
}
```

### 🎯 Console Debugging

#### **Debug Helpers:**
```typescript
// debug.helpers.ts
export function debugLog(message: string, data?: any) {
  if (!environment.production) {
    console.log(`[DEBUG] ${message}`, data);
  }
}

export function debugError(message: string, error: any) {
  if (!environment.production) {
    console.error(`[ERROR] ${message}`, error);
  }
}

export function debugWarn(message: string, data?: any) {
  if (!environment.production) {
    console.warn(`[WARN] ${message}`, data);
  }
}
```

#### **Component'te Kullanım:**
```typescript
import { debugLog, debugError } from '../utils/debug.helpers';

export class LeagueDashboardComponent {
  ngOnInit() {
    debugLog('League Dashboard initialized');
    
    this.store.select(selectCurrentStandings).subscribe({
      next: (standings) => {
        debugLog('Standings updated', standings);
      },
      error: (error) => {
        debugError('Error loading standings', error);
      }
    });
  }
}
```

### 🎯 Network Debugging

#### **HTTP Interceptor:**
```typescript
@Injectable()
export class DebugInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    if (!environment.production) {
      console.log('HTTP Request:', req);
    }
    
    return next.handle(req).pipe(
      tap(event => {
        if (!environment.production && event instanceof HttpResponse) {
          console.log('HTTP Response:', event);
        }
      })
    );
  }
}
```

---

## 📱 Responsive Geliştirme

### 🎯 Mobile First Approach

#### **Breakpoint Strategy:**
```scss
// Mobile first approach
.component {
  // Mobile styles (default)
  padding: 1rem;
  font-size: 1rem;
  
  // Tablet
  @media (min-width: 768px) {
    padding: 1.5rem;
    font-size: 1.125rem;
  }
  
  // Desktop
  @media (min-width: 1024px) {
    padding: 2rem;
    font-size: 1.25rem;
  }
  
  // Large desktop
  @media (min-width: 1280px) {
    padding: 2.5rem;
    font-size: 1.5rem;
  }
}
```

#### **Grid System:**
```scss
.responsive-grid {
  display: grid;
  gap: 1rem;
  
  // Mobile: 1 column
  grid-template-columns: 1fr;
  
  // Tablet: 2 columns
  @media (min-width: 768px) {
    grid-template-columns: repeat(2, 1fr);
  }
  
  // Desktop: 3 columns
  @media (min-width: 1024px) {
    grid-template-columns: repeat(3, 1fr);
  }
  
  // Large desktop: 4 columns
  @media (min-width: 1280px) {
    grid-template-columns: repeat(4, 1fr);
  }
}
```

### 🎯 Touch Optimization

#### **Touch Targets:**
```scss
.touch-target {
  min-height: 44px;
  min-width: 44px;
  padding: 0.75rem;
  
  // Hover effects for touch devices
  @media (hover: hover) {
    &:hover {
      background-color: rgba(0, 0, 0, 0.05);
    }
  }
}
```

#### **Swipe Gestures:**
```typescript
// Swipe directive
@Directive({
  selector: '[appSwipe]'
})
export class SwipeDirective {
  @Output() swipeLeft = new EventEmitter();
  @Output() swipeRight = new EventEmitter();
  
  private startX = 0;
  private startY = 0;
  
  @HostListener('touchstart', ['$event'])
  onTouchStart(event: TouchEvent) {
    this.startX = event.touches[0].clientX;
    this.startY = event.touches[0].clientY;
  }
  
  @HostListener('touchend', ['$event'])
  onTouchEnd(event: TouchEvent) {
    const endX = event.changedTouches[0].clientX;
    const endY = event.changedTouches[0].clientY;
    
    const diffX = this.startX - endX;
    const diffY = this.startY - endY;
    
    if (Math.abs(diffX) > Math.abs(diffY)) {
      if (diffX > 50) {
        this.swipeLeft.emit();
      } else if (diffX < -50) {
        this.swipeRight.emit();
      }
    }
  }
}
```

---

## 🚀 Build ve Deployment

### 🎯 Build Konfigürasyonu

#### **Production Build:**
```bash
# Production build
ng build --configuration production

# Build with stats
ng build --configuration production --stats-json

# Build analysis
npx webpack-bundle-analyzer dist/futbol-ligi-simulasyonu/stats.json
```

#### **Build Optimization:**
```json
// angular.json
{
  "configurations": {
    "production": {
      "budgets": [
        {
          "type": "initial",
          "maximumWarning": "500kb",
          "maximumError": "1mb"
        },
        {
          "type": "anyComponentStyle",
          "maximumWarning": "2kb",
          "maximumError": "4kb"
        }
      ],
      "outputHashing": "all",
      "sourceMap": false,
      "namedChunks": false,
      "aot": true,
      "extractLicenses": true,
      "vendorChunk": false,
      "buildOptimizer": true
    }
  }
}
```

### 🎯 Deployment Strategies

#### **Static Hosting (Netlify/Vercel):**
```bash
# Build
ng build --configuration production

# Deploy to Netlify
netlify deploy --prod --dir=dist/futbol-ligi-simulasyonu

# Deploy to Vercel
vercel --prod
```

#### **Docker Deployment:**
```dockerfile
# Dockerfile
FROM node:20-alpine AS build

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build --configuration production

FROM nginx:alpine
COPY --from=build /app/dist/futbol-ligi-simulasyonu /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### **Nginx Konfigürasyonu:**
```nginx
# nginx.conf
server {
    listen 80;
    server_name localhost;
    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

---

## 📚 Best Practices

### 🎯 Code Organization

#### **File Naming:**
```
src/app/
├── components/           # Reusable components
│   ├── button/
│   │   ├── button.component.ts
│   │   ├── button.component.html
│   │   ├── button.component.scss
│   │   └── button.component.spec.ts
│   └── modal/
├── services/            # Business logic
│   ├── api.service.ts
│   └── auth.service.ts
├── models/              # Type definitions
│   ├── user.model.ts
│   └── api.model.ts
├── utils/               # Utility functions
│   ├── helpers.ts
│   └── constants.ts
└── shared/              # Shared modules
    ├── pipes/
    └── directives/
```

#### **Import Organization:**
```typescript
// 1. Angular imports
import { Component, OnInit, OnDestroy } from '@angular/core';
import { CommonModule } from '@angular/common';

// 2. Third-party imports
import { Store } from '@ngrx/store';
import { Observable } from 'rxjs';

// 3. Internal imports
import { Team } from '../models/team.model';
import { LeagueService } from '../services/league.service';
import { selectCurrentStandings } from '../store/selectors';
```

### 🎯 Error Handling

#### **Global Error Handler:**
```typescript
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
    // Send to logging service
  }
  
  private showErrorMessage(): void {
    // Show toast notification
  }
}
```

#### **Component Error Handling:**
```typescript
export class LeagueDashboardComponent {
  error$ = this.store.select(selectError);
  
  constructor(private store: Store<AppState>) {}
  
  ngOnInit() {
    this.error$.subscribe(error => {
      if (error) {
        this.handleError(error);
      }
    });
  }
  
  private handleError(error: string): void {
    // Show error message to user
    console.error('Component error:', error);
  }
}
```

### 🎯 Performance Best Practices

#### **OnPush Strategy:**
```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class OptimizedComponent {
  // Component sadece input değişikliklerinde güncellenir
}
```

#### **TrackBy Functions:**
```typescript
// Template'te
<div *ngFor="let team of teams; trackBy: trackByTeamId">
  {{ team.name }}
</div>

// Component'te
trackByTeamId(index: number, team: Team): number {
  return team.id;
}
```

#### **Async Pipe:**
```typescript
// Template'te
<div *ngIf="standings$ | async as standings">
  {{ standings.length }} teams
</div>

// Component'te
standings$ = this.store.select(selectCurrentStandings);
```

---

## 🐛 Troubleshooting

### 🎯 Common Issues

#### **1. Build Errors:**
```bash
# TypeScript errors
ng build --verbose

# Clear cache
rm -rf node_modules package-lock.json
npm install

# Update dependencies
ng update @angular/cli @angular/core
```

#### **2. Runtime Errors:**
```typescript
// Null check
if (this.standings && this.standings.length > 0) {
  // Safe to use standings
}

// Optional chaining
const teamName = this.team?.name ?? 'Unknown';
```

#### **3. Memory Leaks:**
```typescript
export class Component implements OnDestroy {
  private destroy$ = new Subject<void>();
  
  ngOnInit() {
    this.store.select(selectData)
      .pipe(takeUntil(this.destroy$))
      .subscribe(data => {
        // Handle data
      });
  }
  
  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

### 🎯 Debugging Tools

#### **Angular DevTools:**
- Component tree inspection
- State management debugging
- Performance profiling

#### **Browser DevTools:**
- Network tab for API calls
- Console for error messages
- Performance tab for bottlenecks

#### **Lighthouse:**
```bash
# Install Lighthouse
npm install -g lighthouse

# Run audit
lighthouse http://localhost:4200 --output html --output-path ./lighthouse-report.html
```

---

## 🎉 Sonuç

Bu geliştirme rehberi, projenin tüm geliştirme süreçlerini kapsamlı olarak açıklar. Bu rehberi takip ederek, modern Angular 20 uygulamaları geliştirebilir, test edebilir ve deploy edebilirsiniz.

### 🌟 Geliştirme Avantajları

1. **Modern Tooling** - En güncel geliştirme araçları
2. **Best Practices** - Endüstri standartları
3. **Performance** - Optimize edilmiş performans
4. **Maintainability** - Sürdürülebilir kod yapısı
5. **Scalability** - Ölçeklenebilir mimari

### 🚀 Gelecek Geliştirmeler

- [ ] **CI/CD Pipeline** (GitHub Actions)
- [ ] **Automated Testing** (E2E)
- [ ] **Performance Monitoring** (Web Vitals)
- [ ] **Error Tracking** (Sentry)
- [ ] **Analytics** (Google Analytics)

---

**🎯 Bu rehber, modern web geliştirme standartlarını karşılayan, profesyonel bir geliştirme süreci sunar.**

