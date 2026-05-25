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

* **How it was built:** * Connected an LLM Chat Model (like OpenAI Chat Model) to the AI Agent node.
  * Added a Memory node (Window Buffer Memory) so the AI remembers the conversation context.
* **Input Mapping:** Inside the `Prompt` field, the user's dynamic message text was linked directly from the trigger using: `{{ $json.message.text }}`.
  
![Step 2 - AI Agent Configuration](./telegram-google-calendar-agent/images/agent_mapping.png)



#### Step 3: Linking the Google Calendar Tool
To allow the AI Agent to actually see your real-world appointments, it needs a specific tool attached to it.

* **How it was built:** A `Google Calendar` node was dragged and connected directly to the bottom of the AI Agent as a **Tool**.
* **Node Configuration:** * **Resource:** `Event`
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

---
