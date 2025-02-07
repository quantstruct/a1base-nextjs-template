
# Documenting the AI Triage Logic

## Introduction

This document provides a comprehensive guide to the AI triage logic implemented in `lib/ai-triage/triage-logic.ts`. The AI triage system is responsible for analyzing incoming user messages and determining the appropriate response type. This document outlines the different response types, the criteria used to select them, how the system handles fallback scenarios, and how developers can customize the triage behavior. This is crucial for developers who want to customize or extend the agent's behavior.

## Response Types

The AI triage system can select from a variety of response types, each designed to handle different user needs. The specific response types available depend on the application's configuration, but common examples include:

*   **Knowledge Base Retrieval:**  The system searches a knowledge base for relevant information and provides it to the user.
*   **Intent Recognition & Task Execution:** The system identifies the user's intent (e.g., booking an appointment, checking order status) and executes the corresponding task.
*   **Handover to Human Agent:** The system determines that a human agent is required to handle the user's request.
*   **Clarification Request:** The system asks the user for more information to better understand their request.
*   **Confirmation:** The system confirms the user's request or action.
*   **Default Response:** A generic response used when no other response type is appropriate.

## Triage Criteria

The triage logic uses a combination of factors to determine the appropriate response type. These criteria are evaluated in a specific order to ensure consistent and predictable behavior. Key criteria include:

1.  **Keywords and Phrases:** The system identifies specific keywords or phrases in the user's message that indicate a particular intent or need.  For example, the presence of "appointment" and "book" might trigger the intent recognition for booking appointments.
2.  **Sentiment Analysis:** The system analyzes the sentiment of the user's message (positive, negative, neutral).  Negative sentiment might trigger a handover to a human agent.
3.  **Contextual Information:** The system considers the conversation history and user profile to understand the context of the message.  For example, if the user recently asked about order status, a follow-up question might be related to the same order.
4.  **Entity Recognition:** The system identifies specific entities in the user's message, such as dates, times, locations, or product names.  This information can be used to populate task parameters.
5.  **Regular Expressions:** The system uses regular expressions to match specific patterns in the user's message, such as phone numbers or email addresses.

The triage logic assigns a score to each response type based on how well it matches the triage criteria. The response type with the highest score is selected.

## Fallback Mechanisms

The AI triage system includes several fallback mechanisms to handle situations where it is unable to determine the appropriate response type. These mechanisms ensure that the user always receives a response, even if it is not the ideal one.

1.  **Default Response:** If no other response type meets a minimum score threshold, the system uses a default response. This response typically acknowledges the user's message and informs them that their request is being processed or that they will be contacted by a human agent.
2.  **Handover to Human Agent:** If the system detects a high level of frustration or confusion, it may automatically hand over the conversation to a human agent.
3.  **Clarification Request:** If the system is unsure about the user's intent, it may ask for more information. This allows the system to gather more context and improve its understanding of the user's needs.
4.  **Escalation Rules:**  Predefined rules can escalate specific types of requests to human agents based on factors like urgency or complexity.

## Customization

Developers can customize the AI triage logic to meet the specific needs of their application. This can be done by:

*   **Adding new response types:** Developers can create new response types to handle specific user needs.
*   **Modifying the triage criteria:** Developers can adjust the weights and thresholds of the triage criteria to fine-tune the system's behavior.
*   **Adding new keywords and phrases:** Developers can add new keywords and phrases to improve the system's ability to recognize user intents.
*   **Defining custom escalation rules:** Developers can define custom escalation rules to automatically hand over conversations to human agents based on specific criteria.
*   **Integrating with external services:** Developers can integrate the triage logic with external services, such as knowledge bases or task management systems.

Customization typically involves modifying the `lib/ai-triage/triage-logic.ts` file and potentially other configuration files.  It's crucial to thoroughly test any customizations to ensure they don't negatively impact the system's overall performance.

## Examples

The following examples illustrate how the AI triage system works in practice.

**Example 1:**

*   **User Message:** "I need to book an appointment for next Tuesday."
*   **Triage Decision:** Intent Recognition & Task Execution (Book Appointment). The system identifies the keywords "book" and "appointment" and extracts the date "next Tuesday" as an entity.

**Example 2:**

*   **User Message:** "My order hasn't arrived yet and it was supposed to be here yesterday!"
*   **Triage Decision:** Knowledge Base Retrieval (Order Tracking) or Handover to Human Agent (depending on sentiment analysis and configuration). The system identifies the keywords "order" and "hasn't arrived" and may perform sentiment analysis to detect negative sentiment.  If the sentiment is strongly negative, it might trigger a handover. Otherwise, it might retrieve order tracking information from a knowledge base.

**Example 3:**

*   **User Message:** "What is your return policy?"
*   **Triage Decision:** Knowledge Base Retrieval (Return Policy). The system identifies the keywords "return policy" and retrieves the relevant information from the knowledge base.

**Example 4:**

*   **User Message:** "asdfjkl;"
*   **Triage Decision:** Default Response. The system is unable to identify any relevant keywords or intents and falls back to the default response.

## Related Endpoints

The AI triage logic is typically triggered by incoming messages received through endpoints such as:

*   `/api/whatsapp/incoming`:  This endpoint (as seen in the provided OpenAPI spec) receives incoming messages from WhatsApp.  The data received at this endpoint is then passed to the triage logic for processing.
*   `/v1/emails/mailserver/incoming`: This endpoint receives incoming emails.

The specific endpoint that triggers the triage logic will depend on the messaging channels supported by the application.

## Code Snippets (Illustrative)

While the exact code is in `lib/ai-triage/triage-logic.ts`, the following is an illustrative example of how the triage logic might be structured:

```typescript
// Simplified example - not the actual code

interface TriageResult {
  responseType: string;
  confidence: number;
  data?: any;
}

function triageMessage(message: string, context: any): TriageResult {
  let bestResult: TriageResult = { responseType: "default", confidence: 0 };

  // Check for appointment booking intent
  if (message.toLowerCase().includes("book") && message.toLowerCase().includes("appointment")) {
    bestResult = { responseType: "book_appointment", confidence: 0.8 };
  }

  // Check for order status inquiry
  if (message.toLowerCase().includes("order") && message.toLowerCase().includes("status")) {
    bestResult = { responseType: "order_status", confidence: 0.7 };
  }

  // Fallback to default if no intent is recognized
  if (bestResult.confidence < 0.5) {
    bestResult = { responseType: "default", confidence: 0.2 };
  }

  return bestResult;
}

// Example usage
const userMessage = "I want to book an appointment.";
const triageDecision = triageMessage(userMessage, {});
console.log(triageDecision); // Output: { responseType: "book_appointment", confidence: 0.8 }
```

**Note:** This is a simplified example and does not represent the complete complexity of the actual triage logic.  Refer to the `lib/ai-triage/triage-logic.ts` file for the complete implementation.
