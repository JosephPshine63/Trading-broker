# 🎨 Frontend - Trading Platform (React + Vite)

L'interfaccia utente della piattaforma di trading costruita con **React 18.2**, **Vite 5.0** e **Redux** per una gestione dello stato moderna e performante. UI components da **Radix UI** con styling **TailwindCSS**.

## 📋 Indice
- [Setup Rapido](#setup-rapido)
- [Architettura](#architettura)
- [Struttura Directory](#struttura-directory)
- [Redux Store](#redux-store)
- [Routing](#routing)
- [Componenti Principali](#componenti-principali)
- [API Client](#api-client)
- [Styling](#styling)
- [Build & Deploy](#build--deploy)

## 🚀 Setup Rapido

### Prerequisiti
- **Node.js 18+**
- **npm 9+**

### Installazione

```bash
# 1. Vai nella cartella frontend
cd frontend

# 2. Installa dipendenze
npm install

# 3. Esegui il dev server
npm run dev

# 4. App in esecuzione su http://localhost:5173
```

### Comandi Comuni

```bash
# Dev server con hot reload (Vite)
npm run dev

# Build per produzione
npm run build

# Preview build locale
npm run preview

# Lint codice con ESLint
npm run lint

# Pulisci cache Vite
npm run dev -- --force

# Build con source maps
npm run build -- --sourcemap
```

## 🏗️ Architettura

### MVC + Redux Pattern

```
┌──────────────────────────────────────────┐
│          Pages (Views)                   │
│  (Home, Portfolio, Wallet, Auth, etc)   │
└──────────────────────────────────────────┘
              ↓ ↓ ↓ (useSelector)
┌──────────────────────────────────────────┐
│      Redux Store (State Management)      │
│  (Auth, Coin, Wallet, Order, etc)       │
└──────────────────────────────────────────┘
              ↑ ↑ ↑ (dispatch)
┌──────────────────────────────────────────┐
│      Components (Riusabili)              │
│    (Buttons, Forms, Cards, etc)         │
└──────────────────────────────────────────┘
              ↓ ↓ ↓
┌──────────────────────────────────────────┐
│      API Client (Axios)                  │
│   (Http Requests to Backend)             │
└──────────────────────────────────────────┘
              ↓ ↓ ↓
┌──────────────────────────────────────────┐
│      Backend API (localhost:5454)        │
└──────────────────────────────────────────┘
```

## 📁 Struttura Directory

```
src/
├── pages/                      # Page Components (Routed)
│   ├── Home.jsx               # Dashboard criptovalute + chatbot
│   ├── StockDetails.jsx       # Dettagli moneta + trading form
│   ├── Portfolio.jsx          # Holdings + trading history
│   ├── Wallet.jsx             # Balance + transactions + topup
│   ├── Activity.jsx           # Transaction log
│   ├── Watchlist.jsx          # Monete salvate
│   ├── Profile.jsx            # Profilo utente + 2FA + password
│   ├── Auth.jsx               # Login + Signup
│   ├── PaymentDetails.jsx     # Bank account management
│   ├── Withdrawal.jsx         # Withdrawal requests
│   ├── Search.jsx             # Search coins
│   ├── TwoFactorAuth.jsx      # 2FA OTP verification
│   ├── ResetPassword.jsx      # Password reset flow
│   ├── WithdrawalAdmin.jsx    # Admin panel
│   └── NotFound.jsx           # 404 page
│
├── components/                 # Riusabili Components
│   ├── SpinnerBackdrop.jsx    # Loading spinner
│   ├── CustomeToast.jsx       # Toast notifications
│   └── [componenti specifici]
│
├── Api/                        # API Client
│   └── api.js                 # Axios instance + baseURL
│
├── Redux/                      # State Management
│   ├── Store.js               # Redux store configuration
│   ├── slices/                # Redux slices (reducers + actions)
│   │   ├── authSlice.js       # Auth state
│   │   ├── coinSlice.js       # Coins data
│   │   ├── walletSlice.js     # Wallet balance & transactions
│   │   ├── orderSlice.js      # Orders
│   │   ├── assetSlice.js      # User assets/holdings
│   │   ├── watchlistSlice.js  # Watchlist
│   │   ├── withdrawalSlice.js # Withdrawal requests
│   │   └── chatSlice.js       # Chatbot messages
│   │
│   └── [async thunks & reducers]
│
├── Admin/                      # Admin Components
│   └── WithdrawalAdmin.jsx
│
├── Util/                       # Utility Functions
│   ├── shouldShowNavbar.js    # Route-based navbar visibility
│   ├── readableDate.js        # Date formatting
│   ├── readableTimestamp.js   # Timestamp formatting
│   ├── maskAccountNumber.js   # Bank account masking
│   ├── calculateProfit.js     # Profit/Loss calculation
│   ├── existInWatchlist.js    # Watchlist checker
│   ├── fetchMarketData.js     # Market data fetcher
│   └── ConvertToChartData.js  # Chart data formatter
│
├── lib/                        # Helper Libraries
│   ├── [utility classes]
│   └── [custom hooks]
│
├── assets/                     # Static Assets
│   ├── images/
│   ├── icons/
│   └── styles/
│
├── App.jsx                     # Main App Component (Routing)
├── App.css                     # App styles
├── main.jsx                    # Entry Point
├── index.css                   # Global styles
│
└── [Config files]
    ├── vite.config.js         # Vite configuration
    ├── tailwind.config.js     # TailwindCSS config
    ├── jsconfig.json          # Path aliases
    └── .eslintrc.cjs          # ESLint rules
```

## 🔀 Routing

### App.jsx - Route Mapping

```javascript
// Utenti Non Autenticati
GET /           → Auth (Login/Signup)
GET /signup     → Signup Page
GET /signin     → Signin Page
GET /forgot-password → Password Recovery
POST /login-with-google → Google OAuth Callback
POST /two-factor-auth/:session → 2FA OTP
POST /reset-password/:token → Reset Password
POST /password-update-successfully → Confirmation

// Utenti Autenticati (ROLE_USER)
GET /           → Home (Coin List)
GET /portfolio  → Portfolio (Holdings + History)
GET /activity   → Transaction Activity
GET /wallet     → Wallet Management
GET /withdrawal → Withdrawal Requests
GET /payment-details → Bank Details
GET /wallet/success → Payment Success
GET /market/:id → Stock/Coin Details
GET /watchlist  → Saved Watchlist
GET /profile    → User Profile
GET /search     → Search Coins

// Admin Routes (ROLE_ADMIN)
GET /admin/withdrawal → Admin Panel (Withdrawals)
```

## 📊 Redux Store

### State Structure

```javascript
{
  auth: {
    user: {
      id, email, fullName, role, twoFactorAuthEnabled, emailVerified
    },
    jwt: "Bearer token...",
    loading: boolean,
    error: null,
    isVerified: boolean,
    twoFactorAuthEnabled: boolean
  },

  coin: {
    coinList: [],           // Tutte le monete
    coinDetails: {},        // Dettagli moneta singola
    marketChart: [],        // Dati grafico
    searchResults: [],      // Risultati ricerca
    top50Coins: [],         // Top 50 monete
    tradingCoins: [],       // Monete in trading
    loading: boolean,
    error: null
  },

  wallet: {
    walletBalance: 0,
    transactions: [],
    userWallet: {},
    loading: boolean,
    error: null
  },

  order: {
    orders: [],
    currentOrder: null,
    loading: boolean,
    error: null
  },

  asset: {
    userAssets: [],
    assetDetails: null,
    loading: boolean,
    error: null
  },

  watchlist: {
    watchlistCoins: [],
    watchlistId: null,
    loading: boolean
  },

  withdrawal: {
    withdrawalRequests: [],
    paymentDetails: [],
    loading: boolean
  },

  chat: {
    messages: [],
    currentMessage: "",
    loading: boolean
  }
}
```

### Redux Slices & Async Thunks

**Auth Slice:**
```javascript
// Actions
signUp(credentials) → Register new user
signIn(credentials) → Login user
verify2FA(otp) → Verify OTP
sendPasswordResetOTP(email) → Reset password
resetPassword(token, newPassword) → Confirm reset
refreshToken() → Refresh JWT
logout() → Clear auth state
```

**Coin Slice:**
```javascript
// Actions
fetchAllCoins() → Get all cryptocurrencies
fetchCoinDetails(coinId) → Get coin info
fetchMarketChart(coinId, days) → Get chart data
searchCoins(query) → Search cryptocurrency
fetchTop50Coins() → Get top 50
fetchTradingCoins() → Get trading list
```

**Wallet Slice:**
```javascript
// Actions
fetchWalletBalance() → Get wallet balance
fetchWalletTransactions() → Get transaction history
depositMoney(amount) → Add funds
withdrawMoney(amount) → Request withdrawal
transferMoney(toWalletId, amount) → Transfer between wallets
```

**Order Slice:**
```javascript
// Actions
createOrder(orderData) → Buy/Sell cryptocurrency
fetchOrderDetails(orderId) → Get order info
fetchUserOrders() → Get all user orders
```

**Asset Slice:**
```javascript
// Actions
fetchUserAssets() → Get user holdings
fetchAssetDetails(assetId) → Get asset info
fetchAssetByCoin(coinId) → Get asset for specific coin
```

## 🎨 Componenti Principali

### Pages

**Home.jsx**
- Lista criptovalute con paginazione
- Top 50 coins display
- Trading coins carousel
- AI Chatbot dialog
- Real-time coin updates

**StockDetails.jsx**
- Dettagli moneta (prezzo, market cap, 24h change)
- Grafico interattivo (ApexCharts)
- Form trading (Buy/Sell)
- Form validation (React Hook Form)
- Storico transazioni moneta

**Portfolio.jsx**
- Tabella holdings utente
- Profit/Loss per asset
- Storico trading completo
- Filtri e sorta

**Wallet.jsx**
- Saldo wallet
- Storico transazioni
- Form deposito
- Form trasferimento
- Form ritiro

**Profile.jsx**
- Informazioni utente
- Verificazione account
- Impostazioni 2FA
- Cambio password
- Account settings

**Auth.jsx**
- Form login
- Form signup
- OTP verification
- Google OAuth button
- Password recovery

### Components Riusabili

```javascript
// Layout
<Navbar />      // Navigation bar
<Sidebar />     // Side navigation
<Card />        // Card container

// Forms
<FormInput />   // Validated input
<FormSelect />  // Dropdown select
<FormCheckbox /> // Checkbox

// Data Display
<DataTable />   // Table component
<ChartComponent /> // Chart display
<Badge />       // Status badge

// Feedback
<CustomeToast />     // Toast notifications
<SpinnerBackdrop />  // Loading state
<ErrorAlert />       // Error message
```

## 🔗 API Client

### Api/api.js

```javascript
import axios from 'axios';

// Base configuration
const LOCALHOST = 'http://localhost:5454'
export const API_BASE_URL = LOCALHOST

// Axios instance
const api = axios.create({
  baseURL: API_BASE_URL
});

// JWT auto-injection
const token = localStorage.getItem('jwt');
api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
api.defaults.headers.post['Content-Type'] = 'application/json';

export default api;
```

### Usage Example

```javascript
// In Redux thunk
import api from '../Api/api';

export const fetchCoins = async () => {
  const response = await api.get('/coins');
  return response.data;
};

export const createOrder = async (orderData) => {
  const response = await api.post('/api/orders/pay', orderData);
  return response.data;
};
```

### Request Interceptors (Possibile)

```javascript
api.interceptors.request.use(config => {
  const token = localStorage.getItem('jwt');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

api.interceptors.response.use(
  response => response,
  error => {
    if (error.response?.status === 401) {
      // Token expired - refresh or logout
      dispatch(logout());
    }
    return Promise.reject(error);
  }
);
```

## 🎨 Styling

### TailwindCSS

```javascript
// Utility-first CSS framework
// Esempio: <div className="bg-blue-500 text-white p-4 rounded-lg">

// Configurazione: tailwind.config.js
module.exports = {
  content: ["./src/**/*.{js,jsx}"],
  theme: {
    extend: {
      colors: {
        primary: "#3b82f6",
        secondary: "#10b981"
      },
      animation: {
        // Custom animations
      }
    }
  },
  plugins: [require("tailwindcss-animate")],
};
```

### Radix UI Components

```javascript
import * as Dialog from "@radix-ui/react-dialog"
import * as Select from "@radix-ui/react-select"
import * as DropdownMenu from "@radix-ui/react-dropdown-menu"

// Componenti accessibili senza styled-components/emotion
// Utilizzare TailwindCSS per styling
```

### Custom Styling

```css
/* App.css - Global styles */
:root {
  --primary-color: #3b82f6;
  --secondary-color: #10b981;
  --background: #f3f4f6;
  --text-dark: #1f2937;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

## 📦 Form Validation

### React Hook Form + Zod

```javascript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

// Schema validation
const loginSchema = z.object({
  email: z.string().email("Email invalida"),
  password: z.string().min(6, "Min 6 caratteri")
});

// In component
const { register, handleSubmit, formState: { errors } } = useForm({
  resolver: zodResolver(loginSchema)
});

const onSubmit = (data) => {
  dispatch(signIn(data));
};
```

## 🔌 Features & Workflow

### Buy Cryptocurrency Flow

```
1. User navigates to /market/bitcoin
2. Frontend fetches coin details + chart
3. Displays trading form
4. User enters quantity
5. Calls POST /api/orders/pay
6. Redux updates order state
7. Success toast notification
8. Portfolio updated
```

### Deposit Money Flow

```
1. User clicks "Deposit" in Wallet
2. Enters amount
3. Selects payment method (Stripe/Razorpay)
4. Form submission
5. Backend processes payment
6. Redux updates wallet balance
7. Transaction added to history
```

### 2FA Verification Flow

```
1. User logs in (POST /auth/signin)
2. Backend sends OTP to email
3. Frontend redirects to /two-factor-auth/:session
4. User enters OTP
5. Calls POST /auth/two-factor/otp/{otp}
6. Backend verifies OTP
7. JWT token returned
8. Redirect to home
```

## 📈 Performance Optimization

### Bundle Size
```bash
# Analyze bundle
npm install -D @vite/plugin-visualizer
npm run build -- --analyze
```

### Code Splitting
```javascript
// Lazy load routes
const Portfolio = lazy(() => import('./pages/Portfolio'));
const Wallet = lazy(() => import('./pages/Wallet'));

// Suspense boundary
<Suspense fallback={<Spinner />}>
  <Portfolio />
</Suspense>
```

### Image Optimization
```javascript
// Use responsive images
<img src={coinImage} alt="coin" loading="lazy" />
```

## 🧪 Testing (Possibile)

```bash
# Install testing libraries
npm install --save-dev vitest @testing-library/react

# Run tests
npm run test

# Coverage
npm run test:coverage
```

## 🚀 Build & Deploy

### Development

```bash
# Hot reload server
npm run dev

# Server runs on http://localhost:5173
```

### Production Build

```bash
# Create optimized build
npm run build

# Output in ./dist folder
# Includes:
# - Minified JavaScript
# - Optimized CSS
# - Asset optimization
# - Source maps (optional)
```

### Deploy to Vercel

```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel

# Set environment variables in Vercel dashboard
# VITE_API_URL=https://api.trading-broker.com
```

### Deploy to Netlify

```bash
# Connect GitHub repo to Netlify
# Configure build command: npm run build
# Configure publish directory: dist
```

## 🌍 Environment Variables

Crea file `.env.local`:

```env
# API Configuration
VITE_API_BASE_URL=http://localhost:5454
VITE_API_PRODUCTION_URL=https://api.trading-broker.com

# Feature Flags
VITE_ENABLE_CHATBOT=true
VITE_ENABLE_2FA=true
VITE_ENABLE_WITHDRAWAL=true
```

Accedi in componenti:

```javascript
const apiUrl = import.meta.env.VITE_API_BASE_URL;
```

## 📞 Troubleshooting

| Problema | Soluzione |
|----------|-----------|
| **Vite port occupied** | `npm run dev -- --port 5174` |
| **Hot reload not working** | Restart dev server |
| **Redux state not updating** | Check thunk dispatch + reducer |
| **API 401 errors** | Token expired, need refresh |
| **Build errors** | `npm install` + clear cache |
| **Node modules corrupted** | `rm -rf node_modules && npm install` |

## 📚 Risorse Utili

- [React Docs](https://react.dev)
- [Vite Guide](https://vitejs.dev)
- [Redux Toolkit](https://redux-toolkit.js.org)
- [TailwindCSS](https://tailwindcss.com)
- [Radix UI](https://radix-ui.com)
- [React Hook Form](https://react-hook-form.com)
- [Zod Validation](https://zod.dev)

## 🎯 Next Steps

1. Installa dipendenze: `npm install`
2. Configura API URL in `Api/api.js`
3. Esegui dev server: `npm run dev`
4. Accedi all'app su `http://localhost:5173`
5. Testa login con credenziali backend
6. Prova trading, wallet, etc.

---

**Versione**: React 18.2 + Vite 5.0 | **Node**: 18+ | **Ultimo Aggiornamento**: 2024
