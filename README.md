# SWYNEX-AI-Problem-Design
Narrow AI classification problem: automatically triages customer support tickets into priority levels (Urgent/High/Medium/Low) from raw ticket text. Includes problem definition, target user, data source, constraints, and evaluation criteria. Built for SWYNEX AI Problem Design task.
AI Problem Design
Customer Support Ticket Priority Classification

1. Problem Statement
Support teams receive a continuous stream of incoming tickets (emails, chat messages, web-form submissions) that must be triaged before an agent can act on them. Manual triage is slow, inconsistent between agents, and creates a real risk that urgent issues (outages, security concerns, angry high-value customers) sit in the same queue as routine questions and get resolved too late.
This project defines a narrow, practical classification problem: automatically assign each incoming support ticket a priority label so it can be routed and queued correctly, without a human having to read it first.
2. AI Use Case
Task type: Text classification (single-label, 4 classes)
Input: Raw ticket text: subject line + body (customer-written, unstructured)
Output: One priority label: Urgent, High, Medium, or Low
The model reads a ticket and predicts the priority class. This is intentionally scoped to classification only — it does not attempt to answer the ticket, summarize it, or route it to a specific team; those would be separate downstream steps built on top of this classifier.
3. Target User
Primary user: a support team lead or triage agent at a small-to-mid-size company (roughly 5–50 support tickets/day) who currently sorts incoming tickets by hand.
•	Goal: see the most urgent tickets at the top of the queue automatically, without reading every ticket first.
•	Constraint on the user: not technical — needs a priority label they can trust and act on immediately, not a probability score to interpret.
•	Secondary user: the agent who picks up a ticket, who benefits from consistent labeling instead of whatever the previous agent guessed.
4. Data Source
A small labeled dataset of historical support tickets, each with the text and the priority it was ultimately given by a human agent. For this project scope:
•	Size: 150–300 tickets — enough to show class balance and evaluate meaningfully, small enough to label and review by hand.
•	Source: a public support-ticket dataset (e.g., a Kaggle customer-support/helpdesk ticket dataset) relabeled into the 4 priority buckets, OR a synthetic-but-realistic set of tickets written to mimic real support language across the 4 categories.
•	Fields used: ticket subject, ticket body, and the priority label. Any other metadata (timestamps, customer ID) is ignored to keep the problem narrow.
Using a small, fixed dataset keeps the task tractable: it can be fully inspected, the labels can be sanity-checked by hand, and the evaluation split can be held out cleanly.
5. Constraints
•	Narrow scope: 4 fixed priority classes, no open-ended label set, no multi-label output.
•	Input length: tickets are short-to-medium text (roughly 20–300 words); very long attachments or thread histories are out of scope.
•	Language: English-only tickets for this version.
•	No external context: the model sees only the ticket text — no customer history, account tier, or SLA data, to keep the input surface simple and reproducible.
•	Latency: classification should be near-instant (sub-second) since it sits in front of a live queue.
•	Explainability: since a non-technical user acts on the output directly, the system should be able to show which words/phrases drove the priority call, not just the label.
6. Evaluation Approach
The labeled dataset is split into a training/reference set and a held-out test set (e.g., 80/20) that the model does not see before evaluation. On the test set:
Metric	Target	Why it matters
Overall accuracy	≥ 80%	Baseline sanity check across all 4 classes
Recall on “Urgent” class	≥ 90%	Missing an urgent ticket is the costliest error — recall matters more than precision here
Precision on “Urgent” class	≥ 70%	Keeps false alarms low enough that the label stays trustworthy
Confusion matrix (4x4)	reviewed manually	Shows exactly which priority levels get confused with which, not just an aggregate score

Success criteria for the project: the model must correctly catch the large majority of Urgent tickets (high recall on that class is non-negotiable, since a missed Urgent ticket is the failure mode that matters most in this use case), while keeping overall accuracy high enough that agents trust the automated label instead of re-checking every ticket themselves.
Qualitative check: in addition to the metrics above, a small sample (10–15 tickets) is reviewed by hand to confirm the predicted labels make sense to a human triager — a purely numeric score is not sufficient sign-off for a tool that a non-technical user will rely on.
7. Out of Scope
•	Automated ticket routing to specific teams or agents
•	Drafting or suggesting a reply to the customer
•	Multi-language support
•	Using customer account/CRM data as additional model input
