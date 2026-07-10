---
title: "Event 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# REPORT: FCAJ COMMUNITY DAY

## 1. Event Overview

- **Objectives:**
  - Create networking opportunities with technology community members.
  - Learn from experts about Cloud Computing and Generative AI (GenAI).
  - Share practical solutions: from LLM prompt optimization, network architecture, to enterprise-grade Multi-Agent AI system deployment.
- **Date & Time:** 09:00 - 12:00, May 23, 2026.
- **Location:** 36th Floor, Bitexco Financial Tower, Ho Chi Minh City, Vietnam.
- **Role:** Attendee.

## 2. Speakers

- **Nguyen Gia Hung:** Solution Architect at AWS Vietnam & Founder of FCJ.
- **Tinh Truong:** Platform Engineer at GoTymeX.
- **Pham Nguyen Hai Anh:** Cloud Consultant at G-AsiaPacific Vietnam.
- **Nguyen Tuan Thinh:** DevOps Engineer at First Cloud AI Journey.
- **Team UTMorpho:** Project development team awardee at LotusHacks 2026.
- **Duc Dao:** Solution Architect at Cloud Kinetics.
- **Vy Lam:** Senior Business Systems Analyst at VPBank.

## 3. Session Summaries

### Career Trends & AI Era Skills (Speaker: Nguyen Gia Hung)

- **The AI Paradox:** As AI makes software development cheaper and faster, market demand for software will explode (similar to how cheap LED lights triggered an explosion in lighting demand).
- **Hiring Requirements:** A university degree is still a necessary "ticket" for recruitment, but technical knowledge alone is no longer enough to compete.
- **Focus on Use Cases & Products:** Engineers need to master real-world business problems in each domain (finance, banking…) and be able to package them into actual products to convince businesses, rather than stopping at demos.
- **Speed is Key:** AI capability doubles every 4 months, demanding that tech professionals never delay knowledge updates.

### Context Optimization for AI (Speaker: Tinh Truong)

- Many incorrect AI responses are not due to poor models but a lack of context.
- **Effective Context Framework (4 steps):** Goal $\rightarrow$ Relevant info $\rightarrow$ Constraints $\rightarrow$ Success criteria.

### AI Assistant Applications with Amazon Q (Speaker: Pham Nguyen Hai Anh)

- Automate repetitive office tasks using AI Agents, securely connecting to over 40 enterprise data sources (S3, Databases…).

### Network Infrastructure Optimization with Amazon CloudFront (Speaker: Nguyen Tuan Thinh)

- Using CloudFront with AWS Shield and WAF to solve DDoS mitigation, Origin Cloaking, and effective cost control through flat-rate pricing.

### Building Real Products Under Time Pressure (Team UTMorpho)

- The 36-hour hackathon product-building journey proved that the best ideas always come from real-world problems encountered in daily work.
- In software development, team alignment and understanding matter more than having too many ideas.

### The Truth About LLM Uncertainty (Speaker: Duc Dao)

- Even when setting Temperature = 0 (T=0) expecting absolute results, AI can still return different answers with deviation rates up to 70%.
- Root cause: Due to floating-point arithmetic characteristics on GPU architecture and inference batching by platform providers.

### Enterprise-Grade Multi-Agent Systems (Speaker: Vy Lam)

- When scoring credit for startups, Single Agent AI models often fail due to information gaps, context overload, and lack of cross-validation mechanisms.
- Using Multi-Agent architecture (acting as a virtual appraisal board with specialized agents for financial, market, and risk analysis) increases reliability, provides clear audit trails, and reduces processing time and costs by up to 95%.

## 4. Value & Lessons Learned

### Mindset

- **Enterprise-grade Mindset:** When deploying AI into enterprise systems, "making it work" is not enough. The system must ensure security, data compliance, and stable operations from day one of design.
- **Engineer's Value Perspective:** Core value lies not in writing code fast, but in understanding business problems (Use cases) and turning them into complete products.
- **Accepting AI's Probabilistic Nature:** Applications should not expect LLMs to be 100% accurate like conventional code; instead, build fallback mechanisms (e.g., structured JSON formats) to handle uncertainty.

### Application to Work & Study

- Instead of setting T=0 when needing logical answers, setting T=0.1 provides a good balance to prevent LLMs from getting stuck in infinite text loops.
- A more practical approach when starting a new project: avoid spreading too thin on imaginary features, focus on solving real frustrations like the UTMorpho team's lesson.
- Learned the AI deployment pipeline from local machine to production environment on AWS (via Docker, ECR, Bedrock, and API Gateway).

## 5. Personal Experience

- **Breaking Misconceptions:** The event helped me recognize common misconceptions when using AI, notably the illusion of "absolute" results when configuring LLMs and the risks of using a single AI agent for important decisions.
- **Inspiration for Action:** Listening to the UTMorpho hackathon team's 36-hour sprint story inspired me to challenge myself in high-intensity tech competitions in the future. Hung's affirmation that "the market changes every 4 months" urged me to take action immediately.
- **Career Direction:** Through the banking credit case study, I understood how major corporations are shaping strict enterprise standards (6 pillars), realizing I need to strengthen my Security and Deployment skills rather than just coding.