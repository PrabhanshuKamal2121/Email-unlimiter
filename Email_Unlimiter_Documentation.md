# Email Unlimiter Documentation

## Introduction

**Email Unlimiter** is a sophisticated automation workflow built using n8n, designed to streamline personalized email outreach at scale. This workflow integrates data from Google Sheets, conducts web-based company research, leverages AI for content generation, and sends tailored emails via Gmail. It is ideal for professionals in sales, marketing, or business development who aim to connect with potential clients or partners efficiently and effectively. By automating the research and email crafting process, Email Unlimiter saves time while ensuring each message is professional, relevant, and personalized.

## Prerequisites

To use this workflow, ensure the following are in place:

- **n8n Instance**: A running instance of n8n, compatible with the node versions specified (e.g., `n8n-nodes-base` v4.6, `@n8n/n8n-nodes-langchain` v1.2).
- **Google Sheet**: A spreadsheet containing target data with columns: `COMPANY NAME`, `TYPE OF COMPANY`, `ADDRESS`, `COMPANY ID`, `NAME`, `DESIGNATION`. The default sheet ID is `1SHV8PbV3-2lyYwss0w_3EsQUO7HdG6NSJ2jcu0bO_Ew`, Sheet1 (`gid=0`).
- **API Credentials**:
  - **Google Sheets OAuth2**: For accessing the spreadsheet.
  - **Gmail OAuth2**: For sending emails.
  - **OpenAI API**: For AI models like `gpt-4.1-mini` and `gpt-4.1-nano`.
  - **Ollama API**: For models like `mistral:7b` and `llama3.1:8b`.
- **Web Search Service**: A running service at `http://host.docker.internal:8089/search` (or a substitute API) for web research. This may require a custom setup or third-party service.
- **Placeholders**: Replace `[your email]` and `[senders details]` in the "Send a message" node with your actual email address and sender information.

**Ethical Note**: Ensure compliance with email regulations (e.g., CAN-SPAM Act). Obtain consent from recipients and respect unsubscribe requests.

## Workflow Overview

The Email Unlimiter workflow automates the following steps:

1. **Manual Trigger**: Initiates the workflow via the "Execute Workflow" button.
2. **Data Retrieval**: Fetches company and contact data from a Google Sheet.
3. **Batch Processing**: Loops through each row of data in manageable batches.
4. **Web Research**: Performs internet searches to gather company insights.
5. **AI Content Generation**: Uses AI models to create personalized email content based on research and predefined prompts.
6. **Content Cleaning**: Processes AI outputs to remove unwanted formatting.
7. **Email Crafting**: Generates the email body and subject.
8. **Email Sending**: Dispatches emails via Gmail.
9. **Completion Notification**: Sends a summary email upon finishing all tasks.

## Detailed Node Explanations

### Data Fetching

- **Get row(s) in sheet1**
  - **Type**: `n8n-nodes-base.googleSheets` (v4.6)
  - **Purpose**: Retrieves all rows from the specified Google Sheet.
  - **Parameters**:
    - `documentId`: `1SHV8PbV3-2lyYwss0w_3EsQUO7HdG6NSJ2jcu0bO_Ew`
    - `sheetName`: `Sheet1` (`gid=0`)
  - **Credentials**: Google Sheets OAuth2 API
  - **Output**: JSON array of rows with columns like `COMPANY NAME`, `TYPE OF COMPANY`, etc.

### Trigger

- **When clicking ‘Execute workflow’**
  - **Type**: `n8n-nodes-base.manualTrigger` (v1)
  - **Purpose**: Starts the workflow manually when the user clicks "Execute Workflow" in n8n.
  - **Output**: Triggers the next node without additional data.

### Web Research

- **web_search**
  - **Type**: `n8n-nodes-base.httpRequestTool` (v4.2)
  - **Purpose**: Conducts web searches to gather company information for AI processing.
  - **Parameters**:
    - `url`: `http://host.docker.internal:8089/search`
    - `queryParameters`:
      - `q`: Dynamic search query from AI (`$fromAI('parameters0_Value', '', 'string')`)
      - `format`: `json`
    - `toolDescription`: "Web search - searches the internet, for research purpose."
  - **Options**: Configured for full response and error handling.
  - **Retry**: Up to 5 tries, 100ms wait between attempts.
  - **Output**: JSON response from the search service.

### AI Processing

#### Language Models

- **OpenAI Chat Model**
  - **Type**: `@n8n/n8n-nodes-langchain.lmChatOpenAi` (v1.2)
  - **Purpose**: Provides GPT-4.1-mini for the `company_research` agent.
  - **Parameters**: `model`: `gpt-4.1-mini`
  - **Credentials**: OpenAI API

- **OpenAI Chat Model1**
  - **Type**: `@n8n/n8n-nodes-langchain.lmChatOpenAi` (v1.2)
  - **Purpose**: Provides GPT-4.1-mini for the `AI Agent1` (email body refinement).
  - **Parameters**: `model`: `gpt-4.1-mini`
  - **Credentials**: OpenAI API

- **OpenAI Chat Model2**
  - **Type**: `@n8n/n8n-nodes-langchain.lmChatOpenAi` (v1.2)
  - **Purpose**: Provides GPT-4.1-nano for the `AI Agent2` (subject generation).
  - **Parameters**: `model`: `gpt-4.1-nano`
  - **Credentials**: OpenAI API

- **Ollama Chat Model**
  - **Type**: `@n8n/n8n-nodes-langchain.lmChatOllama` (v1)
  - **Purpose**: Provides Mistral:7b for the `common` agent.
  - **Parameters**: `model`: `mistral:7b`
  - **Credentials**: Ollama API

- **Ollama Chat Model1**
  - **Type**: `@n8n/n8n-nodes-langchain.lmChatOllama` (v1)
  - **Purpose**: Provides Mistral:7b for the `impressive_things` agent.
  - **Parameters**: `model`: `mistral:7b`
  - **Credentials**: Ollama API

- **Ollama Chat Model2**
  - **Type**: `@n8n/n8n-nodes-langchain.lmChatOllama` (v1)
  - **Purpose**: Provides Mistral:7b for the `better_than` agent.
  - **Parameters**: `model`: `mistral:7b`
  - **Credentials**: Ollama API

- **Ollama Chat Model3**
  - **Type**: `@n8n/n8n-nodes-langchain.lmChatOllama` (v1)
  - **Purpose**: Provides Llama3.1:8b for the `AI Agent` (initial email body).
  - **Parameters**: `model`: `llama3.1:8b`
  - **Credentials**: Ollama API

#### AI Agents

- **company_research**
  - **Type**: `@n8n/n8n-nodes-langchain.agent` (v2)
  - **Purpose**: Researches the target company and organizes findings into a JSON structure for email content.
  - **Prompt**: Instructs the agent to act as a corporate research assistant, gathering procurement-related data under five headings: Personalized Hook, Sourcing & Procurement Insights, Business Approach & Values, Challenges & Opportunities, Key Contacts or Roles.
  - **Key Instructions**:
    - Uses `web_search` tool.
    - Outputs concise bullet points with source URLs.
    - Limits output to 250 words in JSON format.
  - **Inputs**: Company metadata from Google Sheet (`COMPANY NAME`, `TYPE OF COMPANY`, `ADDRESS`, `COMPANY ID`).
  - **Output**: JSON object with research findings.

- **common**
  - **Type**: `@n8n/n8n-nodes-langchain.agent` (v2)
  - **Purpose**: Identifies commonalities between the target company and your company.
  - **Prompt**: Lists common aspects without a word limit.
  - **Inputs**: `COMPANY NAME` and cleaned research output from `Code`.
  - **Output**: Text list of commonalities.

- **impressive_things**
  - **Type**: `@n8n/n8n-nodes-langchain.agent` (v2)
  - **Purpose**: Highlights impressive aspects of the target company.
  - **Prompt**: Finds notable achievements or qualities.
  - **Inputs**: `COMPANY NAME` and cleaned research output from `Code`.
  - **Output**: Text list of impressive features.

- **better_than**
  - **Type**: `@n8n/n8n-nodes-langchain.agent` (v2)
  - **Purpose**: Analyzes why the target company excels over your company and lessons to learn.
  - **Prompt**: Lists 20-30 bullet points without a word limit.
  - **Inputs**: `COMPANY NAME`, cleaned research output from `Code`, and your company details.
  - **Output**: Text list of comparisons and lessons.

- **AI Agent**
  - **Type**: `@n8n/n8n-nodes-langchain.agent` (v2)
  - **Purpose**: Generates the initial email body (91-100 words).
  - **Prompt**: Creates a professional, non-salesy outreach email using company data and insights.
  - **Inputs**: `NAME`, `DESIGNATION`, `COMPANY NAME`, `TYPE OF COMPANY`, and outputs from `Code`, `Code1`, `Code2`, `Code3`.
  - **Output**: Plain text email body.

- **AI Agent1**
  - **Type**: `@n8n/n8n-nodes-langchain.agent` (v2)
  - **Purpose**: Refines the initial email body (90-95 words) in HTML format.
  - **Prompt**: Improves the email, avoiding negative words, with clean formatting using `<p>` tags.
  - **Inputs**: Output from `AI Agent` plus all prior data.
  - **Output**: HTML-formatted email body.

- **AI Agent2**
  - **Type**: `@n8n/n8n-nodes-langchain.agent` (v2)
  - **Purpose**: Crafts a concise email subject based on the refined body.
  - **Prompt**: Generates a subject inspired by examples (not provided in JSON).
  - **Inputs**: Output from `AI Agent1`.
  - **Output**: Plain text subject line.

### Content Cleaning

- **Code, Code1, Code2, Code3**
  - **Type**: `n8n-nodes-base.code` (v2)
  - **Purpose**: Cleans AI outputs by removing unwanted characters and formatting.
  - **Code**:
    ```javascript
    const rawOutput = $input.first().json.output;

    const cleanedOutput = rawOutput
      .replace(/<think>[\s\S]*?<\/think>/gi, '') // Remove <think>...</think>
      .replace(/\*\*/g, '')                      // Remove double asterisks
      .replace(/\*/g, '')                        // Remove single asterisks
      .replace(/\\n/g, ' ')                      // Remove literal \n
      .replace(/\n/g, ' ')                       // Remove actual newlines
      .replace(/\//g, '')                        // Remove forward slashes
      .replace(/"/g, '')                         // Remove double quotes
      .replace(/'/g, '')                         // Remove single quotes
      .replace(/#/g, '')                         // Remove hash symbols
      .replace(/\s+/g, ' ')                      // Collapse multiple spaces
      .trim();

    return [{ json: { output: cleanedOutput } }];
    ```
  - **Output**: Cleaned text string.

- **Code4**
  - **Type**: `n8n-nodes-base.code` (v2)
  - **Purpose**: Removes newlines from `AI Agent1` output.
  - **Code**:
    ```javascript
    const rawInput = $input.first().json.output;

    const cleanedOutput = rawInput
      .replace(/\n\n/g, '')   // Remove double newlines
      .replace(/\n/g, '');    // Remove single newlines

    return [{ json: { output: cleanedOutput } }];
    ```
  - **Output**: Cleaned text string.

- **Code5**
  - **Type**: `n8n-nodes-base.code` (v2)
  - **Purpose**: Formats the email with a greeting and processes newlines.
  - **Code**:
    ```javascript
    const name = $('Get row(s) in sheet1').first().json.NAME || '';
    const rawMessage = $('Code4').first().json.output || '';
    const formattedMessage = rawMessage.replace(/\\n/g, "\n");
    const greeting = name.trim() ? `Dear ${name},` : "Hi,";

    return [{ json: { greeting: greeting, message: formattedMessage } }];
    ```
  - **Output**: JSON with `greeting` and `message`.

### Email Sending

- **Send a message**
  - **Type**: `n8n-nodes-base.gmail` (v2.1)
  - **Purpose**: Sends the personalized email to the recipient.
  - **Parameters**:
    - `sendTo`: `[your email]` (replace with actual recipient email)
    - `subject`: Dynamic from `AI Agent2`
    - `message`: `<p>{{ $json.greeting}}</p>\n{{ $json.message }}\n\n<p>Warm regards,</p>\n\n<p>[senders details]</p>`
  - **Credentials**: Gmail OAuth2 API
  - **Output**: Confirmation of email sent.

- **Send a message1**
  - **Type**: `n8n-nodes-base.gmail` (v2.1)
  - **Purpose**: Sends a completion notification.
  - **Parameters**:
    - `sendTo`: `[your email]` (replace with your email)
    - `subject`: "sent all the emails"
    - `message`: "hurray"
  - **Credentials**: Gmail OAuth2 API
  - **Output**: Confirmation of notification sent.

### Control Flow

- **Loop Over Items**
  - **Type**: `n8n-nodes-base.splitInBatches` (v3)
  - **Purpose**: Processes Google Sheet rows in batches.
  - **Output**: Splits data into two paths: processing loop and completion.

- **Wait**
  - **Type**: `n8n-nodes-base.wait` (v1.1)
  - **Purpose**: Introduces a delay between iterations to manage API rate limits.
  - **Output**: Passes data after a pause.

- **Limit1**
  - **Type**: `n8n-nodes-base.limit` (v1)
  - **Purpose**: Ensures all items are processed before triggering the completion email.
  - **Output**: Aggregates looped data.

- **Think1**
  - **Type**: `@n8n/n8n-nodes-langchain.toolThink` (v1)
  - **Purpose**: Assists `company_research` with reasoning (no specific output).

## Connections and Data Flow

The workflow follows this sequence:

1. **When clicking ‘Execute workflow’** → **Get row(s) in sheet1** → **Loop Over Items**
2. **Inside Loop**:
   - **Wait** → **company_research** (uses `web_search`, `OpenAI Chat Model`, `Think1`) → **Code**
   - **Code** → **common** (uses `Ollama Chat Model`) → **Code1**
   - **Code1** → **impressive_things** (uses `Ollama Chat Model1`) → **Code2**
   - **Code2** → **better_than** (uses `Ollama Chat Model2`) → **Code3**
   - **Code3** → **AI Agent** (uses `Ollama Chat Model3`) → **AI Agent1** (uses `OpenAI Chat Model1`) → **Code4**
   - **Code4** → **AI Agent2** (uses `OpenAI Chat Model2`) → **Code5**
   - **Code5** → **Send a message** → Back to **Loop Over Items**
3. **After Loop**:
   - **Loop Over Items** → **Limit1** → **Send a message1**

**Simplified Flow Diagram**:

```
[Trigger] → [Google Sheet] → [Loop]
  ↓ Inside Loop:
  [Wait] → [Research] → [Clean] → [Common] → [Clean] → [Impressive] → [Clean] → [Better] → [Clean]
  ↓
  [Email Body] → [Refine Body] → [Clean] → [Subject] → [Format] → [Send Email] → [Loop Back]
  ↓ Outside Loop:
  [Limit] → [Completion Email]
```

## Usage Instructions

1. **Prepare Your Google Sheet**:
   - Populate with columns: `COMPANY NAME`, `TYPE OF COMPANY`, `ADDRESS`, `COMPANY ID`, `NAME`, `DESIGNATION`.
   - Update `documentId` and `sheetName` in "Get row(s) in sheet1" if using a different sheet.

2. **Set Up Credentials**:
   - Configure OAuth2 for Google Sheets and Gmail in n8n.
   - Add API keys for OpenAI and Ollama.

3. **Configure Email Settings**:
   - Replace `[your email]` in "Send a message" and "Send a message1" with your email addresses.
   - Update `[senders details]` with your signature.

4. **Customize Prompts** (Optional):
   - Adjust AI agent prompts to align with your outreach goals.

5. **Execute the Workflow**:
   - Open n8n, load the workflow, and click "Execute Workflow".
   - Monitor progress in the n8n interface.

6. **Verify Output**:
   - Check sent emails in your Gmail account.
   - Confirm the completion email is received.

## Customization Options

- **Change Data Source**:
  - Modify `documentId` and `sheetName` in "Get row(s) in sheet1" to use a different sheet.
  - Adjust column names in expressions if your sheet uses different headers.

- **Adjust AI Prompts**:
  - Edit `text` and `systemMessage` in AI agent nodes (e.g., `company_research`, `AI Agent`) to change tone, focus, or content.

- **Modify Cleaning Logic**:
  - Update JavaScript in `Code` nodes to handle different formatting needs (e.g., preserve certain characters).

- **Scale Processing**:
  - Adjust batch size in "Loop Over Items" via n8n settings for larger datasets.
  - Increase `waitBetweenTries` in `web_search` or add delay in `Wait` for API limits.

- **Enhance Email Format**:
  - Alter the HTML structure in "Send a message" for a different layout or styling.

## Troubleshooting

- **API Rate Limits**:
  - **Symptom**: Workflow pauses or fails with rate limit errors.
  - **Solution**: Increase delay in "Wait" node or reduce batch size in "Loop Over Items".

- **Authentication Errors**:
  - **Symptom**: Nodes fail to connect to Google Sheets, Gmail, or AI services.
  - **Solution**: Verify OAuth2/API credentials in n8n and ensure correct permissions.

- **Data Mismatch**:
  - **Symptom**: Empty or incorrect email content.
  - **Solution**: Check Google Sheet column names match those in expressions (e.g., `COMPANY NAME`).

- **Poor Email Quality**:
  - **Symptom**: Generated content is irrelevant or unprofessional.
  - **Solution**: Refine prompts in AI agents or adjust cleaning logic in `Code` nodes.

- **Web Search Failure**:
  - **Symptom**: `web_search` node errors out.
  - **Solution**: Ensure the search service URL is accessible or replace with a different API.

## Conclusion

Email Unlimiter is a powerful tool for automating personalized email outreach, blending data integration, AI-driven insights, and seamless email delivery. It empowers users to build meaningful connections with minimal manual effort, making it a valuable asset for business growth. By leveraging this workflow, you can focus on strategy and relationships while automation handles the details.

Experiment with customizations to tailor it to your needs, and always prioritize ethical communication practices to maximize its impact.