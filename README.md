# PulseScope — Deriv Tick Analytics

A deriv analysis and prediction tool. Made by Ajay.

## 🚀 Features

- **Demo Stream**: Simulated tick data for testing and learning
- **Public Ticks**: Live Deriv public market data (no authentication required)
- **Authenticated Mode**: Connect your Deriv account using Personal Access Token (PAT) for real-time account data and trading analytics

## 📋 Quick Start

### 1. Demo Mode (No Setup Required)
- Open the application
- Select "Demo stream" from the Data source dropdown
- Click "Start scan" to begin analysis

### 2. Public Ticks Mode (No Authentication)
- Select "Deriv public ticks" from the Data source dropdown
- Choose a symbol (Volatility 100, 50, etc.)
- Adjust threshold and lookback parameters
- Click "Start scan"

### 3. Authenticated Mode (Connect Your Deriv Account)

#### Prerequisites:
- A Deriv account (real or demo at [deriv.com](https://deriv.com))
- Personal Access Token (PAT) with appropriate scopes

#### Step-by-Step Setup:

**1. Generate your Personal Access Token:**
   - Log in to your Deriv account at [app.deriv.com](https://app.deriv.com/)
   - Go to **Account Settings → Security & Safety → API Token** (or visit [app.deriv.com/account/api-token/](https://app.deriv.com/account/api-token/))
   - Click "Create new token"
   - Select required scopes:
     - ✓ `read` (read account info, balances, statements)
     - ✓ `trade` (place and manage trades)
     - ✓ `payments` (access payment info)
   - Click "Create"
   - **Copy the generated token** (it won't be shown again)

**2. Connect in PulseScope:**
   - Click the **"Connect Deriv"** button in the top-right corner
   - Paste your PAT in the "Personal Access Token" field
   - Select connection type (Authenticated recommended)
   - Click "Connect"
   - Monitor the connection log to verify successful authentication

**3. Use authenticated data:**
   - Once connected, select "Authenticated (requires connection)" from the Data source dropdown
   - The UI will show:
     - ✓ Account login ID
     - ✓ Current balance
     - ✓ Account type (Demo/Real)
   - Click "Start scan" to stream live data from your account

## 🔧 Technical Architecture

### Configuration
- **App ID**: `34lVzR8IROjFvmgQbW8PX` (Pre-configured and registered)
- **WebSocket Endpoint**: `wss://ws.deriv.com/websockets/v3`
- **Public Endpoint**: `wss://ws.binaryws.com/websockets/v3`
- **Auth Method**: OAuth via Personal Access Token

### Connection Flow

```
┌─────────────────────────────────────────────────────────┐
│ User enters PAT → Modal dialog                          │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│ WebSocket Connect with App ID: 34lVzR8IROjFvmgQbW8PX  │
│ Endpoint: wss://ws.deriv.com/websockets/v3             │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────��
│ Send Authorize Message with PAT                         │
│ { "authorize": "USER_PAT_TOKEN" }                       │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│ Deriv API validates token                              │
│ Returns: Account info, balance, loginid, etc.          │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│ Subscribe to Live Tick Data                            │
│ { "ticks": "R_100", "subscribe": 1 }                   │
└────────────────────┬──────────────────────────────────��─┘
                     │
┌────────────────────▼────────────────────────────────────┐
│ Real-time Analysis & Display                           │
│ - Price updates                                        │
│ - RSI, Momentum, Volatility calculations              │
│ - Digit pattern analysis                              │
│ - Trading probability scoring                         │
└─────────────────────────────────────────────────────────┘
```

## 📊 Analytics Features

### Indicators Calculated

| Indicator | Description | Formula |
|-----------|-------------|---------|
| **RSI (14)** | Relative Strength Index | (100 - (100 / (1 + RS))) where RS = avg_gain / avg_loss |
| **Momentum (10)** | Price change over 10-period window | Price[now] - Price[10 bars ago] |
| **Volatility** | Standard deviation of returns | √(Σ(returns²) / n) |
| **Entropy** | Distribution uniformity | -Σ(p × log₂(p)) for each digit |
| **Digit Streak** | Consecutive same last-digits | Count of consecutive occurrences |

### Analysis Components

1. **Live Price Chart**: Real-time price visualization with grid
2. **Digit Distribution**: Last-digit frequency analysis (0-9)
3. **Transition Matrix**: Probability matrix P(next digit | current digit)
4. **Composite Score**: Evidence-based bias detection (0-100 scale)
5. **Trading Signals**:
   - Rise/Fall frequency
   - Even/Odd distribution
   - Over/Under threshold
   - Digit transition probability

## 🔐 Security & Data Handling

⚠️ **Important Security Notes:**

- **PAT Management**: Personal Access Tokens are sensitive credentials
  - Never share your PAT publicly or in code
  - Tokens are entered only in the modal dialog
  - Tokens are NOT stored, logged, or persisted
  - Each connection uses fresh token validation

- **Data Privacy**: 
  - All data remains in your browser
  - No server-side storage of account info
  - Token sent directly to Deriv's official API
  - WebSocket connection is encrypted (wss://)

- **Token Safety**:
  - If you suspect compromise, regenerate token immediately in Deriv settings
  - Use separate tokens for different apps if needed
  - Tokens can be revoked at any time

## 🎯 Data Source Options

| Source | Auth Required | Update Speed | Latency | Best For |
|--------|---------------|--------------|---------|----------|
| **Demo** | No | 700ms (simulated) | Instant | Testing, Learning |
| **Public Ticks** | No | Real-time | <100ms | Market research, Strategy backtesting |
| **Authenticated** | Yes (PAT) | Real-time | <100ms | Live trading analysis, Account data |

## 📈 Symbols Available

- `1HZ100V` - Volatility 100 (1 second updates)
- `R_100` - Volatility 100
- `1HZ10V` - Volatility 10 (1 second updates)
- `R_50` - Volatility 50

## 🧪 Demo Workflow

### Testing Before Live Trading:

1. Start with **Demo Stream** mode
2. Experiment with lookback windows and thresholds
3. Switch to **Public Ticks** to see real market data
4. Use a **Demo Deriv Account** with authenticated connection
5. Validate signals against historical data
6. Only then consider real account trading

## ⚙️ Configuration

### Modal Fields:

- **Personal Access Token**: Your unique auth token (masked for security)
- **Connection Type**: 
  - Authenticated (Real/Demo Account) - Full account access
  - Public Ticks Only - Market data without authentication
- **Connection Log**: Real-time feedback on connection status

### Control Parameters:

- **Data source**: Choose input stream (demo, public, authenticated)
- **Symbol**: Select market index to analyze
- **Lookback ticks**: Historical window size (50-5000 ticks)
- **Threshold**: Over/Under cutoff value (0-9)

## 🛠️ Troubleshooting

### "Connection Failed" Error

**Solution 1: Verify PAT Validity**
- Check that token hasn't expired
- Regenerate a new token from [app.deriv.com/account/api-token/](https://app.deriv.com/account/api-token/)
- Ensure scopes include: `read`, `trade`, `payments`

**Solution 2: Check Internet Connection**
- Verify stable internet connection
- Try Public Ticks mode first to test connectivity
- Check browser console (F12) for detailed error logs

**Solution 3: App ID Issue**
- App ID `34lVzR8IROjFvmgQbW8PX` is pre-configured
- If issues persist, verify in browser console that correct App ID is being used

### "Collecting sample..." Stuck State

- Ensure Symbol is selected
- Try smaller lookback value (e.g., 50 ticks)
- Check if data stream is receiving ticks in browser console
- Switch to Demo mode to verify analytics engine works

## 📚 Learning Resources

### Official Documentation
- [Deriv API Docs](https://api.deriv.com/docs/)
- [Deriv App Registration](https://app.deriv.com/account/apps/)
- [Personal Access Token Setup](https://app.deriv.com/account/api-token/)
- [WebSocket API Explorer](https://api.deriv.com/api-explorer/)

### API Concepts Used
- **WebSocket Protocol**: Real-time bidirectional communication
- **OAuth Authorization**: Token-based authentication
- **Tick Subscription**: Live market data streaming
- **Volatility Indices**: Synthetic continuous markets

## 📝 Disclaimer

⚠️ **RESEARCH & ANALYSIS ONLY**

This tool is for:
- Educational purposes
- Strategy development and testing
- Market analysis and research

This tool is NOT for:
- Guaranteed trade execution
- Investment advice
- Automatic trading without review

**Important**: 
- Tick indicators and historical transitions do not guarantee trading outcomes
- Past performance does not predict future results
- Always test strategies on demo data first
- Never risk more than you can afford to lose
- Use proper risk management in live trading

---

**Made with ❤️ by Ajay**  
For support or feature requests, please refer to the [GitHub repository](https://github.com/ajayricos8-rgb/mrcash)
