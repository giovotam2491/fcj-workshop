---
title: "Week 8 Worklog"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:
* Optimize the RAG pipeline by tuning search configurations and metadata.
* Deploy a short-term conversation session state mechanism so the chatbot remembers prior user interactions.
* Author an automated test script to evaluate response accuracy and mitigate LLM hallucinations.
* Convert S3 data source formats to structured Markdown files to increase document chunking accuracy.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Research search configuration adjustments: modify the `numberOfResults` parameter (increasing from 3 to 5 chunks) to feed more relevant context to the LLM. | 08/06/2026 | 08/06/2026 | [Bedrock Search Configuration](https://docs.aws.amazon.com/bedrock/) |
| Tue | - Understand and configure the `sessionId` parameter in the `RetrieveAndGenerate` API. <br> - Enable Bedrock to manage conversation states directly on AWS rather than manually passing history in prompts. | 09/06/2026 | 09/06/2026 | [Bedrock Session Management](https://docs.aws.amazon.com/bedrock/) |
| Wed | - Draft a test suite containing 20 representative queries (including 5 trick questions and 5 questions out of the document scope). <br> - Write a Node.js script to call the `/chat` API automatically and log outputs to a JSON file. | 10/06/2026 | 10/06/2026 | Developer Notes |
| Thu | - Execute the evaluation script, analyze accurate response rates, and identify hallucination occurrences. <br> - Adjust the System Prompt Template to apply strict language constraints and knowledge boundaries. | 11/06/2026 | 11/06/2026 | RAG Evaluation Logs |
| Fri | - Convert sample raw PDF scans to clean, structured Markdown documents (using clear headings and lists) to help OpenSearch chunk content more effectively. <br> - Synchronize S3 data sources and write the Week 8 worklog. | 12/06/2026 | 12/06/2026 | Company Docs |

### Knowledge Gained This Week:
* **Prompt Engineering for RAG:** Learned how to build highly restrictive system prompts (defining bot roles and instructions on how to handle missing data) to tightly govern LLM behaviors.
* **Session State Management:** Leveraged Bedrock Runtime's native `sessionId` parameter to allow AWS to store conversation histories optimized for tokens.
* **Input Data Quality:** Realized how structured data formats (like Markdown with clear structural layouts) yield significantly better vector search match results than unformatted scanned documents.

### Challenges & Solutions:
* **Challenge:** The LLM occasionally suffered from hallucinations, generating plausible but incorrect answers using its general pre-trained knowledge when users asked questions outside the company's document scope.
* **Solution:** Rewrote the custom prompt template configuration, adding strict compliance directives:
  "You are a helpful company assistant. You MUST ONLY use the provided reference documents to answer the question. If the information is not present in the references, you MUST reply verbatim: 'Tôi xin lỗi, thông tin này không có trong tài liệu nội bộ công ty.'. Do not assume or fabricate any facts. Answers must be in Vietnamese."
  After updating the prompt, hallucination rates dropped to 0% in test runs.

### Week 8 Achievements:
* Optimized Bedrock retrieval precision and relevance.
* Successfully implemented conversation history memory using Bedrock Session IDs.
* Eradicated hallucination errors via prompt engineering constraints.
* Converted document storage files to Markdown, improving vector search accuracy.
