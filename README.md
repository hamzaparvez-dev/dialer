# Lightning Dialer - Twilio Cold Call Dialer

A Sinatra-based web application for making cold calls using Twilio with Automatic Call Distribution (ACD) capabilities. Features include:

- **Softphone Interface**: Web-based phone interface for agents
- **Click-to-Dial**: Make outbound calls directly from the browser
- **ACD (Automatic Call Distribution)**: Routes incoming calls to available agents
- **Queue Management**: Handles call queues when no agents are available
- **Real-time Updates**: WebSocket support for live agent and queue status
- **Call Management**: Hold, mute, voicemail drop, and more

## Prerequisites

- Ruby 2.7+ (or 3.x)
- Twilio Account with:
  - Account SID
  - Auth Token
  - TwiML App ID
  - A Twilio phone number (for caller ID)
- **No database required!** Uses in-memory storage for simplicity

## Quick Deploy to Render

### Option 1: Using render.yaml (Recommended)

1. **Fork/Clone this repository**

2. **Connect to Render**:
   - Go to [Render Dashboard](https://dashboard.render.com)
   - Click "New +" → "Blueprint"
   - Connect your GitHub repository
   - Render will automatically detect `render.yaml`

3. **Configure Environment Variables**:
   After the services are created, set these environment variables in the Render dashboard:
   - `twilio_account_sid`: Your Twilio Account SID
   - `twilio_account_token`: Your Twilio Auth Token
   - `twilio_app_id`: Your TwiML App ID (create at Twilio Console → Dev Tools → TwiML Apps)
   - `twilio_caller_id`: Your Twilio phone number (e.g., +1234567890)
   - `twilio_dqueue_url`: Your Render service URL + `/voice` (e.g., `https://your-service.onrender.com/voice`)
   - `twilio_queue_name`: Queue name (default: "CustomerService")
   - `anycallerid`: Set to "inline" if agents can set their own caller ID (default: "none")

4. **Deploy**: Render will automatically deploy your application

### Option 2: Manual Setup

1. **Create a Web Service**:
   - Go to Render Dashboard → "New +" → "Web Service"
   - Connect your GitHub repository
   - Set:
     - **Name**: `lightning-dialer`
     - **Environment**: `Ruby`
     - **Build Command**: `bundle install`
     - **Start Command**: `bundle exec puma -C - -e production -p $PORT`

2. **Set Environment Variables** (same as Option 1)

**Note:** No database setup needed! The app uses in-memory storage.

## Local Development

1. **Install Dependencies**:
   ```bash
   bundle install
   ```

2. **Set Environment Variables**:
   ```bash
   export twilio_account_sid="your_account_sid"
   export twilio_account_token="your_auth_token"
   export twilio_app_id="your_app_id"
   export twilio_caller_id="+1234567890"
   export twilio_queue_name="CustomerService"
   export twilio_dqueue_url="http://localhost:4567/voice"
   export anycallerid="none"
   ```

3. **Run the Application**:
   ```bash
   bundle exec rackup config.ru
   ```

4. **Access the Application**:
   - Open `http://localhost:4567` in your browser

## Twilio Setup

### 1. Create a TwiML App

1. Go to [Twilio Console](https://console.twilio.com) → Dev Tools → TwiML Apps
2. Create a new TwiML App
3. Set the Voice URL to: `https://your-service.onrender.com/dial`
4. Copy the App SID to `twilio_app_id` environment variable

### 2. Configure Inbound Phone Number

1. Go to Phone Numbers → Manage → Active Numbers
2. Select your Twilio number
3. Set the Voice webhook to: `https://your-service.onrender.com/voice`
4. Set HTTP method to POST

### 3. Queue Setup

The application automatically creates a queue named "CustomerService" (or your configured `twilio_queue_name`) if it doesn't exist.

## Usage

1. **Access the Softphone**: Navigate to `https://your-service.onrender.com`
2. **Agent Login**: The system automatically identifies agents by their browser session
3. **Set Status**: Click "Ready" to receive calls or "Not Ready" to pause
4. **Make Calls**: Enter a phone number and click "Call"
5. **Receive Calls**: Incoming calls are automatically routed to available agents
6. **Call Controls**: Use Hold, Mute, and Hangup buttons during calls

## Features

- **Agent Status Management**: Track agent availability (Ready/Not Ready/On Call)
- **Call Routing**: Automatic distribution of incoming calls to longest-idle agents
- **Queue Management**: Calls queue when no agents are available
- **Real-time Dashboard**: See queue size and available agents count
- **Call Recording**: Calls are recorded (configured in Twilio)
- **WebSocket Support**: Real-time updates without page refresh

## Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `twilio_account_sid` | Twilio Account SID | Yes |
| `twilio_account_token` | Twilio Auth Token | Yes |
| `twilio_app_id` | TwiML App ID | Yes |
| `twilio_caller_id` | Twilio phone number for outbound calls | Yes |
| `twilio_queue_name` | Name of the call queue | No (default: "CustomerService") |
| `twilio_dqueue_url` | URL for dequeueing calls (your service + `/voice`) | Yes |
| `anycallerid` | Enable custom caller ID ("inline" or "none") | No (default: "none") |
| `RACK_ENV` | Environment (production/development) | No |

## Project Structure

```
lightning-dialer/
├── client-acd.rb          # Main Sinatra application
├── config.ru              # Rack configuration
├── Gemfile               # Ruby dependencies
├── render.yaml           # Render deployment configuration
├── views/
│   └── index.erb        # Softphone HTML interface
└── public/
    ├── css/             # Stylesheets
    └── scripts/
        └── softphone.js # Client-side JavaScript
```

## Troubleshooting

### Twilio Connection Issues
- Verify all Twilio credentials are correct
- Check TwiML App Voice URL points to `/dial`
- Ensure phone number webhook points to `/voice`

### WebSocket Issues
- Render supports WebSockets, but ensure your service is using HTTPS
- Check browser console for WebSocket connection errors

## License

This project is open source and available for use.

## Support

For issues and questions:
- Check Twilio documentation: https://www.twilio.com/docs
- Render documentation: https://render.com/docs

