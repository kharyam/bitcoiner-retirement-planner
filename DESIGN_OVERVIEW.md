# Bitcoin Retirement Portfolio Planner - Design Specification

## Executive Summary

A beautiful, single-page web application for modeling retirement portfolios with sophisticated Bitcoin and Bitcoin-related asset modeling. The app uses power law growth projections, supports multiple tax-advantaged accounts, and provides stunning visualizations of portfolio growth over time.

---

## 1. Core Functionality

### 1.1 Personal Information
- **Current Age**: User's current age (0-120)
- **Retirement Age**: Target retirement age (must exceed current age)
- **Annual Expenses**: Expected annual spending at retirement
- **Inflation Rate**: Default 3%, adjustable to model expense growth
- **Withdrawal Rate**: 
  - **Dynamic (Default)**: Based on Bitcoin allocation percentage
    - ≥80% Bitcoin → 8% withdrawal rate (12.5x multiplier)
    - ≥60% Bitcoin → 6.25% withdrawal rate (16x multiplier)
    - ≥40% Bitcoin → 5% withdrawal rate (20x multiplier)
    - <40% Bitcoin → 4% withdrawal rate (25x multiplier)
  - **Custom**: User can override with fixed percentage

### 1.2 Bitcoin Power Law Model

**7 Fixed Time Periods** with configurable CAGR (Compound Annual Growth Rate):

| Period | Default CAGR | Description |
|--------|--------------|-------------|
| Years 1-5 | 32% | Near-term high growth |
| Years 6-10 | 27% | Strong continued growth |
| Years 11-15 | 20% | Maturing growth phase |
| Years 16-20 | 15% | Stabilizing growth |
| Years 21-25 | 12% | Long-term steady growth |
| Years 26-30 | 10% | Mature asset growth |
| Years 31+ | 9% | Very long-term growth |

**Calculation Method**:
- Compounds across periods based on years until retirement
- Example: Year 8 uses (1.32)^5 × (1.27)^3
- All Bitcoin-related assets derive from this base model

**Bitcoin Price**:
- Auto-fetched from CoinGecko API on app load
- Manual override always available
- Shows last update timestamp
- Falls back to cached price if API unavailable

### 1.3 Asset Types

#### Bitcoin (BTC)
- Direct ownership
- Input in BTC or satoshis (100M sats = 1 BTC)
- Growth based on power law model

#### MicroStrategy (MSTR)
- **Modeling Approach**: Leverage ratio on Bitcoin returns
- **Default Leverage**: 1.8x (MSTR grows 1.8x faster than Bitcoin)
- **Adjustable Range**: 1.0x - 3.0x
- **Price**: Auto-fetched from Yahoo Finance API
- **Rationale**: MSTR holds significant Bitcoin and uses debt leverage, resulting in amplified returns

#### Fidelity Bitcoin ETF (FBTC)
- **Tracking**: 1:1 with Bitcoin price
- **Expense Ratio**: 0.25% annually (default, adjustable)
- **Price**: Auto-fetched (should match Bitcoin price)
- **Growth**: Bitcoin growth rate minus expense ratio

#### Strive Total Return Fund (STRC)
- **Par Value**: $100 (principal stays constant)
- **Dividend Yield**: ~8% (adjustable)
- **Growth**: Dividends only
- **Reinvestment Options**:
  - 100% back into STRC (default)
  - Split between STRC and MSTR (e.g., 80% STRC, 20% MSTR)

#### SATA (Dividend Stock/Fund)
- **Par Value**: ~$95-100 (principal stays constant)
- **Dividend Yield**: ~8.5% (adjustable)
- **Growth**: Dividends only
- **Reinvestment Options**: Same as STRC

#### Generic Stocks
- **Purpose**: Catch-all for other stock holdings
- **Growth**: Fixed CAGR (default 10%)
- **No dividend modeling**: Simple appreciation only

### 1.4 Account Types

#### Tax-Advantaged Accounts

**Roth IRA / Roth 401k**:
- Tax-free growth
- Tax-free withdrawals
- No early withdrawal penalty
- Contribution limits apply (but not modeled in app)

**Traditional IRA / Traditional 401k**:
- Tax-deferred growth
- Withdrawals taxed as ordinary income
- 10% early withdrawal penalty if accessed before age 59.5
- Required Minimum Distributions (RMDs) at 73 (not modeled)

**Taxable Brokerage Account**:
- No tax advantages
- No withdrawal restrictions
- Capital gains tax on growth (not modeled in detail)

**Self-Custody Bitcoin**:
- Direct Bitcoin ownership (cold storage, hardware wallet, etc.)
- Treated similar to taxable account
- Measured in BTC, not USD

#### Account Features

Each account includes:
- **Name**: User-defined (e.g., "Main 401k", "Roth IRA", "Bitcoin Stack")
- **Account Type**: One of the above types
- **Current Balance**: Starting value in USD (or BTC for self-custody)
- **Annual Contribution**: Amount added each year
- **Contribution Duration**: How many years contributions continue
- **Contribution Allocation**: % split among asset types
  - Must total 100%
  - Applied to new contributions only
- **Current Holdings**: Breakdown by asset type
  - MSTR: Number of shares
  - FBTC: Number of shares
  - Bitcoin: BTC amount
  - STRC: Number of shares
  - SATA: Number of shares
  - Stocks: USD value

**Example Account Configuration**:
```
Name: "Main 401k"
Type: Traditional 401k
Balance: $500,000
Annual Contribution: $23,000
Contribution Years: 3
Contribution Allocation:
  - MSTR: 30%
  - FBTC: 30%
  - STRC: 20%
  - SATA: 10%
  - Stocks: 10%
```

### 1.5 Growth Calculations

**Annual Growth Process** (for each account, each year):

1. **Add Contribution** (if year ≤ contribution duration)
   - Allocate new money based on contribution allocation percentages
   - Convert USD to shares/BTC based on current prices

2. **Grow Each Asset**:
   - **Bitcoin**: Apply power law CAGR for current year
   - **MSTR**: `(1 + btcGrowthRate)^leverageRatio - 1`
   - **FBTC**: `btcGrowthRate - expenseRatio`
   - **STRC**: Add dividends, reinvest per settings
   - **SATA**: Add dividends, reinvest per settings
   - **Stocks**: Apply fixed CAGR

3. **Track Values**:
   - Update share counts, BTC amounts
   - Calculate USD values using current prices
   - Sum for account total

4. **Repeat** for all years from now until retirement + visualization period

### 1.6 Retirement Analysis

#### Forever Number
The amount needed at retirement to sustain annual spending indefinitely.

**Formula**: `Annual Spending × Multiplier`

Where multiplier depends on Bitcoin allocation:
- 80%+ Bitcoin: 12.5x (8% withdrawal)
- 60-80% Bitcoin: 16x (6.25% withdrawal)
- 40-60% Bitcoin: 20x (5% withdrawal)
- <40% Bitcoin: 25x (4% withdrawal)

**Example**: 
- Annual spending: $150,000
- Bitcoin allocation: 65%
- Multiplier: 16x
- Forever Number: $2,400,000

#### Gap Number
The amount needed in taxable/brokerage accounts to cover expenses from retirement until age 59.5 (when retirement accounts can be accessed penalty-free).

**Formula**: 
```
Years in Gap = max(0, 59.5 - retirementAge)
Spending at Retirement = annualExpenses × (1 + inflation)^yearsToRetirement
Average Return During Gap = weightedAverageOfTaxableAccounts
Gap Number = PV of annuity (spending, gap years, return rate)
```

**Status Indicators**:
- ✅ **Covered**: Taxable accounts > Gap Number
- ⚠️ **Review**: Close to Gap Number
- ❌ **Shortfall**: Need more in taxable accounts

#### Withdrawal Strategy (Tax-Efficient)

**Priority Order** (withdraw from accounts in this sequence):
1. **Taxable brokerage accounts** (no penalties)
2. **Self-custody Bitcoin** (if needed, treated like taxable)
3. **If under 59.5 and gap exists**:
   - Show warning about early withdrawal penalty
   - Offer toggle: "Allow early retirement account access" (incurs 10% penalty)
   - If toggle off: Show shortfall amount
4. **After 59.5 or if toggle on**:
   - Roth accounts first (tax-free)
   - Traditional accounts second (taxed but no penalty)

---

## 2. User Interface Design

### 2.1 Layout & Structure

**Responsive Single-Page App**:
- Fixed top navigation bar
- Scrollable main content area
- Collapsible/expandable sections
- Mobile-first design (320px - 4K screens)

**Top Navigation**:
```
┌────────────────────────────────────────────────────────┐
│ 🪙 Bitcoin Retirement Planner          🌙 💾 📄 ⚙️    │
└────────────────────────────────────────────────────────┘
```
- Logo/Title (left)
- Theme toggle 🌙 (dark/light)
- Save portfolio 💾
- Export PDF 📄
- Settings ⚙️

**Main Content Sections** (top to bottom):

1. **Personal Information Card** (collapsible)
2. **Bitcoin Growth Model Card** (collapsible)
3. **Asset Prices & Settings Card** (collapsible)
4. **Portfolio Builder** (main section, always expanded)
5. **Visualization Dashboard** (tabbed interface)
6. **Retirement Analysis Summary** (key metrics)

### 2.2 Visual Design

#### Color Scheme

**Light Theme**:
- Background: Clean white (#FFFFFF) with subtle gray (#F8F9FA)
- Cards: White with soft shadow
- Primary actions: Blue (#2563EB)
- Success indicators: Green (#10B981)
- Warning indicators: Amber (#F59E0B)
- Danger actions: Red (#EF4444)
- Bitcoin accent: Orange (#F7931A)

**Dark Theme**:
- Background: Deep navy (#0F172A) with lighter panels (#1E293B)
- Cards: Dark gray (#1E293B) with glow effect
- Primary actions: Light blue (#60A5FA)
- Success indicators: Light green (#34D399)
- Warning indicators: Light amber (#FBBF24)
- Danger actions: Light red (#F87171)
- Bitcoin accent: Orange (#F7931A) - same as light

#### Typography
- **Headers**: Inter, SF Pro Display, or system fonts
- **Body**: System UI fonts for consistency
- **Numbers**: Monospace for alignment
- **Sizes**: H1 32px, H2 24px, Body 16px, Small 14px

#### Animations

**Smooth Transitions**:
- Chart updates: 1 second ease-in-out
- Card expand/collapse: 0.3 seconds
- Value changes: Rolling number animation
- Hover effects: 0.2 seconds (transform, shadow)

**Loading States**:
- Spinner for API fetches
- Skeleton screens for charts
- Progress indicators for calculations

### 2.3 Chart Specifications

#### Chart 1: Stacked Area Chart (Portfolio Growth)
**Purpose**: Show total portfolio value over time with breakdown by account

**Design**:
- X-axis: Years (from now to retirement + 20 years)
- Y-axis: Total portfolio value (USD)
- Multiple colored areas, one per account
- Smooth curves (Bezier interpolation)
- Legend: Click to show/hide accounts
- Tooltip: Hover to see account breakdown for any year

**Visual**:
```
$5M  │                        ┌────── Total
     │                    ┌───┘
$4M  │                ┌───┘     
     │            ┌───┘          ▓ Main 401k
$3M  │        ┌───┘              ▓ Roth IRA
     │    ┌───┘                  ▓ Bitcoin Stack
$2M  │┌───┘                      ▓ Taxable
     │▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
$1M  │
     └──────────────────────────────────
     Now  5yr  10yr  15yr  Retirement
```

#### Chart 2: Bitcoin Power Law Projection
**Purpose**: Visualize expected Bitcoin price growth based on power law

**Design**:
- X-axis: Years
- Y-axis: Bitcoin price (USD, optional log scale)
- Line showing projected price path
- Markers at period boundaries (5, 10, 15, 20, 25, 30 years)
- Shaded region showing +/- one standard deviation (if calculated)

**Visual**:
```
$10M │                              ╱
     │                          ╱
 $1M │                      ╱
     │                  ╱
100K │──────────○──○──○──────────────
     │      ╱   5  10 15
 10K │  ╱
     └──────────────────────────────
     Now         Years
```

#### Chart 3: Account Comparison
**Purpose**: Compare growth rates of individual accounts

**Design**:
- Multiple colored lines, one per account
- Same axes as stacked chart
- Toggle between linear and log scale
- Highlight fastest growing account
- Legend shows final values

#### Chart 4: Asset Allocation Pie (with Timeline)
**Purpose**: Show how portfolio composition changes over time

**Design**:
- Pie chart showing % allocation by asset type
- Timeline slider below: scrub to see allocation at different years
- Smooth animated transitions as slider moves
- Color coding: Bitcoin assets (orange shades), Stocks (blue shades)
- Percentage labels on slices

**Visual**:
```
       ┌─────────┐
     ┌─┘         └─┐
    │   MSTR 35%   │
    │ ┌─────────┐  │
    │ │ FBTC    │  │
    │ │ 30%     │  │
    └─┤         ├──┘
      │ Stocks  │
      │ 20%     │
      └─────────┘
      
    [─────●──────────────────]
    Now   5yr  10yr      Retirement
```

#### Chart 5: Bitcoin Dominance Line
**Purpose**: Track what % of portfolio is Bitcoin-related over time

**Design**:
- X-axis: Years
- Y-axis: Percentage (0-100%)
- Line showing BTC + MSTR + FBTC as % of total
- Horizontal threshold lines at 40%, 60%, 80%
- Shaded regions indicating withdrawal rate zones

**Visual**:
```
100% │                    ┌────────
     │              ┌─────┘
 80% ├─────────────────────────────  8% withdrawal
     │        ┌─────┘
 60% ├─────────────────────────────  6.25% withdrawal
     │  ┌─────┘
 40% ├─────────────────────────────  5% withdrawal
     │──┘
  0% └──────────────────────────────
     Now     Years     Retirement
```

#### Chart 6: Withdrawal Sustainability Heatmap
**Purpose**: Show how long portfolio lasts at various withdrawal rates

**Design**:
- X-axis: Withdrawal rate (3% - 10%)
- Y-axis: Years
- Color gradient: Green (safe, 50+ years) → Yellow (20-50 years) → Red (depleted <20 years)
- Current withdrawal rate highlighted

**Visual**:
```
Years
 50 │ ████████░░░░░░░
 40 │ ████████░░░░░░░
 30 │ ████████▓▓▓▓░░░
 20 │ ████████▓▓▓▓▓▓▓
 10 │ ████████▓▓▓▓▓▓▓
    └──────────────────
    3%  4%  5%  6%  8% 10%
    
    ████ Safe  ▓▓▓▓ Risky  ░░░░ Depleted
```

### 2.4 Interactive Elements

#### Account Cards
Each account displayed as an elegant card:

```
┌─────────────────────────────────────────────────┐
│ 📊 Main 401k                 [Traditional 401k] │
│ ───────────────────────────────────────────────│
│ Current Balance: $500,000                       │
│ Annual Contribution: $23,000 for 3 years        │
│                                                 │
│ Contribution Allocation:                        │
│ ▓▓▓▓▓▓ MSTR 30%      ▓▓▓▓▓▓ FBTC 30%          │
│ ▓▓▓▓ STRC 20%        ▓▓ SATA 10%              │
│ ▓▓ Stocks 10%                                  │
│                                                 │
│ Current Holdings:                               │
│ • 333 shares MSTR @ $450 = $149,850            │
│ • 1.5 BTC via FBTC = $153,675                  │
│ • 400 shares STRC = $40,000                    │
│ • ...                                          │
│                                                 │
│ [Edit Details] [Duplicate] [Delete]            │
└─────────────────────────────────────────────────┘
```

**Actions**:
- **Edit**: Opens modal/drawer with full account editor
- **Duplicate**: Creates copy for what-if scenarios
- **Delete**: Confirms before removing

**Drag & Drop**:
- Drag handle (≡) on left side
- Reorder accounts to change stacking order in chart

#### Input Controls

**Sliders with Numeric Input**:
```
Age: ━━━━●━━━━━━ [49]
     18          120
```
- Draggable slider
- Type-in numeric field
- Synchronized values

**Percentage Allocation**:
```
MSTR:  ━━━●━━━━━━ 30%
FBTC:  ━━━●━━━━━━ 30%
STRC:  ━━●━━━━━━━ 20%
SATA:  ━●━━━━━━━━ 10%
Stocks: ━●━━━━━━━━ 10%
Total: 100% ✓
```
- Color-coded sliders
- Live sum validation
- Warning if total ≠ 100%

**Toggle Switches**:
```
Allow Early Retirement Account Access  [  ○━━  ]
                                       OFF   ON
```

#### Help System

**? Icons**:
- Placed next to section headers
- Click to reveal tooltip popover
- Contains:
  - Brief explanation
  - Formula (if applicable)
  - Example calculation
  - Link to learn more

**Example Tooltip**:
```
╭───────────────────────────────────────────╮
│ ❓ Forever Number                         │
│ ───────────────────────────────────────── │
│ The amount you need to sustain your       │
│ retirement indefinitely.                  │
│                                           │
│ Formula: Annual Expenses × Multiplier     │
│                                           │
│ Example: $150,000 × 16 = $2,400,000      │
│                                           │
│ The multiplier depends on your Bitcoin    │
│ allocation (higher BTC = higher safe      │
│ withdrawal rate).                         │
│                                           │
│ [Learn More →]                            │
╰───────────────────────────────────────────╯
```

### 2.5 Responsive Behavior

**Desktop (1024px+)**:
- Side-by-side layouts
- Large charts (800px+ wide)
- Expanded tooltips

**Tablet (768px - 1023px)**:
- Stacked sections
- Medium charts (600px wide)
- Collapsible help panels

**Mobile (320px - 767px)**:
- Single column layout
- Simplified charts (full width)
- Bottom sheets for editing
- Hamburger menu for navigation

---

## 3. Technical Implementation

### 3.1 Technology Stack

**Core Technologies**:
- HTML5 (semantic markup)
- CSS3 (Grid, Flexbox, Custom Properties)
- JavaScript ES6+ (modules, async/await, classes)

**Libraries**:
- **Chart.js** (v4.x): All visualizations
- **No framework**: Vanilla JS for simplicity and performance

**APIs**:
- **CoinGecko**: Bitcoin price (free, no API key)
- **Yahoo Finance** (or alternative): Stock prices (MSTR, FBTC)

**Storage**:
- **LocalStorage**: Portfolio persistence
- **JSON export/import**: Backup and sharing

### 3.2 Data Flow

```
User Input
    ↓
Data Validation
    ↓
Update Model (in-memory state)
    ↓
Recalculate Growth Projections
    ↓
Update Charts (animated transitions)
    ↓
Save to LocalStorage (debounced)
```

### 3.3 Performance Optimizations

**Calculations**:
- Memoize power law multipliers (cache by year)
- Debounce input changes (300ms)
- Use Web Workers for heavy calculations (if needed)

**Rendering**:
- requestAnimationFrame for animations
- Virtual scrolling for long account lists (if >20 accounts)
- Lazy load charts (create only when tab visible)

**Data**:
- Limit chart data points (max 100 points per series)
- Aggregate data for long time horizons
- Cache API responses (5 minutes)

### 3.4 Error Handling

**API Failures**:
- Show toast notification: "Unable to fetch Bitcoin price. Using cached value."
- Display last successful fetch time
- Provide "Retry" button
- Fall back to manual entry

**Invalid Input**:
- Inline validation messages
- Prevent submission until valid
- Helpful suggestions (e.g., "Retirement age must be greater than current age")

**Edge Cases**:
- Handle negative balances gracefully
- Prevent division by zero in growth calculations
- Warn if contribution allocation doesn't sum to 100%
- Cap max age at 120, min at 0

### 3.5 Accessibility

**Keyboard Navigation**:
- All interactive elements focusable
- Tab order follows logical flow
- Escape key closes modals
- Enter/Space for button actions

**Screen Readers**:
- ARIA labels for charts and controls
- Live regions for dynamic updates
- Descriptive alt text for icons

**Visual**:
- High contrast mode support
- Minimum font size 14px
- Scalable UI (respects browser zoom)
- Color-blind friendly palette (distinct colors)

---

## 4. User Workflow Examples

### Example 1: First-Time User

1. **Opens App**: Sees clean interface with sample/empty portfolio
2. **Enters Personal Info**: Age 49, Retirement 52, Expenses $150k
3. **Clicks "Add Account"**: Creates "Main 401k"
   - Type: Traditional 401k
   - Balance: $500,000
   - Contribution: $23,000/year for 3 years
   - Allocation: 30% MSTR, 30% FBTC, 20% STRC, 10% SATA, 10% Stocks
4. **Clicks "Add Account"** again: Creates "Bitcoin Stack"
   - Type: Self-Custody Bitcoin
   - Balance: 2.5 BTC
   - Contribution: 0.1 BTC/year for 5 years
5. **Views Dashboard**: Sees beautiful stacked chart showing growth
6. **Checks Analysis**:
   - Forever Number: $2.4M
   - Projected at Retirement: $4.5M
   - Status: ✅ On Track (surplus of $2.1M)
7. **Adjusts Power Law**: Changes Years 1-5 from 32% to 28% (more conservative)
8. **Sees Updated Projections**: Now $3.8M projected
9. **Exports PDF**: Downloads report for records
10. **Closes App**: Portfolio auto-saved to LocalStorage

### Example 2: Returning User

1. **Opens App**: Portfolio automatically loaded
2. **Clicks Refresh Prices**: Fetches latest BTC ($105,000 ↑), MSTR ($475 ↑)
3. **Sees Updated Projections**: Portfolio now worth more due to price increases
4. **Adds Roth IRA Account**:
   - Balance: $100,000
   - Contribution: $7,000/year for 3 years
   - Allocation: 50% FBTC, 50% MSTR
5. **Compares Scenarios**: Clicks "Duplicate" on Main 401k, adjusts allocation in copy
6. **Views Allocation Chart**: Scrubs timeline to see how Bitcoin dominance changes
7. **Checks Tax Impact**: Views withdrawal strategy, sees Roth accessed first
8. **Satisfied**: Closes app, changes saved

### Example 3: Conservative Planner

1. **Sets Lower Power Law CAGRs**: All periods reduced by 10-15%
2. **Adds More Generic Stocks**: 40% stocks in taxable account
3. **Views Sustainability Heatmap**: Sees portfolio lasts 50+ years at 4% withdrawal
4. **Adjusts Annual Expenses**: Increases to $200k to be safe
5. **Forever Number Increases**: Now needs $5M (vs $2.4M before)
6. **Checks Gap Analysis**: Sees needs more in taxable accounts
7. **Increases Contributions**: Bumps 401k contribution to $30k/year
8. **Rechecks Status**: Now on track again

---

## 5. Key Differentiators

### Why This App is Special

1. **Beautiful Design**: Not a boring spreadsheet—gorgeous, modern UI
2. **Bitcoin-Focused**: Purpose-built for Bitcoin-centric portfolios
3. **Power Law Modeling**: Based on proven Bitcoin growth model
4. **Tax-Smart**: Models Roth vs Traditional, early withdrawal penalties
5. **Flexible**: Multiple accounts, custom allocations, configurable growth rates
6. **Visual**: 6+ different chart types, all interactive and animated
7. **Private**: All data stays in browser, no cloud storage or tracking
8. **Free**: No subscription, no ads, no data collection

### What It Doesn't Do (Yet)

- No dollar-cost averaging simulation
- No Monte Carlo / stochastic modeling
- No Social Security integration
- No detailed tax calculations (just penalty modeling)
- No Roth conversion ladder optimization
- No backtesting with historical data
- No cloud sync across devices

---

## 6. Testing Checklist

Before launch, verify:

- [ ] All calculations match Excel model (within rounding)
- [ ] Charts render correctly in both light and dark themes
- [ ] Mobile responsive (test on iPhone SE, iPad, Desktop)
- [ ] LocalStorage save/load works across browser restarts
- [ ] API fetching works, fallback to cached values works
- [ ] All animations smooth at 60fps (no jank)
- [ ] No console errors or warnings
- [ ] Input validation prevents invalid states
- [ ] Accessibility: keyboard navigation works, screen reader friendly
- [ ] Cross-browser: Works in Chrome, Firefox, Safari, Edge
- [ ] Edge cases: Very young age, very old retirement age, extreme values
- [ ] Export to PDF generates readable report
- [ ] Theme toggle persists across sessions

---

## 7. Questions for Final Confirmation

Please review and confirm:

1. **MSTR Leverage Ratio**: Is 1.8x reasonable? Should I calculate it dynamically based on actual MSTR NAV premium?

2. **STRC/SATA Dividend Reinvestment**: Confirm the "extra credit" feature—allow splitting dividends between reinvesting in position vs buying more MSTR?

3. **Withdrawal Rate Dynamic Logic**: The spreadsheet uses Bitcoin allocation % to determine withdrawal rate. Confirm this is the desired default behavior?

4. **Gap Number Toggle**: If taxable accounts don't cover the gap, should the app:
   - Always show the shortfall, OR
   - Offer a toggle "Allow early access (with 10% penalty)" to automatically use retirement accounts?

5. **Contribution Duration**: Should contributions automatically stop at retirement age, or strictly follow the user-specified duration?

6. **Chart Data Points**: For long time horizons (40+ years), should I limit chart points to ~100 for performance, or show all years?

7. **Export Feature**: PDF export priority—is this must-have for v1, or can it wait for v2?

---

## Summary

This application will be a powerful, beautiful tool for modeling retirement portfolios with a Bitcoin focus. It combines:

- **Sophisticated modeling** (power law, leverage ratios, tax treatment)
- **Gorgeous UI** (animations, gradients, modern design)
- **Practical features** (multiple accounts, contributions, allocations)
- **Visual insights** (6+ chart types, interactive exploration)
- **Privacy-first** (local storage, no cloud)

The result: An empowering tool that makes complex retirement planning accessible and even enjoyable.

**Ready to build?** Let me know if you'd like any changes to this design!
