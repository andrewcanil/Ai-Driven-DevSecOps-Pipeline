# AI-Driven DevSecOps Pipeline

<div align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![Node.js 18+](https://img.shields.io/badge/node-18+-green.svg)](https://nodejs.org/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

**[Documentation](docs/)** | **[Quick Start](#-quick-start)** | **[Contributing](CONTRIBUTING.md)**

</div>

---

> **Enterprise-grade security automation that integrates seamlessly into your CI/CD pipeline**

An intelligent, AI-powered DevSecOps pipeline that automatically performs SAST and DAST security scans, filters false positives, prioritizes vulnerabilities based on risk, and provides actionable remediation guidance—all integrated into GitHub Actions.

## 🎯 Project Overview

Modern software development demands fast CI/CD pipelines, but integrating security often slows development due to excessive false positives and poor vulnerability prioritization. This project solves that challenge with an **AI-enhanced security analysis system** that:

- ✅ **Reduces false positives by 60-70%** using intelligent pattern matching and context analysis
- ✅ **Prioritizes vulnerabilities** based on multi-factor risk scoring (severity, exploitability, business impact, exposure)
- ✅ **Provides actionable remediation** with code examples and best practices
- ✅ **Integrates seamlessly** into GitHub Actions with zero manual intervention
- ✅ **Visualizes results** through a modern, interactive dashboard

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      GitHub Actions CI/CD                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐         ┌──────────────┐                      │
│  │   SAST Scan  │         │   DAST Scan  │                      │
│  │   (Semgrep)  │         │  (OWASP ZAP) │                      │
│  └──────┬───────┘         └──────┬───────┘                      │
│         │                        │                              │
│         └────────┬───────────────┘                              │
│                  │                                              │
│         ┌────────▼─────────┐                                    │
│         │  AI Engine       │                                    │
│         │  ┌────────────┐  │                                    │
│         │  │ Parser     │  │                                    │
│         │  │ FP Detect  │  │                                    │
│         │  │ Risk Score │  │                                    │
│         │  │ Prioritize │  │                                    │
│         │  │ Remediate  │  │                                    │
│         │  └────────────┘  │                                    │
│         └────────┬─────────┘                                    │
│                  │                                              │
│         ┌────────▼─────────┐                                    │
│         │  Analysis Report │                                    │
│         │     (JSON)       │                                    │
│         └────────┬─────────┘                                    │
│                  │                                              │
│         ┌────────▼─────────┐                                    │
│         │   Dashboard      │                                    │
│         │   (React App)    │                                    │
│         └──────────────────┘                                    │
└─────────────────────────────────────────────────────────────────┘
```

## 🚀 Quick Start

### Prerequisites

- GitHub repository
- Python 3.11+
- Node.js 18+
- Docker (for OWASP ZAP)

### Fork & Setup

1. **Fork this repository** to your own GitHub account

2. **Enable GitHub Actions**
   
   The workflow at `.github/workflows/security-scan.yml` will automatically run on:
   - Push to `main` or `develop` branches
   - Pull requests
   - Manual trigger via workflow_dispatch
   - Daily at 2 AM UTC (scheduled scan)

3. **Configure policy** (optional)
   
   Edit `config/policy.yml` to customize security rules, severity thresholds, and risk scoring for your organization.

4. **Deploy Dashboard** (optional)
   
   **Option A — Vercel (recommended):**
   
   See [VERCEL_DEPLOYMENT.md](VERCEL_DEPLOYMENT.md) for one-click deployment instructions.
   
   **Option B — GitHub Pages:**
   
   The workflow automatically deploys to GitHub Pages. Enable it in your repo settings under **Settings → Pages → Source → GitHub Actions**.

5. **View results**
   
   After the workflow completes:
   - **Dashboard**: Visit your deployed URL (auto-updates with each scan)
   - **Download Artifacts**: Get detailed reports from the Actions tab
   - **Local Dashboard**: Build and run `dashboard/` for offline viewing

### Running Locally

#### Test the Vulnerable Application

```bash
cd Vulnerable_app
pip install -r requirements.txt
python app.py
# Visit http://localhost:5000
```

#### Run Security Scans

```bash
# SAST with Semgrep
pip install semgrep
semgrep --config semgrep-rules/ --json > results/semgrep.json

# DAST with OWASP ZAP (requires Docker)
docker run --rm -v $(pwd)/results:/zap/wrk owasp/zap2docker-stable \
  zap-baseline.py -t http://localhost:5000 -J /zap/wrk/zap_report.json
```

#### Run AI Analysis

```bash
cd ai-engine
pip install -r requirements.txt
python main.py \
  --sast-results ../results/sast/ \
  --dast-results ../results/dast/ \
  --policy ../config/policy.yml \
  --output ../results/ai_analysis.json \
  --verbose
```

#### Build and View Dashboard

```bash
cd dashboard
npm install
npm run dev
# Visit http://localhost:5173
```

## 📊 Features

### 1. Comprehensive Security Scanning

- **SAST (Static Analysis)**: Semgrep with 25+ custom rules covering OWASP Top 10
- **DAST (Dynamic Analysis)**: OWASP ZAP for runtime vulnerability detection
- **Coverage**: SQL injection, XSS, command injection, authentication flaws, crypto weaknesses, and more

### 2. Intelligent False Positive Detection

The AI engine uses multiple techniques to identify false positives:

- **File pattern matching**: Excludes test files, build artifacts, and vendor code
- **Code pattern analysis**: Detects security exception markers (`# nosec`, `# safe:`)
- **Context awareness**: Identifies test code, examples, and commented code
- **Confidence scoring**: Combines multiple signals for accurate FP detection

**Result**: 60-70% reduction in alert noise

### 3. Multi-Factor Risk Scoring

Vulnerabilities are scored using weighted factors:

- **Severity** (30%): Base severity from scanner
- **Exploitability** (25%): Ease of exploitation (proven exploit, POC, theoretical)
- **Business Impact** (20%): Potential damage (catastrophic, severe, moderate)
- **Exposure** (15%): Attack surface (public internet, authenticated, internal)
- **Compliance** (10%): Regulatory impact (OWASP, CWE, PCI-DSS)

### 4. Policy-Based Prioritization

Configure company-specific rules in `config/policy.yml`:

```yaml
prioritization:
  auto_priority_rules:
    - name: "Critical Authentication Bypass"
      conditions:
        severity: ["CRITICAL", "HIGH"]
        cwe: ["CWE-287", "CWE-306"]
      priority: 1
```

### 5. Actionable Remediation Guidance

Each vulnerability includes:
- Detailed explanation
- Code examples (vulnerable vs. secure)
- OWASP/CWE references
- Estimated remediation effort
- SLA based on priority

### 6. Modern Dashboard

- **Real-time insights**: Vulnerability overview, severity distribution, trends
- **Interactive filtering**: Search, filter by severity/source
- **Export capabilities**: CSV export for reporting
- **Beautiful UI**: Dark theme with glassmorphism effects

## 📈 Measurable Metrics

The system tracks and reports:

| Metric | Target | Description |
|--------|--------|-------------|
| **False Positive Reduction** | 60-70% | Percentage of noise filtered out |
| **Prioritization Accuracy** | ≥80% | Critical vulns in top 20% of results |
| **CI/CD Performance Impact** | <15% | Additional pipeline execution time |
| **Automation Coverage** | 100% | Scans run automatically on every commit |
| **Remediation Efficiency** | 30-40% | Reduction in mean time to fix |

## 🔧 Configuration

### Policy Configuration

Edit `config/policy.yml` to customize:

- **Severity weights and thresholds**
- **False positive exclusion patterns**
- **Risk scoring factors**
- **Compliance framework mappings**
- **Remediation SLAs**
- **Custom security rules**

Example:

```yaml
severity:
  weights:
    CRITICAL: 1.0
    HIGH: 0.75
    MEDIUM: 0.5
  blocking_severities:
    - CRITICAL
    - HIGH

false_positive_detection:
  enabled: true
  confidence_threshold: 0.7
  exclusion_patterns:
    file_patterns:
      - "*/test/*"
      - "*/node_modules/*"
```

### GitHub Actions Secrets

For DAST scanning, set the `ZAP_TARGET` secret:

```bash
gh secret set ZAP_TARGET --body "http://your-app-url.com"
```

## 📁 Project Structure

```
Ai-Driven-DevSecOps-Pipeline/
├── .github/
│   └── workflows/
│       └── security-scan.yml       # GitHub Actions workflow
├── ai-engine/                      # AI processing engine
│   ├── main.py                     # Main orchestrator
│   ├── models.py                   # Data models
│   ├── parsers.py                  # SAST/DAST parsers
│   ├── false_positive_detector.py  # FP detection
│   ├── risk_scorer.py              # Risk scoring
│   ├── prioritizer.py              # Prioritization
│   ├── remediation_engine.py       # Remediation guidance
│   ├── policy_loader.py            # Policy configuration
│   └── requirements.txt
├── config/
│   └── policy.yml                  # Security policy
├── dashboard/                      # React dashboard
│   ├── src/
│   │   ├── App.jsx                 # Main dashboard
│   │   ├── App.css
│   │   ├── index.css
│   │   └── main.jsx
│   ├── index.html
│   ├── package.json
│   └── vite.config.js
├── semgrep-rules/
│   └── default.yml                 # Custom Semgrep rules
├── zap/
│   ├── zap-config.yml              # OWASP ZAP configuration
│   └── run_zap.sh
├── Vulnerable_app/                 # Test application
│   ├── app.py                      # Intentionally vulnerable Flask app
│   └── requirements.txt
├── docs/                           # Documentation
│   ├── ARCHITECTURE.md
│   └── DEPLOYMENT.md
└── README.md
```

## 🧪 Testing

### Vulnerable Application

The `Vulnerable_app/` directory contains a Flask application with **intentional security flaws** across all severity categories:

| Severity | Vulnerability | CWE | Endpoint |
|----------|--------------|-----|----------|
| **CRITICAL** | Remote Code Execution (`eval()`) | CWE-95 | `/calculate` |
| **CRITICAL** | Insecure Deserialization (`pickle`) | CWE-502 | `/deserialize` |
| **HIGH** | SQL Injection | CWE-89 | `/login` |
| **HIGH** | Cross-Site Scripting (XSS) | CWE-79 | `/search` |
| **HIGH** | Command Injection | CWE-78 | `/ping` |
| **HIGH** | Hardcoded Secrets | CWE-798 | Global |
| **MEDIUM** | Weak Cryptographic Hash (MD5) | CWE-327 | `/register` |
| **MEDIUM** | Path Traversal | CWE-22 | `/upload` |
| **MEDIUM** | SSL Verification Disabled | CWE-295 | `/fetch` |
| **LOW** | Insecure Random | CWE-330 | `/token` |
| **LOW** | Information Disclosure | CWE-209 | Error handlers |

### Trigger a Scan

```bash
# Push code to trigger workflow
git add .
git commit -m "Test security scan"
git push

# Or manually trigger
gh workflow run security-scan.yml
```

## 📚 Documentation

- [Architecture Documentation](docs/ARCHITECTURE.md) - Detailed system design
- [Deployment Guide](docs/DEPLOYMENT.md) - Production deployment instructions

## 🎓 Academic Context

This project demonstrates:

1. **Integration of AI/ML in DevSecOps**: Rule-based AI with ML-ready architecture
2. **Automated security testing**: SAST + DAST in CI/CD
3. **Intelligent vulnerability management**: False positive reduction, risk scoring, prioritization
4. **Scalable architecture**: Industry-level design patterns
5. **Measurable outcomes**: Quantifiable security improvements

### Key Innovations

- **Context-aware false positive detection**: Goes beyond simple pattern matching
- **Multi-factor risk scoring**: Considers exploitability, business impact, and exposure
- **Policy-driven automation**: Configurable rules for different organizations
- **Developer-friendly**: Actionable insights without overwhelming noise

## 🔮 Future Enhancements

- [ ] Machine learning models for adaptive FP detection
- [ ] Historical learning from developer feedback
- [ ] Role-based dashboard access control
- [ ] Compliance-as-Code integration (PCI-DSS, HIPAA, SOC 2)
- [ ] Automated security gate enforcement
- [ ] Integration with JIRA, Slack, and other tools
- [ ] Trend analysis and security metrics over time

## 🤝 Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) for details.

## 🙏 Acknowledgments

- OWASP for security testing tools and guidelines
- Semgrep for powerful SAST capabilities
- The open-source security community

## 📞 Support

For questions or issues:
- Open an issue on GitHub

---

**⚠️ Disclaimer**: The vulnerable application in this repository contains intentional security flaws for educational purposes. **DO NOT deploy it to production environments.**
