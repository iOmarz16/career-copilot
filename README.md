# 🎓 KFUPM Career Copilot

An **agentic AI career assistant** that helps students prepare for internships and jobs.  
Give it a company and a job role — it researches the position, compares the requirements with your CV, and sends you a personalized improvement plan by email, with study sessions scheduled in Google Calendar.

## ✨ What it does
- 🔍 **Researches** the target company and job role on the web
- 📝 **Generates** an interview-prep report: company summary, key facts, interview questions, model answers, and CV tips
- 📄 **Reads your CV** and runs a **skill-gap analysis** (matched vs. missing skills)
- 🗺️ **Builds** a week-by-week learning roadmap
- 📧 **Emails** the full plan and 📅 **schedules** study sessions automatically

## 🔄 How it works
```
Student enters Company + Job Role (Gradio UI)
        ↓
Agent 1: Career Research Assistant  (CrewAI + Tavily web search)
        ↓
Agent 2: Career Coach Writer        (CrewAI)
        ↓
Career report → n8n Webhook → stored in n8n Data Table
        ↓
Student uploads CV (n8n Form)
        ↓
Extract CV text → merge with report → Claude (skill-gap analysis + learning plan)
        ↓
HTML email via Gmail  +  study sessions in Google Calendar
```

## 🧠 Architecture
### 1. Multi-agent system (CrewAI)
| Agent | Role | Output |
|---|---|---|
| **Career Research Assistant** | Searches the web (Tavily) for the company and role | Company overview, responsibilities, required skills, sources |
| **Career Coach Writer** | Turns the research into a structured prep report | Summary, key facts, interview Q&A, CV suggestions |

### 2. User interface (Gradio on Google Colab)
Simple inputs (company + job role). The generated report is shown to the user and sent to n8n via HTTP POST.

### 3. Automation (n8n)
`Form Trigger (CV upload)` → `Extract From File` → `Data Table lookup` → `Code (merge)` → `LLM Chain (Claude)` → `Code (parse JSON)` → `Code (HTML email)` → `Gmail` → `Split Out` → `Google Calendar`

## 🛠️ Tech Stack
| Layer | Tools |
|---|---|
| Agents | Python, CrewAI, Tavily |
| LLM | Claude (Anthropic) |
| UI | Gradio, Google Colab |
| Automation | n8n, Gmail, Google Calendar |

## ⚙️ Setup
1. Open the notebook in Google Colab.
2. Add your API keys (Anthropic, Tavily) using **Colab Secrets** — never hard-code them.
3. Import the n8n workflow (JSON), connect your Gmail, Google Calendar and Anthropic credentials, and set the webhook URL in the notebook.
4. Run the Gradio app, enter a company and job role, then upload your CV through the n8n form.

## 👤 Author
**Omar Alharbi** — Computer Science, KFUPM · [LinkedIn](https://www.linkedin.com/in/omar-alharbi-335737319)
