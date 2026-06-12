# Setup & Installation Guide

## Complete Setup Instructions for AI Interview Trainer Agent

---

## Table of Contents

1. [System Requirements](#system-requirements)
2. [Pre-installation Checklist](#pre-installation-checklist)
3. [Step-by-Step Installation](#step-by-step-installation)
4. [IBM watsonx.ai Configuration](#ibm-watsonxai-configuration)
5. [Langflow Setup](#langflow-setup)
6. [Workflow Import & Configuration](#workflow-import--configuration)
7. [Testing & Validation](#testing--validation)
8. [Troubleshooting](#troubleshooting)
9. [Environment Variables](#environment-variables)
10. [Next Steps](#next-steps)

---

## System Requirements

### Minimum Requirements

| Component | Requirement | Notes |
|-----------|-------------|-------|
| **Operating System** | Windows 10+, macOS 10.14+, or Linux | Any modern OS |
| **Python Version** | 3.8 or higher | 3.10+ recommended |
| **RAM** | 8 GB minimum | 16 GB recommended |
| **Disk Space** | 2 GB free | For Python, packages, and data |
| **Internet** | Required | For IBM watsonx.ai access |
| **Browser** | Modern browser (Chrome, Firefox, Safari) | For Langflow UI |

### Recommended Setup

```
Operating System: Ubuntu 20.04+ / Windows 11 / macOS 12+
Python Version: 3.10 or 3.11
Virtual Environment: venv or conda
RAM: 16 GB
GPU: Optional (for faster processing)
```

### Network Requirements

- **Outbound HTTPS** on port 443
- **IBM watsonx.ai endpoint** accessible
- **No proxy** required (unless your organization requires it)

---

## Pre-installation Checklist

Before starting, ensure you have:

- [ ] Python 3.8+ installed on your system
- [ ] `pip` package manager working
- [ ] Git installed (for cloning repository)
- [ ] IBM Cloud account created
- [ ] IBM watsonx.ai instance provisioned
- [ ] API credentials obtained
- [ ] Text editor or IDE ready
- [ ] Terminal/Command Prompt access
- [ ] Stable internet connection
- [ ] 2GB free disk space

---

## Step-by-Step Installation

### Phase 1: Environment Setup

#### Step 1.1: Verify Python Installation

```bash
# Check Python version
python --version
# or
python3 --version

# Expected output: Python 3.8.0 or higher
```

If Python is not installed:
- **Windows**: Download from [python.org](https://www.python.org/downloads/)
- **macOS**: `brew install python3`
- **Linux**: `sudo apt-get install python3 python3-pip`

#### Step 1.2: Create Virtual Environment

**On Windows (Command Prompt):**
```bash
# Create virtual environment
python -m venv interview_trainer_env

# Activate virtual environment
interview_trainer_env\Scripts\activate
```

**On macOS/Linux:**
```bash
# Create virtual environment
python3 -m venv interview_trainer_env

# Activate virtual environment
source interview_trainer_env/bin/activate
```

You should see `(interview_trainer_env)` in your terminal prompt.

#### Step 1.3: Upgrade pip, setuptools, and wheel

```bash
# Upgrade pip
pip install --upgrade pip setuptools wheel

# Verify pip version
pip --version
```

### Phase 2: Clone Repository

#### Step 2.1: Clone the Project

```bash
# Navigate to your desired directory
cd ~/projects  # or your preferred location

# Clone the repository
git clone https://github.com/yourusername/AI-Interview-Trainer-Agent.git

# Navigate into project directory
cd AI-Interview-Trainer-Agent
```

#### Step 2.2: Verify Project Structure

```bash
# List project files
ls -la

# Expected structure:
# ├── README.md
# ├── PROJECT_REPORT.md
# ├── workflow/
# ├── docs/
# ├── screenshots/
# ├── config/
# └── scripts/
```

### Phase 3: Install Dependencies

#### Step 3.1: Install Langflow

```bash
# Install Langflow with all dependencies
pip install langflow

# Or install from GitHub (latest development version)
pip install git+https://github.com/logspace-ai/langflow.git

# Verify installation
langflow --version
```

#### Step 3.2: Install Additional Python Packages

Create or update `requirements.txt`:

```bash
# Create requirements.txt if not exists
cat > requirements.txt << EOF
langflow>=1.0.0
python-dotenv==1.0.0
requests==2.31.0
pydantic==2.0.0
aiohttp==3.9.0
pyyaml==6.0.0
EOF

# Install all requirements
pip install -r requirements.txt
```

#### Step 3.3: Verify Installation

```bash
# Check Langflow installation
python -c "import langflow; print(langflow.__version__)"

# Expected output: 1.0.0 or higher
```

### Phase 4: IBM watsonx.ai Setup

*(See detailed section below)*

### Phase 5: Configure Environment Variables

#### Step 5.1: Create .env File

**On Windows (PowerShell):**
```powershell
New-Item -ItemType File -Name ".env" -Force
```

**On macOS/Linux:**
```bash
touch .env
```

#### Step 5.2: Add Credentials to .env

```bash
# IBM watsonx.ai Configuration
IBM_WATSON_PROJECT_ID="your-project-id-here"
IBM_WATSON_API_KEY="your-api-key-here"
IBM_WATSON_ENDPOINT="https://api.dataplatform.cloud.ibm.com"
IBM_WATSON_REGION="us-south"

# Optional: Langflow Configuration
LANGFLOW_DATABASE_URL="sqlite:///langflow.db"
LANGFLOW_SECRET_KEY="your-secret-key-here"
LANGFLOW_LOG_LEVEL="INFO"

# Application Configuration
APP_NAME="AI Interview Trainer Agent"
APP_VERSION="1.0.0"
DEBUG=False
```

⚠️ **Security Warning**: Never commit `.env` file to version control. Add to `.gitignore`:

```bash
echo ".env" >> .gitignore
echo "*.pyc" >> .gitignore
echo "__pycache__/" >> .gitignore
```

---

## IBM watsonx.ai Configuration

### Step 1: Create IBM Cloud Account

1. Go to [IBM Cloud](https://cloud.ibm.com)
2. Click **Create account**
3. Enter email and password
4. Verify email
5. Log in to IBM Cloud console

### Step 2: Provision watsonx.ai Service

1. **Navigate to Catalog**
   - Click **Catalog** in top navigation
   - Search for "watsonx"

2. **Create Instance**
   - Select **watsonx.ai**
   - Choose region (us-south recommended)
   - Enter instance name
   - Select plan (Lite plan is free, up to certain limits)
   - Click **Create**

3. **Wait for Provisioning**
   - Service will take 2-5 minutes to provision
   - You'll see confirmation when ready

### Step 3: Get API Credentials

1. **Access Service**
   - Click on your watsonx.ai instance
   - Go to **Service credentials** (left sidebar)

2. **Create New Credentials**
   - Click **New credential**
   - Leave role as default (Manager or Reader)
   - Click **Add**

3. **View & Copy Credentials**
   - Click on credential name
   - Copy the entire JSON object
   - Save securely

4. **Extract Required Values**
   ```json
   {
     "apikey": "YOUR_API_KEY",
     "url": "YOUR_SERVICE_URL",
     "auth_url": "YOUR_AUTH_URL",
     ...
   }
   ```

### Step 4: Create Project in watsonx

1. **Access watsonx Studio**
   - From watsonx.ai instance, click **Launch studio**
   - Or go to [watsonx.ibm.com](https://watsonx.ibm.com)

2. **Create New Project**
   - Click **New project** or **+**
   - Choose **Create an empty project**
   - Enter project name: "Interview Trainer"
   - Click **Create**

3. **Get Project Details**
   - Click on project name
   - Go to **Manage** tab
   - Note down:
     - **Project ID**
     - **Project Name**

4. **Add API Key to Project**
   - Go to **Settings**
   - Add your watsonx.ai API key

### Step 5: Get Service Endpoint

For API calls, use:
```
https://api.dataplatform.cloud.ibm.com
```

Different regions have different endpoints:
- US South: `https://api.dataplatform.cloud.ibm.com`
- EU Germany: `https://api.eu-de.dataplatform.cloud.ibm.com`
- London: `https://api.eu-gb.dataplatform.cloud.ibm.com`

---

## Langflow Setup

### Step 1: Initialize Langflow

```bash
# Initialize Langflow (creates necessary files)
langflow init

# This creates:
# - .langflow/ directory
# - Configuration files
# - Database files
```

### Step 2: Configure Langflow Settings

Create `langflow_config.json`:

```json
{
  "host": "127.0.0.1",
  "port": 7860,
  "database": {
    "url": "sqlite:///./langflow.db"
  },
  "components": {
    "cache": true,
    "auto_save": true
  },
  "security": {
    "secret_key": "your-secret-key",
    "cors_origins": ["*"]
  }
}
```

### Step 3: Test Langflow Installation

```bash
# Start Langflow server
langflow run

# Expected output:
# Langflow v1.0.0 running on http://127.0.0.1:7860
# Open browser and go to http://localhost:7860
```

Keep Langflow running in terminal. Open browser to `http://localhost:7860`

### Step 4: Create Langflow User Account (if required)

1. On first startup, you may need to create admin account
2. Enter admin email and password
3. Log in with credentials

---

## Workflow Import & Configuration

### Step 1: Access Langflow Dashboard

1. Open browser: `http://localhost:7860`
2. You should see Langflow welcome page
3. Create/log in to account if prompted

### Step 2: Import Workflow

**Method 1: Upload JSON File**

```bash
1. Click "New Flow"
2. Click "Import from file"
3. Browse to: workflow/langflow_workflow.json
4. Click "Open"
5. Wait for import to complete
```

**Method 2: Create from Scratch**

```bash
1. Click "New Flow"
2. Name the flow: "AI Interview Trainer"
3. Start adding components:
   - Chat Input Node
   - Prompt Template Node
   - IBM watsonx.ai Node
   - Chat Output Node
```

### Step 3: Configure IBM watsonx.ai Component

1. **Add Component**
   - In Langflow, search "IBM watsonx"
   - Drag IBM watsonx.ai component to canvas

2. **Configure Credentials**
   - Click on IBM watsonx.ai component
   - In right panel, fill in:
     - **API Key**: Your IBM Watson API key
     - **Project ID**: Your watsonx project ID
     - **Endpoint URL**: https://api.dataplatform.cloud.ibm.com
     - **Model ID**: meta-llama/llama-3-3-70b-instruct
     - **Region**: us-south (or your region)

3. **Set Model Parameters**
   ```
   Max Tokens: 2048
   Temperature: 0.7
   Top P: 0.9
   Top K: 40
   Repetition Penalty: 1.1
   ```

### Step 4: Configure Prompt Template

1. **Add Prompt Component**
   - Search "Prompt"
   - Drag "Prompt Template" to canvas

2. **Set System Prompt**
   - Copy content from `workflow/prompt_templates/system_prompt.txt`
   - Paste into prompt field
   - Include variables: `{user_input}`, `{context}`, etc.

3. **Save Prompt Template**
   - Click Save
   - Name: "Interview Trainer System Prompt"

### Step 5: Connect Components

1. **Connect Chat Input to Prompt**
   - Drag output from Chat Input → Input of Prompt Template

2. **Connect Prompt to IBM watsonx**
   - Drag output from Prompt → Input of IBM watsonx.ai

3. **Connect Output**
   - Drag output from IBM watsonx → Chat Output

4. **Verify Flow**
   - All components should be connected
   - No red error indicators

### Step 6: Save Workflow

```bash
1. Click "Save" button
2. Name: "AI Interview Trainer Agent"
3. Click "Save"
4. Export as JSON:
   - Click Export → JSON
   - Save to: workflow/langflow_workflow.json
```

---

## Testing & Validation

### Test 1: Connection Test

```bash
# Test IBM watsonx.ai connection
python << 'EOF'
import requests
import json
from dotenv import load_dotenv
import os

load_dotenv()

api_key = os.getenv('IBM_WATSON_API_KEY')
endpoint = os.getenv('IBM_WATSON_ENDPOINT')
project_id = os.getenv('IBM_WATSON_PROJECT_ID')

headers = {
    'Authorization': f'Bearer {api_key}',
    'Content-Type': 'application/json'
}

# Test connection
url = f"{endpoint}/ml/v1/projects/{project_id}/models"
response = requests.get(url, headers=headers)

if response.status_code == 200:
    print("✓ Successfully connected to IBM watsonx.ai")
    print(f"✓ Project ID: {project_id}")
else:
    print(f"✗ Connection failed: {response.status_code}")
    print(f"  Error: {response.text}")
EOF
```

### Test 2: Langflow Test

```bash
# Start Langflow in test mode
langflow run --debug

# Test workflow:
# 1. Go to http://localhost:7860
# 2. Click on your workflow
# 3. Click "Play" or "Run"
# 4. Enter test question: "What is digital electronics?"
# 5. Check if AI generates response
```

### Test 3: End-to-End Interview Test

```bash
# Run a sample interview
# 1. In Langflow chat, select "Start Interview"
# 2. Choose "Technical Interview"
# 3. Set difficulty to "Beginner"
# 4. Answer first question
# 5. Check for:
#    - Question generated ✓
#    - Answer evaluated ✓
#    - Score provided ✓
#    - Feedback generated ✓
```

### Test 4: Performance Test

```bash
# Test response time
python << 'EOF'
import time
import requests
import json

# Measure response time
start = time.time()
# Make API call
end = time.time()

response_time = (end - start) * 1000  # Convert to ms
print(f"Response Time: {response_time:.2f}ms")

# Target: < 3000ms (3 seconds)
if response_time < 3000:
    print("✓ Performance acceptable")
else:
    print("⚠ Performance degraded")
EOF
```

---

## Troubleshooting

### Common Issues & Solutions

#### Issue 1: Python Not Found

**Error:** `'python' is not recognized as an internal or external command`

**Solution:**
```bash
# Use python3 instead
python3 --version

# Or add Python to PATH
# Windows: System Properties → Environment Variables
# Add Python installation directory to PATH
```

#### Issue 2: Virtual Environment Activation Fails

**Error:** `The system cannot find the path specified.`

**Solution:**
```bash
# Ensure you're in correct directory
cd AI-Interview-Trainer-Agent

# Try alternative activation command
# Windows:
python -m venv interview_trainer_env
interview_trainer_env\Scripts\activate.bat

# macOS/Linux:
python3 -m venv interview_trainer_env
source interview_trainer_env/bin/activate
```

#### Issue 3: Langflow Installation Fails

**Error:** `pip is not recognized` or `ERROR: Could not find a version`

**Solution:**
```bash
# Upgrade pip first
python -m pip install --upgrade pip

# Try installing Langflow again
pip install langflow

# If still failing, try specific version
pip install langflow==1.0.0
```

#### Issue 4: IBM watsonx.ai Connection Error

**Error:** `401 Unauthorized` or `Invalid API Key`

**Solution:**
```bash
# Verify .env file has correct credentials
cat .env

# Check API key:
# 1. Go to IBM Cloud console
# 2. Navigate to Service Credentials
# 3. Copy exact API key (no extra spaces)
# 4. Update .env file
# 5. Save and test again
```

#### Issue 5: Port 7860 Already in Use

**Error:** `Address already in use`

**Solution:**
```bash
# Use different port
langflow run --port 7861

# Or kill process using port 7860:
# macOS/Linux:
lsof -i :7860
kill -9 <PID>

# Windows:
netstat -ano | findstr :7860
taskkill /PID <PID> /F
```

#### Issue 6: Model Not Found

**Error:** `Model 'meta-llama/llama-3-3-70b-instruct' not found`

**Solution:**
```bash
# Verify model is available in your region
# Check available models in watsonx.ai console
# Use correct model ID:
# - meta-llama/llama-3.1-70b-instruct
# - ibm/granite-13b-chat-v2
# - mistralai/mistral-large
```

#### Issue 7: Out of Memory Error

**Error:** `MemoryError` or `OutOfMemoryError`

**Solution:**
```bash
# Increase virtual memory:
# Windows: System Properties → Advanced → Virtual Memory
# Linux: Create swap file

# Or reduce batch size:
# In model parameters, reduce max_tokens from 2048 to 1024
```

#### Issue 8: CORS Error in Browser

**Error:** `Access to XMLHttpRequest blocked by CORS policy`

**Solution:**
```bash
# Update Langflow config to allow CORS
# Edit langflow_config.json:
{
  "security": {
    "cors_origins": ["http://localhost:7860", "*"]
  }
}

# Restart Langflow
langflow run
```

---

## Environment Variables

### Complete Environment Variables Reference

```bash
# IBM watsonx.ai Configuration
IBM_WATSON_PROJECT_ID=<your-project-id>
IBM_WATSON_API_KEY=<your-api-key>
IBM_WATSON_ENDPOINT=https://api.dataplatform.cloud.ibm.com
IBM_WATSON_REGION=us-south
IBM_WATSON_AUTH_URL=https://iam.cloud.ibm.com/identity/token

# Langflow Configuration
LANGFLOW_DATABASE_URL=sqlite:///./langflow.db
LANGFLOW_SECRET_KEY=<your-secret-key>
LANGFLOW_LOG_LEVEL=INFO
LANGFLOW_HOST=127.0.0.1
LANGFLOW_PORT=7860

# Application Configuration
APP_NAME=AI Interview Trainer Agent
APP_VERSION=1.0.0
APP_ENV=production
DEBUG=False

# Interview Configuration
MAX_QUESTIONS=20
DEFAULT_DIFFICULTY=intermediate
DEFAULT_DOMAIN=technical
SESSION_TIMEOUT=3600

# LLM Configuration
MODEL_ID=meta-llama/llama-3-3-70b-instruct
MAX_TOKENS=2048
TEMPERATURE=0.7
TOP_P=0.9
```

---

## Next Steps

After successful setup:

1. **Read Documentation**
   - Review [USAGE.md](../docs/USAGE.md)
   - Check [ARCHITECTURE.md](../docs/ARCHITECTURE.md)

2. **Try Sample Interviews**
   - Start a technical interview
   - Try HR interview
   - Explore different difficulty levels

3. **Customize System Prompt**
   - Edit `workflow/prompt_templates/system_prompt.txt`
   - Add your specific topics
   - Test and validate

4. **Add Interview Topics**
   - Extend domain topics in `config/domain_topics.json`
   - Add new question templates
   - Test with new topics

5. **Deploy Application**
   - Set up database
   - Configure production settings
   - Deploy to cloud (AWS, GCP, Azure, IBM Cloud)

6. **Monitor Performance**
   - Set up logging
   - Monitor API usage
   - Track system metrics
   - Analyze interview data

---

## Support & Help

### Getting Help

- **Documentation**: Check [docs/](../docs/) folder
- **FAQs**: See [FAQ.md](../docs/FAQ.md)
- **Issues**: [GitHub Issues](https://github.com/yourusername/issues)
- **Email**: [your-email@example.com]

### Useful Resources

- [IBM watsonx.ai Documentation](https://www.ibm.com/docs/en/watsonx)
- [Langflow Documentation](https://docs.langflow.io)
- [Python Documentation](https://docs.python.org/3/)
- [IBM Cloud Documentation](https://cloud.ibm.com/docs)

---

**Last Updated:** June 2026  
**Setup Guide Version:** 1.0.0  
**Status:** ✅ Production Ready

[← Back to README](../README.md)
