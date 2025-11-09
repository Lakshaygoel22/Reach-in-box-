# ReachInbox Backend

A Node.js/TypeScript backend for the ReachInbox Onebox Email Aggregator that syncs emails via IMAP, categorizes them using AI, and provides search capabilities.

## Prerequisites

Before starting, ensure you have the following installed:

1. **Node.js** (v18 or higher) - [Download](https://nodejs.org/)
2. **Docker Desktop** - [Download](https://www.docker.com/products/docker-desktop/)
3. **VS Code** (or any code editor)

## Getting Started in VS Code

### Step 1: Open the Project

1. Open VS Code
2. Go to `File > Open Folder...`
3. Navigate to and select the `reachinbox-backend` folder

### Step 2: Install Dependencies

Open the integrated terminal in VS Code (`Ctrl + `` ` `` or `View > Terminal`) and run:

```bash
npm install
```

This will install all required dependencies listed in `package.json`.

### Step 3: Start Docker Services

The project requires MongoDB and Elasticsearch, which are configured via Docker Compose.

In the VS Code terminal, run:

```bash
docker-compose up -d
```

This will start:
- **MongoDB** on port `27017`
- **Elasticsearch** on port `9200`

To verify the services are running:
```bash
docker ps
```

You should see `reachinbox-mongodb` and `reachinbox-elasticsearch` containers running.

### Step 4: Configure Environment Variables

Create a `.env` file in the root directory of the project:

```bash
# In VS Code terminal
touch .env
```

Or create it manually in VS Code. Add the following environment variables:

```env
# Server Configuration
PORT=3000

# MongoDB (defaults to localhost:27017/reachinbox if not set)
MONGODB_URI=mongodb://localhost:27017/reachinbox

# Elasticsearch (defaults to http://localhost:9200 if not set)
ELASTICSEARCH_NODE=http://localhost:9200

# OpenAI API Key (required for email categorization)
OPENAI_API_KEY=your_openai_api_key_here

# Email Accounts Configuration (JSON array)
EMAIL_ACCOUNTS=[{"host":"imap.gmail.com","port":993,"user":"your-email@gmail.com","password":"your-app-password","account":"gmail"}]

# Slack Integration (optional)
SLACK_BOT_TOKEN=xoxb-your-slack-bot-token
SLACK_CHANNEL=#leads

# Webhook URL (optional)
WEBHOOK_URL=https://your-webhook-url.com
```

**Important Notes:**
- For Gmail, you'll need to use an [App Password](https://support.google.com/accounts/answer/185833) instead of your regular password
- The `EMAIL_ACCOUNTS` should be a JSON array with IMAP connection details
- `OPENAI_API_KEY` is required for the categorization feature to work

### Step 5: Start the Development Server

In the VS Code terminal, run:

```bash
npm run dev
```

This will:
- Start the Express server on `http://localhost:3000` (or the port specified in `.env`)
- Connect to MongoDB
- Initialize Elasticsearch
- Start IMAP email syncing (if email accounts are configured)
- Watch for file changes and auto-restart

You should see output like:
```
✅ Connected to MongoDB
✅ Elasticsearch index created
📧 Starting IMAP sync for 1 account(s)...
🚀 Server running on http://localhost:3000
```

### Step 6: Verify the Server is Running

Open your browser or use curl to test:

```bash
# Health check
curl http://localhost:3000/health

# API info
curl http://localhost:3000/
```

## Available Scripts

- `npm run dev` - Start development server with hot-reload
- `npm run build` - Build TypeScript to JavaScript
- `npm start` - Start production server (requires build first)
- `npm run lint` - Run ESLint

## API Endpoints

Once the server is running, you can access:

- `GET /` - API information
- `GET /health` - Health check
- `GET /api/emails` - Get emails
- `GET /api/search?q=query` - Search emails
- `POST /api/categorize` - Categorize an email

## VS Code Tips

1. **Install Recommended Extensions:**
   - ESLint
   - Prettier
   - TypeScript and JavaScript Language Features (built-in)

2. **Debugging:**
   - Press `F5` to start debugging
   - Create a `.vscode/launch.json` file for custom debug configurations

3. **Terminal:**
   - Use `Ctrl + Shift + `` ` `` to create a new terminal
   - Split terminals to run multiple commands simultaneously (e.g., Docker and npm)

## Alternative Setup (Without Docker)

If you cannot use Docker, you can install MongoDB and Elasticsearch directly on Windows:

### Option 1: MongoDB Community Server

1. **Download MongoDB:**
   - Visit [MongoDB Download Center](https://www.mongodb.com/try/download/community)
   - Download the Windows installer
   - Install with default settings

2. **Start MongoDB:**
   - MongoDB usually runs as a Windows service automatically
   - Or run manually: `mongod --dbpath "C:\data\db"`

3. **Update `.env`:**
   ```env
   MONGODB_URI=mongodb://localhost:27017/reachinbox
   ```

### Option 2: MongoDB Atlas (Cloud - Free Tier)

1. Sign up at [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free cluster
3. Get your connection string
4. Update `.env`:
   ```env
   MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/reachinbox
   ```

### Elasticsearch Setup

1. **Download Elasticsearch:**
   - Visit [Elasticsearch Download](https://www.elastic.co/downloads/elasticsearch)
   - Download the Windows ZIP file
   - Extract to a folder (e.g., `C:\elasticsearch`)

2. **Start Elasticsearch:**
   ```powershell
   cd C:\elasticsearch\bin
   .\elasticsearch.bat
   ```

3. **Update `.env`:**
   ```env
   ELASTICSEARCH_NODE=http://localhost:9200
   ```

**Note:** Elasticsearch requires Java. Install [Java JDK](https://adoptium.net/) if needed.

## Troubleshooting

### Port Already in Use
If port 3000 is already in use, change the `PORT` in your `.env` file.

### Docker Services Not Starting

**Error: "docker-compose: command not found" or "docker: command not found"**

This means Docker is not installed or not in your PATH. Solutions:

1. **Install Docker Desktop:**
   - Download from [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/)
   - Install and restart your computer
   - Make sure Docker Desktop is **running** (you should see the Docker icon in your system tray)
   - Restart VS Code after installation

2. **If Docker Desktop is installed but not recognized:**
   - Make sure Docker Desktop is **running** (check system tray)
   - Close and reopen VS Code terminal
   - Try restarting your computer
   - Check if Docker is in PATH: Open PowerShell and run `$env:PATH -split ';' | Select-String docker`

3. **Try using `docker compose` instead of `docker-compose`:**
   ```bash
   docker compose up -d
   ```
   (Note: Docker Compose V2 uses `docker compose` without the hyphen)

4. **Alternative: Install MongoDB and Elasticsearch locally:**
   - If you can't use Docker, you can install MongoDB and Elasticsearch directly on Windows
   - Update your `.env` file to point to your local installations
   - See "Alternative Setup (Without Docker)" section below

**Other Docker Errors:**
- **Ports already in use:** Check if ports 27017 or 9200 are already in use
  ```powershell
  netstat -ano | findstr :27017
  netstat -ano | findstr :9200
  ```
- **Permission errors:** Run VS Code as Administrator or ensure Docker Desktop has proper permissions
- **Container conflicts:** Try `docker-compose down` then `docker-compose up -d`
- **Out of memory:** Elasticsearch needs at least 512MB RAM. Close other applications or reduce `ES_JAVA_OPTS` in docker-compose.yml

### MongoDB Connection Error
- Verify MongoDB container is running: `docker ps`
- Check MongoDB logs: `docker logs reachinbox-mongodb`

### Elasticsearch Connection Error
- Verify Elasticsearch container is running: `docker ps`
- Check Elasticsearch logs: `docker logs reachinbox-elasticsearch`
- Wait a few seconds after starting Docker services for Elasticsearch to fully initialize

### Email Sync Not Working
- Verify your IMAP credentials are correct
- For Gmail, ensure you're using an App Password
- Check the console logs for IMAP connection errors

## Stopping the Project

1. Stop the development server: Press `Ctrl + C` in the terminal
2. Stop Docker services: `docker-compose down`
3. To remove volumes (data): `docker-compose down -v`

## Project Structure

```
reachinbox-backend/
├── src/
│   ├── ai/           # AI categorization logic
│   ├── db/           # MongoDB connection and models
│   ├── elasticsearch/# Elasticsearch client and indexing
│   ├── imap/         # IMAP email syncing
│   ├── integrations/ # Slack and webhook integrations
│   ├── routes/       # Express API routes
│   ├── types/        # TypeScript type definitions
│   └── index.ts      # Main entry point
├── docker-compose.yml
├── package.json
├── tsconfig.json
└── README.md
```

## Security Best Practices

### Environment Variables
- Never commit the `.env` file to version control
- Use different environment files for development/staging/production
- Use strong, unique passwords and API keys
- For Gmail integration, always use App-specific passwords
- Rotate API keys and tokens periodically

### Production Deployment
- Always use HTTPS in production
- Set appropriate CORS policies
- Enable rate limiting
- Use secure MongoDB and Elasticsearch connections (SSL/TLS)
- Set proper file permissions
- Use secrets management service in cloud deployments
- Enable audit logging
- Implement proper session management
- Regular security updates and patches

### Handling Sensitive Data
- Encrypt sensitive data at rest
- Use secure protocols for data transmission (HTTPS, SSL/TLS)
- Implement proper access controls
- Regular security audits
- Follow data protection regulations (GDPR, CCPA, etc.)
- Implement proper data retention policies

## License

ISC

