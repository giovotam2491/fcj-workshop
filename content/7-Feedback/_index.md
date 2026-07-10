---
title: "Sharing and Feedback"
date: 2026-07-10
weight: 7
chapter: false
pre: " <b> 7. </b> "
---

Here, I would like to freely share my personal thoughts and experiences from participating in the **Workforce Bootcamp – First Cloud AI Journey (FCAJ)** program at Amazon Web Services Vietnam, with the hope of helping the FCAJ team continue to improve for future cohorts.

### Overall Evaluation

**1. Working Environment**

The working environment was incredibly friendly and open. FCAJ members were always willing to help whenever I ran into difficulties — even outside of office hours. There were evenings when I was struggling to trace a CORS error in my Bedrock Lambda handler late into the night, posted the question on the group channel, and still received helpful hints from someone who was online. Community activities organized by FCAJ also created space for me to connect with fellow interns and mentors beyond daily work, making the 12-week journey feel much less isolating.

**2. Support from Mentor / Team Admin**

My mentor provided very detailed, structured guidance each week — from foundational AWS knowledge and architecture design in the early weeks, to reviewing the RAG pipeline, Cognito authentication flows, and WAF configurations in later stages. What I valued most was the mentor's teaching approach: whenever I got stuck on a technical problem (such as IAM permission misconfigurations, the wrong JWT token type, or cold start latency issues), the mentor encouraged me to research and attempt a fix first before stepping in with hints. This built genuine problem-solving skills rather than simply handing me solutions. The admin team was also extremely responsive — managing all logistics, event schedules, and AWS sandbox account provisioning seamlessly, allowing me to focus entirely on learning and building.

**3. Relevance of Work to Academic Major**

As a Computer Science undergraduate, the FCAJ program aligned well with my academic foundations while filling critical practical gaps that university curricula rarely address in depth — particularly real-world serverless architecture patterns, Infrastructure as Code with AWS CDK, and applied Generative AI using Amazon Bedrock. Rather than isolated exercises covering individual topics, I worked on a complete end-to-end project from architecture design through to production deployment and monitoring — an experience that academic environments find very difficult to replicate authentically.

**4. Learning & Skill Development Opportunities**

Over 12 weeks, I went from having never used AWS CDK or Amazon Bedrock, to independently writing all Infrastructure as Code, integrating a full RAG pipeline, deploying a frontend on CloudFront secured with WAF, and configuring Amazon Cognito authentication. Beyond technical skills, I also improved soft skills: writing clear weekly progress reports, managing time across a multi-module project running in parallel, and articulating technical decisions during check-in sessions.

**5. Company Culture & Team Spirit**

The culture at FCAJ was highly positive and collaborative — everyone took their work seriously while keeping the atmosphere warm and encouraging. As deadlines approached each week, interns naturally supported one another, sharing solutions on the group channel as soon as someone figured something out. Despite being an intern, I genuinely felt like an integral part of the team rather than an outsider.

**6. Internship Policies / Benefits**

The program maintained a consistent, well-structured weekly cadence and provided timely learning materials and access to valuable internal training sessions and events. The AWS sandbox account was provisioned with a reasonable spending budget — I only spent $4.2 across the full 12 weeks by setting up AWS Budgets monitoring from Day 1 — allowing me to experiment freely without worrying about unexpected cost overruns.

---

### A Few Personal Reflections

Looking back on these 12 weeks, the moment I'll remember most vividly is the first time my chatbot correctly answered a question using internal company documents — a question about the company's leave policy, and the bot accurately cited the exact paragraph from the PDF uploaded to S3. After weeks of configuring the Bedrock Knowledge Base, debugging IAM permission errors, wrestling with Cognito JWT token types, and fixing CORS issues again and again — seeing the frontend, API Gateway, Lambda, Bedrock RAG, and DynamoDB all click together into a working product was a feeling I won't forget easily.

If I had to pick one memory that was equal parts satisfying and haunting, it would be the evening I spent chasing down a CORS error that simply wouldn't reveal itself. The culprit turned out to be that when a Lambda function threw an internal 500 error, it returned a response without any CORS headers — and the API Gateway Lambda Proxy Integration forwarded that raw response verbatim to the browser. Once I found the root cause and built a shared response helper module that always injects CORS headers regardless of status code, my understanding of Lambda Proxy Integration became far deeper than any amount of reading documentation could have achieved.

Without that support, I doubt I could have completed a full 12-week journey and delivered a working product. This was truly one of the most formative experiences of my time as a student — strengthening not only my AWS and AI technical foundation, but also showing me what a genuinely supportive and collaborative professional environment looks and feels like.
