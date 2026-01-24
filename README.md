# Bitcoin Retirement Planner 🚀

A comprehensive retirement planning tool designed for Bitcoin and crypto investors who want to model their path to financial independence with Bitcoin-focused portfolios.

## 🎯 Quick Start

1. **Open the app** - Just open `bitcoin-retirement-planner.html` in your web browser
2. **Set your basics** - Enter your current age, retirement age, and annual expenses
3. **Add accounts** - Click "+ Add Account" to add your 401(k)s, IRAs, brokerage accounts, or Bitcoin holdings
4. **Allocate assets** - For each account, set how much you'll hold in Bitcoin, MSTR, stocks, etc.
5. **Review your plan** - Check the Analysis Dashboard to see if you're on track!

That's it! Your portfolio is automatically projected forward using Bitcoin power law growth models.

---

## 📊 What This Tool Does

The Bitcoin Retirement Planner helps you answer critical questions:

- **Can I retire early?** See if your taxable accounts can bridge the gap to age 59.5
- **Will my money last?** Project your portfolio through retirement with realistic withdrawals
- **Am I on track?** Compare your projected wealth against your "Forever Number"
- **What's my allocation?** Visualize how your portfolio is distributed across accounts and assets

### Key Features

✅ **Multiple account types** - Traditional/Roth 401(k)s, IRAs, taxable brokerage, self-custody Bitcoin  
✅ **Bitcoin power law modeling** - Uses realistic CAGR projections that decrease over time  
✅ **Tax-aware withdrawals** - Automatically optimizes which accounts to withdraw from  
✅ **Dividend-first strategy** - Uses STRC/SATA dividends before touching principal  
✅ **Gap analysis** - Shows if you have enough in taxable accounts for early retirement  
✅ **Visual charts** - Portfolio growth, income breakdown, and asset allocation pie charts  
✅ **Data persistence** - Your plan auto-saves to browser localStorage  

---

## 📖 How to Use Each Section

### Personal Information

Set your foundational retirement parameters:

- **Current Age** - Your age today
- **Retirement Age** - When you plan to stop working (can be before 59.5!)
- **Life Expectancy** - How long to plan for (default: 100)
- **Annual Expenses** - Your yearly spending in today's dollars
  - 💡 **Important:** Include healthcare costs, insurance premiums, and out-of-pocket medical expenses
- **Inflation Rate** - Expected annual inflation (default: 3%)
- **Tax Rates** - Ordinary income and capital gains tax rates

### Bitcoin Power Law Model

Customize Bitcoin's projected growth over time:

- The model uses declining CAGR periods (32% → 27% → 20% → ... → 9%)
- Click on periods to edit individual growth rates
- Use presets for conservative, moderate, or aggressive scenarios
- Fetch current Bitcoin price from CoinGecko API

**Why power law?** Bitcoin's growth historically follows a power law curve - explosive early growth that moderates over time.

### Asset Parameters

Fine-tune assumptions for each asset:

- **MSTR Leverage Ratio** - How much MSTR amplifies Bitcoin returns (default: 1.8x)
- **₿ ETF Expense Ratio** - Annual fee for Bitcoin ETFs (default: 0.25%)
- **STRC Dividend Yield** - Strategy/STRC annual dividend (default: 11%)
- **SATA Dividend Yield** - Strive/SATA annual dividend (default: 12.25%)
- **Generic Stocks CAGR** - Expected return for traditional stocks (default: 10%)
- **Cash/Money Market** - Yield on cash holdings (default: 3%)

### Accounts

This is where you build your actual portfolio:

**Adding an Account:**
1. Click "+ Add Account"
2. Choose account type (Roth IRA, Traditional 401k, Taxable Brokerage, Self-Custody BTC)
3. Enter current balance
4. Set annual contribution amount and years
5. Allocate percentages across assets (must total 100%)

**Account Types:**
- **Taxable Brokerage** - Standard brokerage account (capital gains tax on withdrawals)
- **Traditional 401(k)/IRA** - Tax-deferred (ordinary income tax on withdrawals, penalties before 59.5)
- **Roth 401(k)/IRA** - Tax-free (no tax on qualified withdrawals after 59.5)
- **Self-Custody Bitcoin** - Bitcoin you hold in your own wallet (treated like taxable)

**Tips:**
- Enable/disable accounts with the eye icon (👁️ / 🚫) to test scenarios
- Use realistic contribution amounts you can actually sustain
- Front-load higher-return assets in tax-advantaged accounts

### Analysis Dashboard

Your retirement plan at a glance:

#### Overview Tab

Three view modes:
- **By Account** - See each account's growth trajectory
- **By Asset Type** - View total exposure to Bitcoin, MSTR, stocks, etc.
- **By Tax Treatment** - Compare taxable vs. tax-deferred vs. tax-free buckets

**Visual indicators:**
- Purple line = Your retirement age
- Orange line = Age 59.5 (when retirement accounts become penalty-free)
- Red shaded area = Gap period shortfall (if any)

#### Income Tab

Shows your inflation-adjusted spending by year with tax breakdown:
- **Cash (Tax-Free)** - Purple
- **Roth Withdrawals (Tax-Free)** - Green
- **Capital Gains** - Orange (15% tax)
- **Ordinary Income** - Red (25% tax)

#### Allocation Tab

Two pie charts:
- **By Account** - Current balance distribution
- **By Asset Type** - Current holdings across all accounts

### Retirement Analysis

Four critical metrics:

**1. Forever Number**
- Portfolio value needed at age 59.5 to retire forever
- Uses dynamic safe withdrawal rate (4-8%) based on Bitcoin allocation
- Higher Bitcoin allocation = higher sustainable withdrawal rate

**2. Projected at Age 59.5**
- What your portfolio will be worth at 59.5
- Accounts for contributions, growth, AND withdrawals (if retiring early)

**3. Gap Number**
- Funds needed in taxable accounts to cover expenses from retirement age → 59.5
- Only matters if retiring before 59.5 (early retirement)
- Calculated using present value of annuity formula

**4. Gap Available**
- Actual funds in taxable + self-custody accounts at retirement age
- These are the only accounts accessible penalty-free before 59.5

**5. Status**
- ✅ **On Track** - Strong surplus (>20%)
- ⚠️ **Review Recommended** - Modest surplus
- ❌ **Gap Shortfall** - Insufficient taxable funds for early retirement
- ❌ **Forever Shortfall** - Insufficient funds at 59.5

---

## 🧠 Understanding the Withdrawal Strategy

When you retire, the planner withdraws funds in this tax-optimized order:

### Asset Order (Within Each Account)
1. **STRC/SATA dividends** - Current year's dividend income
2. **Cash** - Tax-free principal
3. **Stocks** - Traditional equities
4. **STRC/SATA principal** - After dividends exhausted
5. **MSTR/₿ ETF/Bitcoin** - Preserved longest for maximum growth

### Account Order
1. **Taxable accounts** - Until age 59.5 (avoid penalties)
2. **Self-custody Bitcoin** - Until age 59.5
3. **Roth accounts** - After 59.5 (tax-free)
4. **Traditional accounts** - After 59.5 (last resort due to taxes)

**Why this order?** Maximize Bitcoin exposure longest, use dividend income first, avoid penalties, minimize taxes.

---

## 💡 Tips & Best Practices

### General Strategy
- **Front-load Bitcoin** in tax-advantaged accounts where it can grow tax-free
- **Keep 1-2 years cash** in taxable accounts for flexibility
- **Max out Roth accounts** for tax-free growth on high-return assets
- **Plan for healthcare** - ACA subsidies, Medicare gaps, long-term care

### Early Retirement (Before 59.5)
- **Build taxable accounts** - You need enough to cover the "gap" period
- **Consider dividend assets** - STRC/SATA provide income without depleting principal
- **72(t) SEPP option** - Not modeled here, but allows penalty-free IRA withdrawals
- **Roth conversion ladder** - Convert Traditional → Roth in low-income years

### Model Accuracy
- **Be conservative** - Use lower growth rates if uncertain
- **Test scenarios** - Toggle accounts on/off to see different paths
- **Account for one-time expenses** - Large purchases, home repairs, travel
- **Review annually** - Update with actual performance and life changes

### Common Pitfalls
- ❌ Underestimating healthcare costs (can be $10-20k+/year before Medicare)
- ❌ Ignoring sequence of returns risk (market crashes in early retirement)
- ❌ Overallocating to Bitcoin (even if bullish, keep some diversification)
- ❌ Forgetting about RMDs (Required Minimum Distributions at 73)

---

## 🔒 Privacy & Data

- **Everything is local** - No data is sent to any server
- **Auto-save** - Your plan saves automatically to browser localStorage
- **Export/Import** - Use Save/Load buttons to backup your plan
- **Privacy first** - Only CoinGecko API is called (for Bitcoin price), no tracking

---

## 📱 Technical Requirements

- **Modern web browser** - Chrome, Firefox, Safari, Edge (2020+)
- **JavaScript enabled** - Required for calculations and charts
- **localStorage enabled** - Required for saving your plan
- **No installation needed** - Just open the HTML file
- **Works offline** - After initial load (except Bitcoin price fetching)

---

## ❓ FAQ

**Q: What if I retire exactly at 59.5?**  
A: No gap period! You can access all accounts immediately, no tax penalties.

**Q: Can I model a Roth conversion ladder?**  
A: Not directly - this tool assumes accounts stay in their original type.

**Q: What about Social Security?**  
A: Not included. Either (a) reduce your expenses input by expected SS, or (b) treat SS as bonus.

**Q: Can I use this for non-Bitcoin portfolios?**  
A: Yes! Set Bitcoin/MSTR/₿ ETF allocations to 0% and use Stocks + Cash only.

**Q: What's the "Forever Number ×12.5" mean?**  
A: If your Bitcoin allocation is 80%+, you can safely withdraw 8% per year (1/0.08 = 12.5x multiplier).

**Q: Why does my portfolio value drop suddenly?**  
A: That's withdrawals! You're spending money in retirement. Check the Income chart to see annual spending.

**Q: The gap shortfall shading seems off by 0.5 years?**  
A: Data points are at 0.5-year intervals. The tool shades conservatively (starts 1 year early).

**Q: Can I export to Excel/CSV?**  
A: Not currently - but you can screenshot charts or use browser devtools to access raw data.

---

## 🎓 Key Concepts Explained

### The Gap Period
If you retire before 59.5, you face a "gap period" where retirement accounts have 10% early withdrawal penalties. You need sufficient **taxable** or **self-custody Bitcoin** holdings to bridge this gap.

**Example:**
- Retire at 55
- Age 59.5 in 4.5 years
- Need ~$675k in taxable accounts (assuming $150k/year expenses, 3% inflation, 10% returns)

### Safe Withdrawal Rate (SWR)
Traditional retirement planning uses the 4% rule - you can withdraw 4% of your portfolio annually forever. This tool uses **dynamic SWR** based on Bitcoin allocation:

- 80%+ Bitcoin → 8% SWR (aggressive)
- 60-79% Bitcoin → 6.25% SWR
- 40-59% Bitcoin → 5% SWR  
- <40% Bitcoin → 4% SWR (traditional)

**Why higher for Bitcoin?** Bitcoin's historical growth exceeds traditional assets, and many Bitcoin holders believe this will continue (though past performance doesn't guarantee future results).

### Tax Treatment Hierarchy
1. **Tax-Free (Roth)** - Best. No taxes ever on qualified withdrawals.
2. **Taxable (Brokerage)** - Good. Only capital gains tax (15%), cash principal is tax-free.
3. **Tax-Deferred (Traditional)** - Last resort. Full ordinary income tax (25%+) on withdrawals.

### Power Law vs. Exponential Growth
- **Exponential:** Same % growth forever (unrealistic for Bitcoin)
- **Power Law:** Declining % growth over time (Bitcoin's historical pattern)

The tool uses power law by default, which is more conservative and realistic for long-term planning.

---

## 🚨 Important Disclaimers

⚠️ **This tool is for educational and planning purposes only. It is not financial advice.**

- **Not a guarantee** - Markets are unpredictable. Past performance ≠ future results.
- **Simplified model** - Real life has taxes, fees, and complexity this doesn't capture
- **Consult professionals** - Work with a tax advisor and financial planner for your specific situation
- **Bitcoin volatility** - Bitcoin can be extremely volatile. Don't bet your retirement on any single asset.
- **No warranty** - This software is provided as-is with no guarantees of accuracy

**Use at your own risk. You are responsible for your own financial decisions.**

---

## 🛠️ Tips for Getting the Most Out of This Tool

1. **Start conservative** - Better to be pleasantly surprised than fall short
2. **Model multiple scenarios** - Bull case, base case, bear case
3. **Update regularly** - Review and adjust at least annually
4. **Stress test** - What if Bitcoin only does 10% CAGR? What if healthcare doubles?
5. **Don't optimize to zero** - Leave buffers for unexpected expenses
6. **Consider professional help** - Especially for taxes, estate planning, and insurance

---

## 📈 Version History

**v1.0** - Initial release
- Bitcoin power law modeling with 7 periods
- Multiple account types with full tax treatment
- Dividend-first withdrawal strategy
- Gap analysis for early retirement
- Three chart views (by account, asset, tax treatment)
- Comprehensive help tooltips
- Gap shortfall visualization
- Auto-save to localStorage

---

## 💬 Feedback & Support

This is an open-source educational tool. While there's no formal support:

- **Found a bug?** Document it with screenshots and steps to reproduce
- **Have a suggestion?** Describe the feature and why it would be useful
- **Want to contribute?** The code is open and available for enhancement

Remember: This tool is most valuable when you understand your own financial situation deeply. Use it as a starting point for conversations with financial professionals, not as a replacement for personalized advice.

---

**Happy planning! May your Bitcoin moon and your retirement be secure. 🚀🌕**
