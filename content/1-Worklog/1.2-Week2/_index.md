---
title: "Week 2 Worklog"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:
* Design a detailed IAM Roles and Access Policies matrix for the entire system following the Principle of Least Privilege.
* Research Amazon Cognito User Pool and Client App configuration for user authentication.
* Develop a basic layout for the Chatbot (Chat Layout) using React + Tailwind CSS.
* Integrate initial GSAP Timeline animation effects for the Sidebar and chat panels.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Draft the IAM Roles matrix: define specific Lambda Execution Roles, permissions for S3 buckets, and DynamoDB tables. <br> - Differentiate between trust policies and permission policies. | 27/04/2026 | 27/04/2026 | [IAM Policies and Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/) |
| Tue | - Research the architecture of Amazon Cognito User Pool. <br> - Understand how ID tokens, Access tokens, and Refresh tokens verify user identity at the API Gateway level. | 28/04/2026 | 28/04/2026 | [Amazon Cognito Developer Guide](https://docs.aws.amazon.com/cognito/) |
| Wed | - Build the core Frontend interface: Sidebar (conversation list), Main Chat Window, and TextInput container. <br> - Use Tailwind CSS to ensure a responsive design across devices. | 29/04/2026 | 29/04/2026 | [Tailwind CSS Documentation](https://tailwindcss.com/docs) |
| Thu | - Install the `@gsap/react` wrapper. <br> - Program initial GSAP animations for the sliding sidebar and a fade-in effect for newly sent messages. | 30/04/2026 | 30/04/2026 | [GSAP React Integration](https://gsap.com/resources/react/) |
| Fri | - Test animation performance and debug GSAP conflicts caused by React state re-rendering. <br> - Consolidate and write the Week 2 worklog. | 01/05/2026 | 01/05/2026 | Intern Worklog |

### Knowledge Gained This Week:
* **IAM Matrix Design:** Understood the trust relation configuration (AssumeRole) and IAM Policy structure. Learned to restrict permissions using specific Resource ARNs rather than wildcards.
* **Cognito Authentication:** Differentiated between Cognito User Pools (user management) and Identity Pools (AWS temporary credential provisioning).
* **GSAP with React:** Mastered managing animation lifecycles in React components using the `useGSAP` hook, preventing memory leaks and re-render glitching.

### Challenges & Solutions:
* **Challenge:** The GSAP Sidebar sliding animation was jittery and re-triggered from the beginning every time the user typed in the chat input (due to React state updates on the input text forcing a re-render of the parent component).
* **Solution:** Used the `useGSAP` hook combined with `useRef` to scope the animation context. Defined a static timeline that only triggers when the user explicitly clicks the "Toggle Sidebar" button, removing volatile state variables from the animation dependency array.

### Week 2 Achievements:
* Completed the Least Privilege-compliant IAM access matrix for all backend services.
* Created the responsive Frontend Chatbot layout.
* Successfully implemented smooth GSAP animations for the sidebar toggle and messaging transitions.
