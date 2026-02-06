# MDMetrix API: Technical Overview

## Project Summary

MDMetrix API represents the core backend infrastructure of the MDMetrix healthcare analytics platform—a comprehensive system for statistical process control, real-time dashboards, and AI-driven insights in healthcare settings. With over 74,000 lines of Python code, this is the foundational system that powers the entire MDMetrix product suite.

Mr. Schwab served in a dual capacity on this project: as the senior engineer who architected and implemented the core API infrastructure, and as the team manager leading a small group of mid-level developers. This dual role required both deep technical expertise to design scalable systems and leadership skills to guide the team through complex healthcare analytics challenges.

The platform enables healthcare organizations to track performance metrics, detect statistical anomalies through control charts, identify disparities across patient populations, and receive AI-driven recommendations for improvement—all through a secure, multi-tenant architecture designed for enterprise healthcare environments.

---

## The Innovation: Statistical Process Control for Healthcare

The core insight driving MDMetrix is applying industrial quality control techniques to healthcare outcomes:

| Industrial SPC Concept | MDMetrix Healthcare Adaptation |
|-----------------------|-------------------------------|
| Manufacturing defects | Patient outcomes, wait times, complications |
| Control charts | Real-time clinician/department dashboards |
| Process signals | Statistical anomalies requiring investigation |
| Root cause analysis | Drill-down by demographics, procedures, time |
| Continuous improvement | AI-driven improvement recommendations |
| Quality metrics | Healthcare equity and disparity detection |

This translation of proven manufacturing techniques to healthcare enables data-driven quality improvement that was previously manual and inconsistent.

---

## Technical Architecture

```
mdmetrix_api/
├── mdmetrix/
│   ├── endpoint/                    # 23 REST API namespaces
│   │   ├── chart/                   # Chart data generation
│   │   ├── control_board/           # Dashboard CRUD & sharing
│   │   ├── ai_signal/               # Signal detection API
│   │   ├── manager_data/            # AI analysis endpoints
│   │   ├── equity_info/             # Disparity analysis
│   │   └── [18 more namespaces...]
│   ├── provider/                    # 100+ business logic providers
│   │   ├── signals/                 # 20+ statistical signal detectors
│   │   ├── charts/                  # 15+ control chart implementations
│   │   ├── authentication/          # Multi-provider auth system
│   │   ├── binning/                 # Data binning strategies
│   │   ├── security/                # RBAC field-level access
│   │   └── ml/                      # Machine learning utilities
│   ├── data/
│   │   ├── importer/                # ETL pipeline (facts & dimensions)
│   │   ├── model/                   # 60+ data model classes
│   │   │   ├── security/            # Roles, permissions, assets
│   │   │   ├── config/              # Dataset definitions
│   │   │   └── tardis/              # Analytics metadata
│   │   └── generators/              # Dynamic schema generation
│   ├── manager/
│   │   ├── systemai/                # AI providers (capacity, equity, risk)
│   │   ├── flow/                    # Manager workflow system
│   │   └── fishing_pills/           # Intelligent planning agent
│   ├── webserver/
│   │   ├── decorators/              # Auth, permission, logging
│   │   ├── endpoint/                # Login, session management
│   │   └── session.py               # Redis session handling
│   ├── subscriptions/               # Signal change notifications
│   ├── settings/                    # Multi-source configuration
│   └── flask_aws_cognito/           # Cognito authentication plugin
├── tests/                           # Unit tests
├── playwright_tests/                # End-to-end tests
└── .gitlab-ci.yml                   # CI/CD pipeline
```

---

## Talent Highlights: Skills Demonstrated

### 1. Large-Scale System Architecture

The MDMetrix API demonstrates mastery of enterprise-scale system design:

- **Provider Pattern Architecture**: 100+ specialized provider classes with clean separation of concerns
- **Multi-Tenant Design**: Site-based isolation with configurable access controls
- **Layered Architecture**: Clear separation between endpoints, providers, and data models
- **Horizontal Scalability**: Split-graph architecture distributes data across Redis instances

Building a 74,000+ line codebase that remains maintainable requires disciplined architectural thinking that Mr. Schwab both implemented and taught to his team.

### 2. Statistical Process Control Implementation

A comprehensive library of healthcare-specific SPC tools:

**20+ Signal Detection Algorithms:**
| Signal Type | Statistical Rule |
|------------|------------------|
| Shift Detector | 8+ consecutive points above/below median |
| Trend Detector | 6+ consecutive increasing/decreasing points |
| M2M Detector | Two-of-three points outside ±2σ |
| Astronomical Value | Extreme outlier (±3-4σ) |
| Over-Dispersion | Variance exceeds Poisson expectation |
| Ghost Points | Incomplete data period detection |

**15+ Control Chart Types:**
- P-Chart, C-Chart, U-Chart (attribute data)
- X-Chart, X-Bar-Chart, S-Chart (continuous data)
- T-Chart, G-Chart (time-between-events)
- Run Chart with control limits
- Log-Linear and Power Regression charts

This isn't library usage—it's ground-up implementation of statistical methods adapted for healthcare contexts.

### 3. Healthcare AI System Design

The SystemAI module provides four AI-driven analysis modes:

```
┌─────────────────────────────────────────────────────────┐
│                    SystemAI Provider                     │
├─────────────┬─────────────┬──────────────┬──────────────┤
│  Capacity   │   Equity    │ Improvement  │    Risk      │
│  Analysis   │  Analysis   │   Analysis   │  Analysis    │
├─────────────┼─────────────┼──────────────┼──────────────┤
│ Workload    │ Disparity   │ Performance  │ Risk         │
│ optimization│ detection   │ optimization │ stratification│
└─────────────┴─────────────┴──────────────┴──────────────┘
```

Features include:
- Multi-threaded drill-down analysis across demographic dimensions
- Z-score and percentage-based comparisons with confidence levels
- Balancing metric optimization (improving one metric without harming others)
- Threshold-based filtering for actionable insights

### 4. Enterprise Authentication Architecture

A pluggable multi-provider authentication system:

- **CognitoAuthProvider**: AWS Cognito with OAuth2 flows
- **Federated Identity**: SAML integration for enterprise SSO
- **ServiceAuthProvider**: Service-to-service authentication
- **SupportAuthProvider**: Support team access controls
- **Internal Credentials**: Legacy system support

The system supports seamless provider switching through configuration, enabling deployment across diverse enterprise environments.

### 5. Role-Based Access Control (RBAC)

Sophisticated security model designed for healthcare compliance:

```python
# 3-Tuple Permission Model: (Verb, Scope, Asset)
Permission = (READ, OWN, CONTROL_BOARD)  # Read own dashboards
Permission = (WRITE, ALL, METRIC)         # Edit all metrics

# 6 Role Hierarchy
ADMIN > POWER_USER > MANAGER > STANDARD_USER > READER > DEMO
```

**Field-Level Security**: `RoleSecuredField` restricts data display based on user role—critical for HIPAA-sensitive healthcare data where different roles see different patient information.

### 6. Parallel ETL Pipeline

High-performance data import system:

- **mpipe Integration**: Pipeline-based parallel processing for fact/dimension loading
- **Hash-Based Deduplication**: Efficient duplicate detection during bulk imports
- **Transactional Rollback**: Failed imports don't corrupt existing data
- **Multi-threaded Processing**: Configurable thread pools for dimension and fact tables

```python
# Import architecture
DataImporter
├── DimRepo (parallel dimension import)
│   ├── Thread 1: Geography hierarchy
│   ├── Thread 2: Time hierarchy
│   └── Thread N: Custom dimensions
└── FactRepo (parallel fact import)
    ├── Hash deduplication
    └── Bulk validation
```

### 7. Redis Graph Database Integration

Custom graph database layer for dimensional modeling:

- **Split-Graph Architecture**: Distribute graphs across multiple Redis instances by module
- **Dynamic Routing**: Automatic graph selection based on data context
- **Lazy Index Creation**: Indices created on first access, not deployment
- **Connection Pooling**: Thread-safe, reusable connections

### 8. Team Leadership & Mentorship

As team manager, Mr. Schwab:

- Designed the provider pattern architecture that enabled parallel development
- Established coding standards and review processes for the team
- Mentored mid-level developers in statistical algorithms and healthcare domain knowledge
- Balanced feature delivery with technical debt management
- Made architectural decisions that scaled from prototype to production

---

## Key Technical Achievements

### Comprehensive API Surface

23 REST API namespaces covering:
- Chart generation and analysis
- Dashboard (control board) management with sharing permissions
- AI signal detection and search
- Healthcare equity analysis
- Manager workflow and capacity planning
- User subscriptions and notifications
- Administrative functions

### Zero-Trust Signal Processing

Every chart automatically runs through the signal detection pipeline:
```
Chart Request → Data Aggregation → Signal Detection (20+ algorithms) → Annotated Response
```

Users don't request signal detection—it's built into the data flow, ensuring no anomaly goes unnoticed.

### Configuration Hierarchy

Four-layer configuration with intelligent fallback:
1. Environment variables (highest priority)
2. JSON configuration files
3. Unleash feature flags
4. AWS Parameter Store
5. Default values (lowest priority)

This enables identical code to run across development, staging, and production with environment-specific behavior.

### Healthcare Compliance Design

- Activity logging decorators track all user actions
- Field-level access control prevents unauthorized data exposure
- Audit trails for dashboard sharing and modifications
- Role-based metric visibility

---

## Technology Stack

| Layer | Technologies |
|-------|--------------|
| Web Framework | Flask 2.3.2+, Flask-RESTX 1.1.0+, Gunicorn |
| Data Processing | Pandas 2.0.1+, NumPy 1.24.3+, SciPy 1.10.1+ |
| Machine Learning | Scikit-learn 1.2.2+, NetworkX 3.1+ |
| Visualization | Matplotlib 3.7.1+, Seaborn 0.12.2+ |
| Data Storage | Redis 4.5.4+, RedisGraph |
| Authentication | AWS Cognito, Python-Jose (JWT), Pydantic |
| Cloud Services | AWS (S3, SNS, Parameter Store, ECR) |
| Monitoring | Datadog, Slack SDK, StatsD |
| Feature Flags | UnleashClient 5.6.0+ |
| Email | SendGrid 6.10.0+ |
| CI/CD | GitLab CI, Bitbucket Pipelines, Docker |
| Testing | Pytest, Playwright (E2E) |

---

## Performance Characteristics

- **Codebase Size**: 74,000+ lines of Python
- **API Endpoints**: 23 namespaces with hundreds of routes
- **Signal Detectors**: 20+ pluggable algorithms
- **Chart Types**: 15+ statistical control charts
- **Provider Classes**: 100+ specialized business logic components
- **Data Models**: 60+ entity classes
- **Authentication Providers**: 6 pluggable auth strategies
- **Parallel Processing**: Configurable thread pools for ETL and analysis

---

## What This Project Demonstrates

The MDMetrix API project is evidence that Mr. Schwab can:

1. **Architect enterprise-scale systems**: 74,000+ lines of maintainable, well-structured code
2. **Implement complex statistical algorithms**: Ground-up SPC implementation, not library calls
3. **Design healthcare-compliant systems**: RBAC, audit trails, field-level security
4. **Lead development teams**: Provider pattern enabled parallel team development
5. **Build production infrastructure**: CI/CD, monitoring, multi-environment configuration
6. **Bridge technical and business domains**: Translating healthcare needs into software architecture
7. **Balance dual roles**: Delivering technical work while managing team priorities

This project represents the intersection of statistical expertise, healthcare domain knowledge, enterprise security requirements, and team leadership—the core platform that powers MDMetrix's healthcare analytics capabilities.

---

*Project Location: ~/adaptx/mdmetrix_api*
