# 📧 Email Unlimiter(720 emails daily)

![Email Unlimiter Banner](https://github.com/PrabhanshuKamal2121/Email-unlimiter/blob/27b68ac554ca2efa4259af2e182b2fbee4df0a60/Email%20Unlimiter.png) <!-- Replace with your project banner -->

> **Email Unlimiter** is a powerful, AI-driven automation tool built with **n8n** to simplify and enhance personalized email outreach.  
> Perfect for sales, marketing, or business development, it pulls data from Google Sheets, researches companies online, crafts tailored emails with AI, and sends them via Gmail—all seamlessly in one workflow.

🔗 **[Download the Workflow JSON](https://github.com/PrabhanshuKamal2121/Email-unlimiter/blob/88a38d585af132fc1bb4b266c309c11c2fb5bf9e/Email%20Unlimiter.json)**  
🔗 **[View the Full Documentation (PDF)](https://github.com/PrabhanshuKamal2121/Email-unlimiter/blob/2048f09ee73746a7637b6285818eb4fd11a62555/Email_Unlimiter_Documentation.md)**

---

## 📋 Table of Contents

1. [🌟 Key Features](#-key-features)  
2. [⚙️ Technology Stack](#️-technology-stack)  
3. [🗂️ Workflow Breakdown](#️-workflow-breakdown)  
4. [🚀 How It Works](#-how-it-works)  
5. [🔮 Future Enhancements](#-future-enhancements)  
6. [🎯 Target Audience](#-target-audience)  
7. [📬 Get in Touch](#-get-in-touch)

---

## 🌟 Key Features

- **Dynamic Data Input**  
  - Pulls company details (name, type, address, etc.) from Google Sheets for scalable outreach.
- **Smart Web Research**  
  - Uses HTTP requests to search the web and gather insights about target companies.
- **AI-Driven Personalization**  
  - Leverages OpenAI and Ollama models to generate unique, professional email content.
- **Content Refinement**  
  - Cleans and formats AI output with custom JavaScript code for polished emails.
- **Email Automation**  
  - Sends tailored emails via Gmail with personalized greetings and subjects.
- **Batch Processing**  
  - Loops through multiple contacts efficiently with built-in batch handling.
- **Timing Control**  
  - Includes wait mechanisms to manage execution pace and avoid overloading services.

---

## ⚙️ Technology Stack

| Component          | Tools/Services                              |
|--------------------|---------------------------------------------|
| **Automation**     | n8n                                          |
| **Data Source**    | Google Sheets                                |
| **AI Processing**  | OpenAI (GPT-4.1), Ollama (Mistral, LLaMA)    |
| **Email Delivery** | Gmail                                        |
| **Web Research**   | Custom HTTP Request Tool                     |
| **Scripting**      | JavaScript (n8n Code Nodes)                  |

---

## 🗂️ Workflow Breakdown

The Email Unlimiter workflow is powered by a series of interconnected nodes in **n8n**:

- **`Manual Trigger`**  
  - Starts the workflow with a single click (`When clicking ‘Execute workflow’`).
- **`Google Sheets`**  
  - Retrieves data from a specified sheet (`Get row(s) in sheet1`).
- **`Loop Over Items`**  
  - Processes each row in batches for scalability.
- **`Web Search`**  
  - Gathers online insights about companies (`web_search`).
- **`AI Agents`**  
  - Crafts email content using multiple AI models and agents (`company_research`, `AI Agent`, etc.).
- **`Code Nodes`**  
  - Refines AI output for clarity and professionalism (`Code`, `Code1`, etc.).
- **`Gmail`**  
  - Sends the final email with dynamic content (`Send a message`).
- **`Wait`**  
  - Adds delays to ensure smooth operation (`Wait`).

---

## 🚀 How It Works

1. **Start with Data**  
   - The workflow fetches company info from Google Sheets (e.g., name, designation, company type).
2. **Research Online**  
   - A web search tool collects relevant details about each company.
3. **Generate Insights**  
   - AI models analyze the data to highlight commonalities, impressive traits, and areas of excellence.
4. **Craft the Email**  
   - AI agents create a concise, professional email body and subject, refined by code nodes.
5. **Send It Out**  
   - Gmail delivers the personalized email to the recipient, with a custom greeting.
6. **Repeat**  
   - The process iterates through all entries, notifying completion via a summary email.

---

## 🔮 Future Enhancements

- **Advanced AI Integration**  
  - Incorporate more sophisticated models for deeper personalization.
- **Multi-Channel Outreach**  
  - Add support for LinkedIn messages or other email providers.
- **Analytics Dashboard**  
  - Track email performance (e.g., opens, replies) in real-time.
- **Customizable Templates**  
  - Allow users to define email styles and tones via a UI.
- **Error Handling**  
  - Enhance retry logic and add detailed logs for troubleshooting.

---

## 🎯 Target Audience

- **Marketers**  
  - Automate outreach campaigns with minimal effort.
- **Sales Professionals**  
  - Build relationships with prospects efficiently.
- **Freelancers**  
  - Scale client acquisition with personalized emails.
- **Automation Enthusiasts**  
  - Experiment with n8n and AI-driven workflows.

---

## 📬 Get in Touch

Created by **Prabhanshu Kamal**  
📧 Email: [prabhanshukamal2121@gmail.com](mailto:prabhanshukamal2121@gmail.com)  
🌐 GitHub: [github.com/PrabhanshuKamal2121](https://github.com/PrabhanshuKamal2121)

> Have questions or ideas? Feel free to open an issue or reach out directly!
