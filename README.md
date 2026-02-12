# 🚀 CareerCopilot - AI-Powered Job Search Platform

<div align="center">

[![Python](https://img.shields.io/badge/python-3.10%2B-blue.svg)](https://www.python.org/downloads/)
[![Streamlit](https://img.shields.io/badge/Streamlit-1.30%2B-FF4B4B.svg)](https://streamlit.io)
[![License](https://img.shields.io/badge/license-Apache--2.0-green.svg)](LICENSE)
[![Supabase](https://img.shields.io/badge/Supabase-Backend-3ECF8E.svg)](https://supabase.com)

**Your AI-powered job hunting companion — Scrape smarter, match faster, apply with confidence.**

### [👉 Click here to try the Live Demo](https://career-copilot-ai.streamlit.app/)

[Features](#-features) • [Quick Start](#-quick-start) • [Architecture](#-architecture) • [Demo](#-demo) • [Contributing](#-contributing)

</div>

---

## 📖 Overview

**CareerCopilot** is a full-stack job search automation platform that combines intelligent web scraping with AI-powered resume matching. It helps job seekers discover high-quality opportunities, analyze job-resume fit, and make data-driven application decisions.

### 🎯 Problem It Solves

Traditional job hunting is:
- ⏰ **Time-consuming**: Manually browsing hundreds of job boards
- 🎲 **Hit-or-miss**: No way to know if you're qualified before applying
- 💸 **Inefficient**: Wasting time on low-match or spam postings
- 🤷 **Unclear**: Not knowing which skills you're missing

**CareerCopilot automates everything.**

---

## ✨ Features

### 🔧 Backend (Job Scraper)

| Feature | Description |
|---------|-------------|
| **🕷️ LinkedIn Scraper** | Automated job posting extraction with anti-detection measures |
| **🧠 LLM Salary Parser** | Normalizes messy salary text into structured min/max ranges |
| **🔄 Smart De-duplication** | Identifies and removes duplicate/reposted jobs |
| **📊 Structured Output** | Exports clean CSV with standardized fields |
| **⏰ GitHub Actions** | Scheduled daily scraping runs automatically |
| **☁️ Supabase Sync** | Real-time data upload to cloud database |

### 🎨 Frontend (Streamlit Dashboard)

| Feature | Description |
|---------|-------------|
| **🏠 Public Feed** | Browse latest jobs with keyword filtering (Free) |
| **🔍 Advanced Search** | Multi-criteria filtering (location, salary, seniority) (Premium) |
| **🤖 AI Resume Matching** | DeepSeek-powered compatibility scoring with explanations (Premium) |
| **📈 Match Score (0-100)** | Quantified job-resume fit with reasoning |
| **⚠️ Skill Gap Analysis** | Identifies missing skills for each position |
| **📱 Responsive UI** | Clean, modern interface with real-time updates |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    CareerCopilot System                     │
└─────────────────────────────────────────────────────────────┘
                           │
        ┌──────────────────┴──────────────────┐
        │                                     │
   ┌────▼────┐                          ┌────▼────┐
   │ Backend │                          │Frontend │
   │ (Scraper)│                         │(Streamlit)│
   └────┬────┘                          └────┬────┘
        │                                    │
        │  1. Scrape LinkedIn                │  3. User queries
        │  2. Parse & Clean                  │  4. AI matching
        │                                    │
        ├─────────────┐      ┌───────────────┤
        │             │      │               │
   ┌────▼────┐  ┌────▼──────▼────┐     ┌─────▼─────┐
   │ GitHub  │  │    Supabase    │     │  Google   │
   │ Actions │  │   (Database)   │     │  Gemini   │
   └─────────┘  └────────────────┘     └───────────┘
                      │
                      │ PostgreSQL Tables:
                      ├─ JOB_POSTS (scraped data)
                      ├─ profiles (user accounts)
                      ├─ resumes (uploaded files)
                      ├─ search_configs (user preferences)
                      └─ PREMIUM_OUTPUT (AI results)
```

### Tech Stack

**Backend:**
- Python 3.10+
- BeautifulSoup4 (web scraping)
- Playwright (browser automation)
- Ollama / OpenAI (LLM integration)
- Pandas (data processing)

**Frontend:**
- Streamlit (web framework)
- Supabase (auth + database)
- DeepSeek API (AI matching)
- PyPDF (resume parsing)

**Infrastructure:**
- GitHub Actions (scheduling)
- Supabase Storage (resume files)
- PostgreSQL (relational data)

---

## 🚀 Quick Start

### Prerequisites

- Python 3.10+
- Supabase account (free tier works)
- DeepSeek API key (or OpenAI)
- Git

### 1. Backend Setup (Job Scraper)

```bash
# Clone backend repo
git clone https://github.com/arronchen-520/CareerCopilot-Scraper.git
cd CareerCopilot-Scraper

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
python -m playwright install chromium

# Configure environment
cp .env.example .env
# Edit .env with your Supabase credentials

# Create config
cp config/example.yaml config/my_search.yaml
# Edit search parameters (keywords, location, etc.)

# Run scraper
python src/main.py
```

### 2. Frontend Setup (Streamlit App)

```bash
# Clone frontend repo
git clone https://github.com/arronchen-520/CareerCopilot-Dashboard.git
cd CareerCopilot-Dashboard

# Install dependencies
pip install -r requirements.txt

# Configure secrets
mkdir .streamlit
cat > .streamlit/secrets.toml << EOF
SUPABASE_URL = "your_supabase_url"
SUPABASE_API_KEY = "your_supabase_anon_key"
DEEPSEEK_API_KEY = "your_deepseek_key"
DEEPSEEK_URL = "https://api.deepseek.com"
EOF

# Run app
streamlit run dashboard.py
```

### 3. Supabase Database Setup

**Create these tables in your Supabase project:**

```sql
-- User profiles
CREATE TABLE profiles (
  user_id UUID PRIMARY KEY,
  email TEXT,
  tier TEXT DEFAULT 'free',
  tier_expire_at TIMESTAMP,
  api_limit INT DEFAULT 700,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Job postings
CREATE TABLE JOB_POSTS (
  "Job Title" TEXT,
  "Company" TEXT,
  "Location" TEXT,
  "Date" DATE,
  "Keyword" TEXT,
  "URL" TEXT PRIMARY KEY,
  "Salary" TEXT,
  "Job Description" TEXT,
  "Employment type" TEXT,
  "Seniority level" TEXT,
  "Industries" TEXT
);

-- User resumes
CREATE TABLE resumes (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES profiles(user_id),
  path TEXT,
  uploaded_at TIMESTAMP DEFAULT NOW()
);

-- Search configs
CREATE TABLE search_configs (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES profiles(user_id),
  config_json JSONB,
  api_used INT DEFAULT 0,
  updated_at TIMESTAMP DEFAULT NOW()
);

-- AI match results
CREATE TABLE PREMIUM_OUTPUT (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES profiles(user_id),
  "Job Title" TEXT,
  "Company" TEXT,
  "Location" TEXT,
  "URL" TEXT,
  "Date" DATE,
  "Salary" TEXT,
  "Match Score" INT,
  "Reasoning" TEXT,
  "Missing Skills" TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- User feedback
CREATE TABLE user_feedback (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID,
  user_email TEXT,
  category TEXT,
  content TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
```

**Set up Storage bucket:**
- Create bucket: `resumes`
- Set permissions for authenticated users

---

## 📊 Output Schema

### Job Postings Table

| Field | Type | Description |
|-------|------|-------------|
| `Job Title` | TEXT | Normalized job title |
| `Company` | TEXT | Company name |
| `Location` | TEXT | City, State, Country |
| `Date` | DATE | Posting date |
| `Keyword` | TEXT | Search keyword used |
| `URL` | TEXT | Application link (Primary Key) |
| `Salary` | TEXT | Raw salary text |
| `Job Description` | TEXT | Full description |
| `Employment type` | TEXT | Full-time/Part-time/Contract |
| `Seniority level` | TEXT | Entry/Mid/Senior |

### AI Match Results

| Field | Type | Description |
|-------|------|-------------|
| `Match Score` | INT | 0-100 compatibility score |
| `Reasoning` | TEXT | Why this job matches (or doesn't) |
| `Missing Skills` | TEXT | Comma-separated skill gaps |

**Scoring Rubric:**
- **80-100**: 🟢 Strong match, highly recommended
- **60-79**: 🟡 Moderate match, worth applying if interested
- **0-59**: 🔴 Weak match, major gaps or overqualified

---

## 🎯 Usage Examples

### Scenario 1: Daily Job Monitoring

**Backend (automated):**
```yaml
# config/daily_ml.yaml
keywords: "Machine Learning Engineer"
location: "Toronto, Ontario, Canada"
period: "past day"
max_pages: 10
```

**Schedule with GitHub Actions** → runs every day at 9 AM EST

**Frontend:**
- Login → check "Private Data" tab
- See AI-matched jobs from last 7 days
- Apply to high-scoring positions

### Scenario 2: Custom Search + AI Analysis

1. **Login** to dashboard
2. **Navigate** to "AI Search" tab
3. **Configure** filters:
   - Keywords: "Data Scientist"
   - Location: "Remote"
   - Current Title: "Data Analyst"
   - Current Salary: "80k"
4. **Click** "Start AI Matching"
5. **Review** results with:
   - Match scores
   - Reasoning
   - Skill gaps
6. **Apply** to top matches

### Scenario 3: Market Research

```python
# Analyze salary trends
import pandas as pd
from supabase import create_client

db = create_client(SUPABASE_URL, SUPABASE_KEY)
jobs = db.table("JOB_POSTS").select("*").execute()
df = pd.DataFrame(jobs.data)

# Avg salary by location
df.groupby('Location')['Max Salary'].mean()

# Most in-demand skills
all_skills = df['Missing Skills'].str.split(',').explode()
all_skills.value_counts().head(10)
```

---

## 🔧 Configuration

### Backend Config (YAML)

```yaml
# config/my_search.yaml
keywords: "Data Scientist"
location: "New York, NY"
distance: 25  # miles

period: "past week"
job_type: "full time"

max_pages: 20
max_workers: 5

output_file: "data/jobs.csv"
```

### Frontend Config (secrets.toml)

```toml
SUPABASE_URL = "https://xxx.supabase.co"
SUPABASE_API_KEY = "eyJhbGc..."
DEEPSEEK_API_KEY = "sk-xxx"
DEEPSEEK_URL = "https://api.deepseek.com"
```

---

## 🎨 Screenshots

### Home Feed (Free Users)
![Home Feed](docs/images/home.png)
*Browse latest jobs with keyword filtering*

### AI Matching (Premium)
![AI Match](docs/images/ai_match.png)
*Get match scores and skill gap analysis*

### Private Dashboard
![Dashboard](docs/images/dashboard.png)
*Track your application history*

---

## 📈 Roadmap

### ✅ Completed
- [x] LinkedIn scraper with anti-detection
- [x] Supabase integration
- [x] Basic Streamlit UI
- [x] DeepSeek AI matching
- [x] User authentication
- [x] Resume upload & parsing

### 🚧 In Progress
- [ ] Multi-platform scraping (Indeed, Glassdoor)
- [ ] Email notifications for new matches
- [ ] Chrome extension for quick apply

### 🔮 Future
- [ ] Application tracking (Applied/Interview/Offer)
- [ ] Company reviews integration
- [ ] Salary negotiation assistant
- [ ] Interview prep Q&A based on JD

---

## 🛠️ Development

### Backend Development

```bash
# Run tests
pytest tests/

# Format code
black src/
ruff check src/

# Type check
mypy src/
```

### Frontend Development

```bash
# Run with hot reload
streamlit run dashboard.py

# Debug mode
streamlit run dashboard.py --logger.level=debug
```

### Database Migrations

```sql
-- Example: Add new column
ALTER TABLE JOB_POSTS ADD COLUMN "Remote" BOOLEAN DEFAULT FALSE;

-- Example: Create index
CREATE INDEX idx_job_date ON JOB_POSTS("Date");
```

---

## 🤝 Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Quick Contribution Guide

1. **Fork** the repository
2. **Create** a feature branch
   ```bash
   git checkout -b feature/amazing-feature
   ```
3. **Commit** changes
   ```bash
   git commit -m "feat: add amazing feature"
   ```
4. **Push** to branch
   ```bash
   git push origin feature/amazing-feature
   ```
5. **Open** a Pull Request

---

## ⚠️ Disclaimer

**For Educational Use Only**: This project is intended for personal research and education. Users are responsible for complying with LinkedIn's Terms of Service and applicable laws. Web scraping may violate platform policies and result in account restrictions. **Use at your own risk.**

We do not encourage:
- Mass scraping that overloads servers
- Commercial use without permission
- Violating website Terms of Service

**Best Practices:**
- Respect robots.txt
- Use reasonable rate limits
- Don't scrape personal/private data
- Check platform policies before use

---

## 📄 License

This project is licensed under the Apache License 2.0 - see [LICENSE](LICENSE) for details.

---

## 🙏 Acknowledgments

- **Anthropic Claude** for AI assistance in development
- **Supabase** for backend infrastructure
- **Streamlit** for rapid frontend development
- **DeepSeek** for affordable LLM inference
- The open-source community for invaluable tools

---

## 📧 Contact & Support

- **Issues**: [GitHub Issues](https://github.com/arronchen-520/CareerCopilot/issues)
- **Discussions**: [GitHub Discussions](https://github.com/arronchen-520/CareerCopilot/discussions)
- **Email**: yuyu.jobfinder@gmail.com
- **WeChat**: layuyu_canada

---

## 🌟 Star History

[![Star History Chart](https://api.star-history.com/svg?repos=arronchen-520/CareerCopilot&type=Date)](https://star-history.com/#arronchen-520/CareerCopilot&Date)

---

<div align="center">

**⭐ If this project helped you land your dream job, please star it!**

Made with ❤️ by job seekers, for job seekers.

</div>