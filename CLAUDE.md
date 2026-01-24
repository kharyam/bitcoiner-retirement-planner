# Bitcoin Retirement Portfolio Planner - Development Prompt

## Project Overview
A sophisticated single-page web application for modeling retirement portfolio growth with emphasis on Bitcoin and Bitcoin-related assets (MSTR, FBTC). The app uses power law modeling for Bitcoin price projections and supports complex tax-advantaged account modeling.

## Core Requirements

### Technology Stack
- **Frontend**: Pure HTML5, CSS3, Vanilla JavaScript (ES6+)
- **Charts**: Chart.js (latest version) for all visualizations
- **Storage**: Browser LocalStorage API for portfolio persistence
- **APIs**: CoinGecko (Bitcoin), Yahoo Finance or similar (stocks)
- **No frameworks**: Keep it simple, fast, and dependency-free

### Key Features

#### 1. Personal Information Management
- Current age input (numeric, 0-120)
- Retirement age input (must be > current age)
- Annual expenses at retirement (with inflation adjustment preview)
- Inflation rate (default 3%, adjustable 0-20%)
- Custom withdrawal rate option (optional, defaults to dynamic based on Bitcoin allocation)

#### 2. Bitcoin Power Law Growth Model
- **7 fixed time periods** with configurable CAGR:
  - Years 1-5: Default 32% CAGR
  - Years 6-10: Default 27% CAGR
  - Years 11-15: Default 20% CAGR
  - Years 16-20: Default 15% CAGR
  - Years 21-25: Default 12% CAGR
  - Years 26-30: Default 10% CAGR
  - Years 31+: Default 9% CAGR
- Real-time Bitcoin price fetching from CoinGecko API
- Manual override option for Bitcoin price
- Compound growth calculation across periods

#### 3. Asset Types & Pricing

**Supported Assets:**
- **Bitcoin**: Direct ownership, measured in BTC or satoshis
- **MSTR**: MicroStrategy stock with leverage ratio modeling
  - Default leverage: ~1.8x Bitcoin returns
  - Configurable leverage ratio (1.0x - 3.0x)
  - Auto-fetch current stock price
- **FBTC**: Fidelity Bitcoin ETF (1:1 tracking)
  - Current expense ratio: 0.25% (default, adjustable)
  - Auto-fetch current price
- **STRC**: Strive Total Return Fund
  - Par value: $100
  - Dividend yield: ~8% (adjustable)
  - Principal stays at par
  - Dividend reinvestment options
- **SATA**: Strive U.S. Semiconductor ETF (or similar)
  - Par value: ~$95-100
  - Dividend yield: ~8.5% (adjustable)
  - Principal stays at par
  - Dividend reinvestment options
- **Generic Stocks**: Any other stock holdings
  - Configurable CAGR (default 10%)

**Price Fetching:**
- Auto-fetch Bitcoin, MSTR, FBTC from APIs on load
- Refresh button for manual update
- Show timestamp of last fetch
- Always allow manual override
- Graceful fallback to cached/default values if API fails

#### 4. Account Types & Tax Treatment

**Account Types:**
- **Roth IRA**: Tax-free growth and withdrawals
- **Traditional IRA**: Tax-deferred growth, taxed withdrawals, 10% early penalty before 59.5
- **Traditional 401k**: Same as Traditional IRA
- **Roth 401k**: Same as Roth IRA
- **Taxable Brokerage**: No tax benefits, no penalties
- **Self-Custody Bitcoin**: Direct Bitcoin holdings, treated like taxable

**Account Features:**
- Unique user-defined name
- Account type selector
- Current balance
- Annual contribution amount
- Number of years to contribute
- Asset allocation for contributions (% to each asset type)
- Current holdings breakdown by asset

**Tax Modeling:**
- Early withdrawal penalty: 10% for traditional accounts before 59.5
- Gap Number calculation: Amount needed in taxable/brokerage to cover expenses until 59.5
- Smart withdrawal order:
  1. Taxable accounts first
  2. Self-custody Bitcoin
  3. If gap and under 59.5: show warning or toggle to allow penalty
  4. After 59.5: Roth first (tax-free), then Traditional (taxed)

#### 5. Dividend Reinvestment (STRC & SATA)
- **Default**: 100% reinvest back into the same position
- **Advanced option**: 
  - Specify % to reinvest in position
  - Specify % to invest in MSTR instead
  - Must total 100%
- Model dividend growth compounding over time

#### 6. Portfolio Growth Calculations

**Annual Growth Process (per account):**
1. **Apply contribution** (if within contribution years)
   - Allocate to assets based on contributionAllocation percentages
2. **Calculate asset-specific growth:**
   - Bitcoin: Use power law CAGR for current year
   - MSTR: `btcGrowthRate^leverageRatio` (compounded)
   - FBTC: Bitcoin growth minus expense ratio
   - STRC/SATA: Dividends only (principal constant), reinvest per settings
   - Generic Stocks: Fixed CAGR
3. **Apply inflation** to expenses tracking
4. **Track balances** by asset type and account

**Withdrawal Modeling:**
- Calculate "Forever Number" (retirement need):
  - Annual expenses at retirement (inflation-adjusted)
  - Multiply by withdrawal multiplier based on Bitcoin allocation:
    - ≥80% BTC: 8% withdrawal (12.5x multiplier)
    - ≥60% BTC: 6.25% withdrawal (16x multiplier)
    - ≥40% BTC: 5% withdrawal (20x multiplier)
    - <40% BTC: 4% withdrawal (25x multiplier)
- Show current portfolio vs. Forever Number
- Show Gap Number (amount needed before 59.5)

### 7. User Interface Design

#### Layout Structure
```
┌─────────────────────────────────────────────────────┐
│  🪙 Bitcoin Retirement Planner    🌙 💾 ⚙️ 📄      │
├─────────────────────────────────────────────────────┤
│                                                     │
│  📋 Personal Information                      [▼]  │
│  ┌───────────────────────────────────────────────┐ │
│  │ Age: [49] → Retirement: [52]  (3 years)      │ │
│  │ Annual Expenses: $150,000                    │ │
│  │ Inflation: 3% → $163,909 at retirement       │ │
│  └───────────────────────────────────────────────┘ │
│                                                     │
│  📈 Bitcoin Growth Model                      [▼]  │
│  ┌───────────────────────────────────────────────┐ │
│  │ Current Price: $102,450 🔄 (2 min ago)       │ │
│  │                                              │ │
│  │  Period     CAGR     Years                   │ │
│  │  1-5        [32%]     5                      │ │
│  │  6-10       [27%]     5                      │ │
│  │  ...                                         │ │
│  └───────────────────────────────────────────────┘ │
│                                                     │
│  💰 Asset Prices & Settings                   [▼]  │
│  ┌───────────────────────────────────────────────┐ │
│  │  MSTR: $450.25 🔄  Leverage: [1.8x] ━━●━━    │ │
│  │  FBTC: $102,450 🔄  Expense: [0.25%]         │ │
│  │  STRC: $100.00  Yield: [8%]                  │ │
│  │  SATA: $95.00   Yield: [8.5%]                │ │
│  │  Stocks CAGR: [10%]                          │ │
│  └───────────────────────────────────────────────┘ │
│                                                     │
│  💼 Portfolio Accounts                        [+]  │
│  ┌───────────────────────────────────────────────┐ │
│  │  📊 Main 401k          [Traditional 401k]    │ │
│  │  Current: $500,000                           │ │
│  │  +$23,000/year for 3 years                   │ │
│  │  ────────────────────────────────────────    │ │
│  │  Allocation:                                 │ │
│  │  ▓▓▓ MSTR 30%  ▓▓▓ FBTC 30%                 │ │
│  │  ▓▓ STRC 20%   ▓ SATA 10%  □ Stocks 10%     │ │
│  │  [Edit] [Clone] [Delete]                     │ │
│  └───────────────────────────────────────────────┘ │
│  ┌───────────────────────────────────────────────┐ │
│  │  💎 Bitcoin Stack     [Self-Custody]        │ │
│  │  Current: 2.5 BTC (256,125 USD)              │ │
│  │  +0.1 BTC/year for 5 years                   │ │
│  │  [Edit] [Clone] [Delete]                     │ │
│  └───────────────────────────────────────────────┘ │
│                                                     │
│  📊 Analysis Dashboard                             │
│  ┌───────────────────────────────────────────────┐ │
│  │ [Overview] [Growth] [Allocation] [Taxes]     │ │
│  │                                              │ │
│  │  ╔════════════════════════════════════════╗ │ │
│  │  ║                                        ║ │ │
│  │  ║     Stacked Area Chart                 ║ │ │
│  │  ║     (Portfolio Growth Over Time)       ║ │ │
│  │  ║                                        ║ │ │
│  │  ╚════════════════════════════════════════╝ │ │
│  │                                              │ │
│  │  Forever Number: $4,097,725   ✅ ON TRACK   │ │
│  │  Gap Number: $491,727         ✅ COVERED    │ │
│  │  Projected at Retirement: $4,500,000        │ │
│  └───────────────────────────────────────────────┘ │
│                                                     │
└─────────────────────────────────────────────────────┘
```

#### Visual Design Guidelines

**Color Palette:**
- **Light Theme**:
  - Background: #FFFFFF, #F8F9FA
  - Cards: #FFFFFF with subtle shadow
  - Primary: #2563EB (Blue)
  - Success: #10B981 (Green)
  - Warning: #F59E0B (Amber)
  - Danger: #EF4444 (Red)
  - Bitcoin: #F7931A (Orange)
  
- **Dark Theme**:
  - Background: #0F172A, #1E293B
  - Cards: #1E293B with glow
  - Primary: #60A5FA (Light Blue)
  - Success: #34D399 (Light Green)
  - Warning: #FBBF24 (Light Amber)
  - Danger: #F87171 (Light Red)
  - Bitcoin: #F7931A (Orange - same)

**Typography:**
- Headers: Inter, SF Pro, system-ui
- Body: -apple-system, BlinkMacSystemFont, "Segoe UI"
- Numbers: Tabular nums, monospace for alignment
- Font sizes: 
  - H1: 2rem (32px)
  - H2: 1.5rem (24px)
  - Body: 1rem (16px)
  - Small: 0.875rem (14px)

**Animations:**
- Chart transitions: 1s ease-in-out
- Card expand/collapse: 0.3s ease
- Value counter animations: Number rolling effect
- Hover states: 0.2s ease transform/shadow
- Loading spinners: Smooth rotation

**Chart Specifications:**

1. **Stacked Area Chart (Main Portfolio)**
   - X-axis: Years from now to retirement + 20 years
   - Y-axis: Portfolio value (USD)
   - Each account is a colored area
   - Smooth curves (tension: 0.4)
   - Interactive legend (click to hide/show accounts)
   - Tooltip shows: Year, Account breakdown, Total

2. **Power Law Projection Chart**
   - X-axis: Years
   - Y-axis: Bitcoin price (log scale optional)
   - Line showing projected price based on power law
   - Shaded confidence interval
   - Markers at period boundaries

3. **Account Comparison Chart**
   - Multiple lines, one per account
   - Same axes as stacked chart
   - Can toggle log scale
   - Highlight fastest growing account

4. **Asset Allocation Pie Chart**
   - Animated transitions as you scrub timeline
   - Timeline slider below chart (year 0 to retirement+20)
   - Show percentage labels
   - Bitcoin assets in orange shades
   - Stock assets in blue shades

5. **Bitcoin Dominance Line Chart**
   - X-axis: Years
   - Y-axis: Percentage of portfolio
   - Shows % of portfolio in BTC + BTC-related (MSTR, FBTC)
   - Threshold lines at 40%, 60%, 80% (withdrawal rate boundaries)

6. **Withdrawal Sustainability Heatmap**
   - X-axis: Withdrawal rate (3% - 10%)
   - Y-axis: Years
   - Color intensity: Green (safe) → Yellow → Red (depleted)
   - Shows how long portfolio lasts at various withdrawal rates

#### Interactive Elements

**Sliders:**
- Age, retirement age (with numeric input beside)
- CAGR for each power law period
- MSTR leverage ratio
- Contribution allocation percentages (sum to 100%)

**Buttons:**
- Primary: Large, rounded, shadow on hover
- Secondary: Outline style
- Danger: Red for delete
- Icon buttons: Circular, subtle

**Forms:**
- Inline editing where possible
- Validation with helpful error messages
- Auto-save to localStorage after 1s delay

**Help System:**
- (?) icon next to each section header
- Click to show tooltip/popover
- Examples and formulas shown
- Links to external resources

### 8. Data Persistence

**LocalStorage Schema:**
```javascript
{
  "portfolioVersion": "1.0",
  "savedAt": "2024-01-23T10:30:00Z",
  "personalInfo": {...},
  "bitcoinModel": {...},
  "assetPrices": {...},
  "accounts": [...],
  "dividendSettings": {...},
  "theme": "dark"
}
```

**Features:**
- Auto-save every time data changes (1s debounce)
- Export to JSON file
- Import from JSON file
- Clear all data button (with confirmation)
- Multiple portfolio support (future enhancement)

### 9. Performance Considerations

**Optimization:**
- Debounce input changes (300ms)
- Memoize expensive calculations
- Use requestAnimationFrame for animations
- Lazy load charts (only create when tab visible)
- Limit chart data points for long time horizons

**Caching:**
- Cache API responses for 5 minutes
- Store last successful fetch
- Show data freshness indicator

### 10. Error Handling

**API Failures:**
- Show toast notification
- Fall back to cached data
- Allow manual price entry
- Retry button

**Invalid Input:**
- Inline validation messages
- Prevent form submission
- Highlight problematic fields
- Suggest corrections

**Edge Cases:**
- Division by zero in growth calculations
- Negative account balances
- Contribution exceeding reasonable limits
- Retirement age before current age

## Development Guidelines

### Code Organization
```
/
├── index.html          # Main HTML structure
├── styles/
│   ├── main.css        # Core styles
│   ├── themes.css      # Light/dark themes
│   └── animations.css  # Animation definitions
├── js/
│   ├── app.js          # Main application logic
│   ├── calculations.js # Growth models & math
│   ├── charts.js       # Chart.js configurations
│   ├── storage.js      # LocalStorage management
│   ├── api.js          # Price fetching
│   └── ui.js           # UI interactions
└── README.md           # User documentation
```

### Code Style
- Use ES6+ features (const, let, arrow functions, template literals)
- Descriptive variable names (camelCase)
- JSDoc comments for functions
- Modular functions (single responsibility)
- Avoid deep nesting (early returns)

### Testing Checklist
- [ ] All calculations match Excel model
- [ ] Charts render correctly in both themes
- [ ] Mobile responsive (320px to 4K)
- [ ] LocalStorage save/load works
- [ ] API fetching with fallback works
- [ ] All animations are smooth (60fps)
- [ ] No console errors
- [ ] Accessibility (keyboard navigation, screen readers)

## Future Enhancement Ideas

### Phase 2 Features
- [ ] Monte Carlo simulation
- [ ] Multiple portfolios (compare scenarios)
- [ ] Roth conversion ladder modeling
- [ ] Backdoor Roth contributions
- [ ] Social Security integration
- [ ] Healthcare/insurance costs
- [ ] Estate planning (inheritance tax)
- [ ] Rebalancing strategies
- [ ] Dollar-cost averaging simulator

### Phase 3 Features
- [ ] PDF report generation
- [ ] Share portfolio (URL encoding)
- [ ] Community templates
- [ ] Historical backtesting
- [ ] What-if scenario comparison
- [ ] Integration with portfolio tracking APIs
- [ ] Mobile app (PWA)
- [ ] Multi-currency support

## References & Resources

### Bitcoin Power Law
- Original spreadsheet model (attached)
- Power law research: [bitcoin-power-law.com](https://bitcoin-power-law.com)

### APIs
- CoinGecko: https://www.coingecko.com/api/documentation
- Yahoo Finance alternatives: yfinance, Alpha Vantage, Finnhub

### Chart.js
- Documentation: https://www.chartjs.org/docs/latest/
- Examples: https://www.chartjs.org/samples/

### Financial Calculations
- Compound interest formula
- XIRR for irregular cash flows
- Safe withdrawal rate research (Trinity Study)

## Notes for Claude Code

When implementing this application:

1. **Start with the calculation engine** - ensure math is 100% accurate before building UI
2. **Build mobile-first** - easier to scale up than down
3. **Use semantic HTML** - better for accessibility and SEO
4. **Progressive enhancement** - work without JavaScript for basic viewing
5. **Comment complex formulas** - especially the power law growth calculation
6. **Test with extreme values** - very old age, very high contributions, etc.
7. **Beautiful defaults** - the app should look great with no customization
8. **Guided first-time experience** - tooltips or tour for new users

## Example User Journey

1. User opens app, sees clean interface with sample data
2. Updates personal info (age 49, retirement 52)
3. App fetches current Bitcoin price ($102,450)
4. User adds 401k account with $500k balance
5. Sets contribution: $23k/year for 3 years
6. Allocates: 30% MSTR, 30% FBTC, 20% STRC, 10% SATA, 10% Stocks
7. Adds self-custody Bitcoin account (2.5 BTC)
8. App calculates growth, shows beautiful stacked chart
9. Forever Number: $4.1M, Current projection: $4.5M ✅
10. User exports to PDF for records
11. Comes back next month, data auto-loaded from LocalStorage
12. Clicks refresh prices, sees updated projections

This should be a delightful, empowering tool for retirement planning!
