# Example Workflows

This document provides real-world examples of how Claude Code and Claude Desktop can collaborate through Basic Memory.

## Table of Contents
- [Research → Implementation](#research--implementation)
- [Code Documentation](#code-documentation)
- [Project Planning](#project-planning)
- [Learning & Study](#learning--study)
- [Content Creation](#content-creation)
- [Data Analysis Pipeline](#data-analysis-pipeline)

## Research → Implementation

### Scenario
You need to implement a complex feature but want to research best practices first.

### Workflow Steps

**1. Claude Desktop: Initial Research**
```
User: "Research modern authentication patterns for web applications. Focus on security best practices, popular libraries, and implementation considerations."

Claude Desktop creates comprehensive research notes in:
- `4-Resources/Web-Development/Authentication-Patterns.md`
- `4-Resources/Security/Auth-Security-Checklist.md`
```

**2. Claude Code: Read Research & Plan Implementation**
```bash
# Read the research
uvx basic-memory tool read-note "Authentication Patterns"
uvx basic-memory tool read-note "Auth Security Checklist"

# Create implementation plan
uvx basic-memory tool write-note \
  --title "Auth Implementation Plan" \
  --folder "2-Projects/auth-system" \
  --content "# Authentication Implementation Plan

Based on research from Claude Desktop:

## Chosen Approach
- JWT with refresh tokens
- OAuth2 integration  
- Rate limiting on auth endpoints

## Implementation Steps
1. Set up JWT library
2. Create auth middleware
3. Implement refresh token rotation
4. Add OAuth providers
5. Security testing

## Security Checklist
- [ ] Password hashing (bcrypt)
- [ ] HTTPS only
- [ ] CSRF protection
- [ ] Rate limiting
- [ ] Session timeout
"
```

**3. Claude Code: Implementation**
```bash
# As Claude Code implements, document progress
uvx basic-memory tool write-note \
  --title "Auth Implementation Progress" \
  --folder "2-Projects/auth-system" \
  --content "# Implementation Progress

## Completed
✅ JWT library setup (jsonwebtoken)
✅ Basic auth middleware
✅ Password hashing

## In Progress
🔄 Refresh token rotation

## Next Steps
- OAuth2 integration
- Rate limiting implementation
- Security testing
"
```

**4. Claude Desktop: Review & Iterate**
```
User: "Review the auth implementation progress and suggest improvements or additional considerations."

Claude Desktop reads the progress notes and creates:
- Security audit recommendations
- Testing strategy
- Deployment considerations
```

## Code Documentation

### Scenario
Document a complex codebase for team knowledge sharing.

### Workflow Steps

**1. Claude Code: Analyze Codebase**
```bash
# Analyze file structure
find ./src -name "*.js" | head -20

# Create architectural overview
uvx basic-memory tool write-note \
  --title "Codebase Architecture Analysis" \
  --folder "2-Projects/documentation" \
  --content "# Codebase Architecture

## File Structure
$(tree src/ -I node_modules)

## Key Components
- Authentication: src/auth/
- API Routes: src/routes/
- Database: src/models/
- Utilities: src/utils/

## Dependencies Analysis
$(cat package.json | jq .dependencies)
"

# Document specific complex functions
uvx basic-memory tool write-note \
  --title "Complex Functions Documentation" \
  --folder "2-Projects/documentation" \
  --content "# Complex Functions

## Authentication Flow
File: src/auth/jwt.js
- generateToken(): Creates JWT with user payload
- validateToken(): Verifies and decodes JWT
- refreshToken(): Handles token refresh logic

## Data Processing Pipeline  
File: src/processors/dataProcessor.js
- processData(): Main processing function
- validateInput(): Input validation
- transformData(): Data transformation logic
"
```

**2. Claude Desktop: Create User-Friendly Documentation**
```
User: "Read the codebase analysis and create comprehensive developer documentation."

Claude Desktop creates:
- Getting Started Guide
- API Documentation
- Architecture Decision Records (ADRs)
- Troubleshooting Guide
```

## Project Planning

### Scenario
Plan and execute a multi-phase project.

### Workflow Steps

**1. Claude Desktop: Project Planning**
```
User: "Help me plan a personal finance tracking application."

Claude Desktop creates:
- `2-Projects/finance-app/Project-Overview.md`
- `2-Projects/finance-app/Requirements.md`
- `2-Projects/finance-app/Architecture-Plan.md`
- `2-Projects/finance-app/Phase-1-Tasks.md`
```

**2. Claude Code: Setup & Phase 1 Implementation**
```bash
# Read the project plan
uvx basic-memory tool read-note "Project Overview"
uvx basic-memory tool read-note "Phase 1 Tasks"

# Initialize project
mkdir finance-tracker
cd finance-tracker
npm init -y

# Document setup progress
uvx basic-memory tool write-note \
  --title "Project Setup Complete" \
  --folder "2-Projects/finance-app" \
  --content "# Setup Complete

## Environment
- Node.js project initialized
- Basic folder structure created
- Dependencies identified for Phase 1

## Next Steps from Plan
1. Set up database schema
2. Create basic API endpoints
3. Implement transaction model

## Questions for Claude Desktop
- Should we use MongoDB or PostgreSQL?
- Any additional security considerations?
"
```

**3. Back-and-forth collaboration continues...**

## Learning & Study

### Scenario
Learning a new technology with hands-on practice.

### Workflow Steps

**1. Claude Desktop: Learning Plan**
```
User: "I want to learn GraphQL. Create a comprehensive learning plan."

Creates:
- `3-Areas/Learning/GraphQL-Learning-Path.md`
- `4-Resources/GraphQL/Core-Concepts.md`
- `4-Resources/GraphQL/Best-Practices.md`
```

**2. Claude Code: Hands-on Practice**
```bash
# Read learning materials
uvx basic-memory tool read-note "GraphQL Learning Path"

# Create practice project
mkdir graphql-practice
cd graphql-practice

# Document practice exercises
uvx basic-memory tool write-note \
  --title "GraphQL Practice Log" \
  --folder "3-Areas/Learning" \
  --content "# GraphQL Practice Session $(date)

## Today's Focus
- Basic schema definition
- Query resolvers
- Mutations

## Code Examples
\`\`\`graphql
type User {
  id: ID!
  name: String!
  email: String!
}

type Query {
  users: [User!]!
  user(id: ID!): User
}
\`\`\`

## What I Learned
- Schema-first development approach
- Resolver functions map to schema fields
- Strong typing system

## Tomorrow's Goals
- Subscriptions
- Error handling
- Performance optimization
"
```

## Content Creation

### Scenario
Creating technical blog posts or tutorials.

### Workflow Steps

**1. Claude Desktop: Research & Outline**
```
User: "Help me create a blog post about microservices patterns."

Creates detailed research and outline in:
- `2-Projects/blog/Microservices-Post-Outline.md`
- `4-Resources/Architecture/Microservices-Research.md`
```

**2. Claude Code: Code Examples & Technical Details**
```bash
# Create practical examples
uvx basic-memory tool write-note \
  --title "Microservices Code Examples" \
  --folder "2-Projects/blog" \
  --content "# Code Examples for Microservices Post

## Service Discovery Example
\`\`\`javascript
// service-registry.js
class ServiceRegistry {
  constructor() {
    this.services = new Map();
  }
  
  register(name, url, health) {
    this.services.set(name, { url, health, lastSeen: Date.now() });
  }
  
  discover(name) {
    return this.services.get(name);
  }
}
\`\`\`

## API Gateway Pattern
\`\`\`javascript
// api-gateway.js
const express = require('express');
const httpProxy = require('http-proxy-middleware');

const app = express();

// Route to user service
app.use('/api/users', httpProxy({
  target: 'http://user-service:3001',
  changeOrigin: true
}));
\`\`\`
"
```

**3. Claude Desktop: Final Assembly**
```
User: "Combine the research, outline, and code examples into a complete blog post."

Assembles everything into a polished article.
```

## Data Analysis Pipeline

### Scenario
Analyzing data with documentation throughout the process.

### Workflow Steps

**1. Claude Desktop: Analysis Plan**
```
User: "Help me analyze our customer churn data."

Creates:
- Analysis methodology
- Questions to investigate  
- Expected insights
```

**2. Claude Code: Data Processing**
```bash
# Read analysis plan
uvx basic-memory tool read-note "Churn Analysis Plan"

# Process data and document findings
python analyze_churn.py

# Document results
uvx basic-memory tool write-note \
  --title "Churn Analysis Results" \
  --folder "2-Projects/data-analysis" \
  --content "# Customer Churn Analysis Results

## Key Findings
- 23% monthly churn rate
- Higher churn in first 30 days (67%)
- Price sensitivity correlation: 0.78

## Visualizations Created
- churn_by_month.png
- cohort_analysis.png
- feature_importance.png

## Recommendations for Claude Desktop Review
1. Focus retention efforts on first 30 days
2. Consider pricing strategy adjustments
3. Implement early warning system

## Data Quality Notes
- Missing data in 3% of records
- Outliers removed: 12 records
- Sample size: 10,847 customers
"
```

**3. Claude Desktop: Strategic Recommendations**
```
User: "Review the churn analysis and create strategic recommendations."

Creates executive summary and actionable recommendations.
```

## Tips for Effective Collaboration

### Communication Patterns
- **Clear handoffs**: Always specify what the other Claude should do next
- **Context setting**: Reference previous notes and current project state  
- **Progress updates**: Document what's been completed and what's next
- **Question flagging**: Highlight areas needing input from the other Claude

### Note Organization
- Use consistent folder structures
- Include creation dates and status
- Link related notes together
- Tag notes for easy searching

### Workflow Templates
Create templates for common patterns:

```bash
# Project kickoff template
uvx basic-memory tool write-note \
  --title "Project Template" \
  --folder "templates" \
  --content "# Project: [NAME]

## Objective
[What are we trying to achieve?]

## Scope
- In scope: 
- Out of scope:

## Success Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Handoff Points
- [ ] Research complete → Implementation begins
- [ ] Implementation complete → Testing begins
- [ ] Testing complete → Documentation

## Questions/Decisions Needed
- Question 1?
- Question 2?
"
```

---

These workflows demonstrate the power of combining Claude Desktop's research and analysis capabilities with Claude Code's implementation and technical skills, all while maintaining persistent context through Basic Memory.