# automated-workflows-n8n

## **[AI-Powered Calendar Assistant via Telegram](./telegram-google-calendar-agent)**

A step-by-step documentation of building an autonomous AI Assistant using n8n that connects Telegram to Google Calendar. This assistant uses an AI Agent to understand natural language, check your calendar, and send your schedule back to you on Telegram.

<img width="1081" height="546" alt="image" src="https://github.com/user-attachments/assets/bfb2fd46-8d2c-4678-a59b-a45d46a8683d" />

### Step-by-Step Implementation

#### Step 1: Setting up the Telegram Trigger
The entry point of the workflow is to listen for any new message sent to the Telegram bot.

* **How it was built:** The bot was created using Telegram's `@BotFather` to get a secure API Token.
* **Node Configuration:** A `Telegram Trigger` node was added to n8n and configured with the bot credentials to listen for the `message` event.
  
![Step 1 - Telegram Trigger](./telegram-google-calendar-agent/images/step1_telegram_trigger.png)



#### Step 2: Configuring the AI Agent Engine
The core reasoning engine of this project is the AI Agent node, which decides how to handle the user's request.

* **How it was built:**
  * Connected an LLM Chat Model (like OpenAI Chat Model) to the AI Agent node.
* **Input Mapping:** Inside the `Prompt` field, the user's dynamic message text was linked directly from the trigger using: `{{ $json.message.text }}`.
  
![Step 2 - AI Agent Configuration](./telegram-google-calendar-agent/images/agent_mapping.png)



#### Step 3: Linking the Google Calendar Tool
To allow the AI Agent to actually see your real-world appointments, it needs a specific tool attached to it.

* **How it was built:** A `Google Calendar` node was dragged and connected directly to the bottom of the AI Agent as a **Tool**.
* **Node Configuration:**
  * **Resource:** `Event`
  * **Operation:** `Get Many`
  * **Calendar ID:** Linked to your primary email address (`ansamjanajreh@gmail.com`).
  * **Tool Description:** Changed to *Set Manually* with a clear description so the AI knows exactly when to call it: *"Use this tool to fetch, retrieve, or list the user's calendar events from Google Calendar."*
    
![Step 3 - Google Calendar Tool](./telegram-google-calendar-agent/images/google_calendar_tool.png)



#### Step 4: Routing the Final Response via Telegram
Once the AI Agent calls the calendar and fetches the data, the final step is to send that formulated answer back to your chat.

* **How it was built:** A regular Telegram node (`Send a text message` operation) was placed at the very end of the workflow.
* **Node Configuration:**
  * **Chat ID:** Mapped dynamically using expressions to match your personal chat ID: `{{ $('Telegram Trigger').item.json.message.chat.id }}`.
  * **Text:** Mapped to output the AI's final response: `{{ $json.output }}`.
    
![Step 4 - Telegram Send Message](./telegram-google-calendar-agent/images/telegram_output_node.png)

---

### Live Testing & Results

#### 1. Sending the First Query
Testing began by activating the workflow using **Execute workflow** and sending a natural language prompt via Telegram:

> *"whats my appointments for this month ?"*

![Testing - User Prompt](./telegram-google-calendar-agent/images/telegram_query.png)

#### 2. Successful Workflow Execution
The entire canvas successfully executed in real-time. Every single node turned green, indicating perfect data routing, successful API handshakes, and proper execution loops.

![Testing - Full Workflow Success](./telegram-google-calendar-agent/images/workflow_full.png)

#### 3. Data Payload Inspection (Behind the Scenes)
Before the AI Agent formats the response, we can inspect the raw data payload returned from the Google Calendar API inside n8n. This confirms that the system is successfully fetching and parsing structured JSON data directly from the server.

![Testing - JSON Output Payload](./telegram-google-calendar-agent/images/json_payload_output.png)

#### 4. Final AI Response Received
The AI successfully called the calendar, processed the structural events data, and returned a cleanly formatted summary directly to the chat interface.

![Testing - AI Final Reply](./telegram-google-calendar-agent/images/telegram_response.png)


### Live Demo
The AI Agent is fully active and listening in production. You can test this intelligent scheduling assistant live by opening a direct chat window with the active Telegram bot link below:

  **[Chat Live with the Calendar Assistant Bot](https://t.me/ansam65_bot)**


---


## **[AI-Powered Incident Management & Automation System](./Automated%20DevOps%20&%20IT%20Support%20Ticketing%20Hub%20via%20Slack)**

An enterprise-grade, end-to-end automated incident response and classification pipeline built with n8n. The system monitors Slack for production bugs or technical issues reported by users or automated QA smoke tests, leverages Generative AI to analyze and structure the raw context, registers tracking tickets inside Trello, and routes highly contextualized confirmation replies back to specific Slack threads via an intelligent conditional architecture.

![full_workflow](./Automated%20DevOps%20&%20IT%20Support%20Ticketing%20Hub%20via%20Slack/images/full_workflow.png)


### Step-by-Step Implementation

#### Step 1: Automated Incident Ingress (Slack Trigger)
The workflow kicks off dynamically the moment a team member or a testing framework reports a bug entry inside a monitored Slack workspace channel.

* **How it was built:** A `Slack Trigger` node was deployed to listen live for new app mentions or specific message channel events.
* **Data Captured:** Extracts raw text, client message IDs, user details, and the unique timestamp string (`ts`) to initiate context tracking.

![Step 1 - Slack Event Trigger](./Automated%20DevOps%20&%20IT%20Support%20Ticketing%20Hub%20via%20Slack/images/step1_slack_trigger.png)

#### Step 2: Extracting Intelligence (AI Agent Configuration)
The core reasoning and classification layer relies on an advanced AI Agent node connected to a Large Language Model to structure messy human reporting.

* **How it was built:** Connected an LLM Chat Model (OpenAI Chat Model) to the AI Agent node to provide semantic analytical capabilities.
* **Input Mapping:** Inside the agent prompt, the dynamic message text payload from the channel is bound using expressions: `{{ $json.text }}`.
* **Structured Output:** The agent parses the text and standardizes it into distinct key variables: `category`, `urgency`, and an engineer-friendly `summary`.

![Step 2 - AI Agent & OpenAI Model](./Automated%20DevOps%20&%20IT%20Support%20Ticketing%20Hub%20via%20Slack/images/step2_ai_agent.png)

#### Step 3: URL Parameter Normalization via JavaScript
To facilitate bidirectional hyperlinking between platforms, a dedicated data transformation layer is injected.

* **How it was built:** A custom JavaScript `Code Node` parses runtime parameters.
* **The Operation:** It safely isolates the workspace metadata and performs string operations on the message timestamp string (`{{ $json.ts.replace('.', '') }}`) to manually construct a full, clickable browser URL (`messageUrl`) pointing directly back to the original Slack context.

![Step 3 - JavaScript Context Normalization](./Automated%20DevOps%20&%20IT%20Support%20Ticketing%20Hub%20via%20Slack/images/step3_js_code.png)

#### Step 4: Ledger Registration (Trello Ticket Creation)
Once the metadata is fully enriched, the issue must be officially registered into the engineering team's project board backlog.

* **How it was built:** A `Trello` node is integrated into the linear pipeline using a dedicated List ID mapping configuration.
* **Payload Customization:**
  * **Card Name:** Generated dynamically with the format: `[{{ $json.urgency }}] - {{ $json.category }}: {{ $json.summary }}`.
  * **Description:** Injected with structured markdown text including the AI summary and a direct embedded context link: `[View Original Message]({{ $json.messageUrl }})`.

![Step 4 - Trello Ticket Generation](./Automated%20DevOps%20&%20IT%20Support%20Ticketing%20Hub%20via%20Slack/images/step4_trello_node.png)

#### Step 5: Deterministic Pipeline Splitting (If Node Logic)
To separate emergency critical response loops from standard logging tasks, we execute a localized condition router immediately after database registration.

* **The Rule:** An `If Node` evaluates the normalized `{{ $json.urgency }}` parameter.
* **The Filter Criteria:** Checks whether the string equals `high` or `critical`.
* **The Paths:**
  * **True Branch:** Instantly flags high-risk failures for immediate engineering team alert.
  * **False Branch:** Diverts standard operations (Medium/Low) into passive tracking queues.

![Step 5 - Conditional If Router](./Automated%20DevOps%20&%20IT%20Support%20Ticketing%20Hub%20via%20Slack/images/step5_if_logic.png)

---

### Live Testing & Results

### 1. Linear Workflow Execution Success
When incident traces are simulated, data flows linearly through the primary integrations and splits perfectly down to the dedicated Slack communication endpoints without losing upstream payload schemas or triggering undefined object references.

![Testing - Full Linear Workflow Success](./Automated%20DevOps%20&%20IT%20Support%20Ticketing%20Hub%20via%20Slack/images/full_workflow.png)

---

### 2. Dual-Route Communication Output (Slack Live Threads Verification)
Depending on the urgency evaluation executed by the `If` router, the bot safely responds inside the exact thread where the bug was born to maintain tidy workspaces.

#### Case A: Emergency Alerts Ingress (True Branch Output)
* **The Context:** An incident categorized as `High` or `Critical` runs successfully through the system.
* **The Response:** The node (`Send a message`) targets the active channel thread, issuing an on-call response format emphasizing immediate action and printing the newly generated short tracking link.

Slack Ingress & Bot Response Thread
When a user reports a critical issue, the automated system captures it and the bot instantaneously threads a structured confirmation containing the direct Trello shortcut back into the channel workspace.

Dynamic Trello Card Generation
Simultaneously, the payload triggers an instant ledger write, logging the incident under the high-priority board list with comprehensive cross-referencing parameters populated.

---


## **[Automated E-commerce Order Processing System](./Automated%20E-commerce%20Order%20Processing%20System)**

An end-to-end automated pipeline built with **n8n** to streamline e-commerce order workflows. This system listens for customer form submissions, standardizes raw data fields using custom **JavaScript**, implements selective business routing thresholds, triggers dynamic email responses via **Gmail**, and logs all activities inside a secure database backend via **Google Sheets**.

<img width="1527" height="454" alt="image" src="https://github.com/user-attachments/assets/10dbec77-1040-4339-a6ae-39f0988e8f82" />

### Step-by-Step Implementation

#### Step 1: Customer Order Ingress (Form Submission)
The workflow kicks off the moment a transaction entry or client checkout profile arrives at our primary webhook listener.

* **The Process:** The consumer fills out an order checkout interface capturing key fields: *Full Name*, *Email Address*, *Phone Number*, *Country*, and the total *Order Total*.

<p align="center">
  <img src="./Automated%20E-commerce%20Order%20Processing%20System/images/step1_order_form.png" width="40%" alt="Order Form UI" />
  &nbsp;&nbsp;&nbsp;&nbsp;
  <img src="./Automated%20E-commerce%20Order%20Processing%20System/images/form%20Trigger.png" width="48%" alt="n8n Form Trigger Node" />
</p>


#### Step 2: Data Standardization via JavaScript
To normalize inconsistent data inputs (such as mixed casing, leading whitespaces, or missing keys) before database write operations, a custom script engine is injected.
* **The Execution:** Using a dedicated **Code Node** loaded with a script layout, the workflow cleans client email records, parses numeric values safely, and exports structured variables.
  
 ![Step 2 - Custom JavaScript Normalization](./Automated%20E-commerce%20Order%20Processing%20System/images/step2_js_cleaning.png)


 #### Step 3: Dynamic Field Mapping
Following data cleanup operations, the structured object parameters must be explicitly instantiated into the localized operational context.
* **The Process:** An **Edit Fields** configuration maps variables cleanly (e.g., parsing raw `firstName` or formatting time metadata into a standardized `submittedAt` timestamp string).
  
![Step 3 - Localized Value Mapping](./Automated%20E-commerce%20Order%20Processing%20System/images/step3_field_mapping.png)


#### Step 4: Business Logic & Threshold Filtering
To create differential client journeys, we execute an automated split router based on active spending targets.
* **The Rule:** An **If Node** evaluates incoming values to check if `{{ $json.orderTotal }}` is **greater than or equal to 100**.
* **The Split Path:**
  * **True Branch:** Routes orders $\ge 100$ to premium communication pathways.
  * **False Branch:** Routes low-threshold orders through standard operational processing.
    
![Step 4 - Conditional Router Pipeline](./Automated%20E-commerce%20Order%20Processing%20System/images/step4_if_logic.png)

---

### Live Testing & Results

#### 1. Unified Workflow Architecture View
When tests are fired across the staging environment, the engine tracks complete trace passes without systemic runtime failures, illuminating all active routes in green.

![Step 4 - Conditional Router Pipeline](./Automated%20E-commerce%20Order%20Processing%20System/images/step4_if_logic.png)


#### 2. Dual-Route Communication Output (Gmail Live Verification)
Depending on which value profile matches the filter constraint rules established above, the system dispatches highly specialized, reactive email confirmation receipts to the respective buyers.

#### Case A: Standard Order Routing (False Branch Output)
* **The Context:** When user *Sana Taher* completed an order total value of **$85** (failing the $\ge 100$ test constraint), her payload dropped into the secondary node pool (`Send a message1`). 
* **The Output:** A standard receipt copy was generated summarizing her shipment parameters to the destination country safely.

![Case A - Standard Invoice Email Summary](./Automated%20E-commerce%20Order%20Processing%20System/images/standard_receipt_email.png)

#### Case B: VIP Premium Rewards Order Routing (True Branch Output)
* **The Context:** When user *Ansam Janajreh* submitted a transactional order total value of **$210**, the core system verified it passed our business criteria and shifted her straight to the premium node pool (`Send a message`).
* **The Output:** An exclusive customer validation receipt was dispatched containing a special reward discount tag (`VIP20`) thanking her for her premium engagement level.

![Case B - VIP Customer Promo Code Email Summary](./Automated%20E-commerce%20Order%20Processing%20System/images/vip_reward_email.png)



#### 3. Ledger Database Synchronization (Google Sheets Backend Log)
Simultaneously, at the final step, both system routes converge to append and serialize all critical transactional information records seamlessly into a unified storage system tracker spreadsheet.

![Database Synchronized Rows Log](./Automated%20E-commerce%20Order%20Processing%20System/images/google_sheets_live_database.png)


### Live Demo
You can test this automated pipeline live by submitting a mock order through the production form link below:

  **[Try the Live E-commerce Order Form](https://ansam65.app.n8n.cloud/form/d73ae766-abc0-477f-bf31-3b972c78db4c)**
