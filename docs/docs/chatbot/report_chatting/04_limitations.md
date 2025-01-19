# Limitations of Report Chatting

Author: `Ahmet Selman Güclü`

The report chatting functionality has proven to be a useful tool, allowing users to interact with reports in a dynamic and conversational way. However, there are several limitations in its current implementation that are worth noting:

## 1. Context Isolation

* **Report-Chat Exclusivity:** Each report chat is bound to a report. This means that report-specific conversations and general chats (involving other topics or reports) cannot coexist within the same chat session. This constraint can hinder users who wish to discuss or cross-reference multiple reports within one conversation.
* **Lack of Cross-Report Comparisons:** Since a report chat is limited to one report at a time, comparing two or more reports within the same chat is not feasible. Users would need to manually switch between chats or consolidate findings externally.

## 2. Contextual Limitations in Multi-Question Scenarios

* **Limited Contextual Retrieval:** When users pose multiple questions about different parts of a report within a single message, the system relies on specific sections as context for generating accurate responses. Currently, the similarity search mechanism retrieves up to two chunks of a report at a time. This constraint may result in incomplete or suboptimal answers for complex queries that reference a lot of sections simultaneously **within one single query**.
