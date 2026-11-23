# 🎓 KFUPM Career Copilot

An **agentic AI career assistant** that helps students prepare for internships and jobs.  
A student names a target company and job role and shares their CV. Career Copilot researches the role, compares it with the CV, and emails a **personalized 15-day preparation plan**.

## ✨ What it does
- 🔍 **Researches** the target company and role on the web
- 📝 **Writes** a career report: company overview, responsibilities, required skills, hiring process, likely interview questions, and tips
- 📄 **Reads the CV** (PDF or pasted text) and identifies **8–12 skills to strengthen**
- 🗺️ **Builds** a 15-day learning plan with a concrete activity and a named resource for each day
- 📧 **Emails** the plan as a formatted HTML message
- 🗂️ **Logs** every request in Google Sheets and an n8n Data Table, and emails students when information is missing

## 🔄 Two ways in
```
A) Colab + Gradio                         B) n8n Form
   Company + Job Role + CV                   Name, Email, Company, Role, CV (PDF or text)
          ↓                                          ↓
   CrewAI: Research Assistant (Tavily)        Extract CV text from PDF
          ↓                                          ↓
   CrewAI: Career Coach Writer               (no report yet → n8n researches the
          ↓                                    role with Brave Search + Claude)
   POST report to n8n Webhook ──────────┐            │
                                        ↓            ↓
                    Validate input → Skill gap & 15-day plan (Claude)
                                        ↓
                    HTML email (Gmail) → Log (Google Sheets + Data Table)
```

## 🧠 Architecture
### 1. Multi-agent research — `HakathoanProject.ipynb`
Built with **CrewAI** and **Claude Haiku 4.5**, served through a **Gradio** app on Google Colab.

| Agent | Role |
|---|---|
| **Career Research Assistant** | Searches the web (Tavily) for current information on the company and role |
| **Career Coach Writer** | Turns the research into a clear interview-prep report |

The report, together with the company, role and CV text, is sent to the n8n webhook.

### 2. Automation — `n8n-workflow.json`
| Stage | What happens |
|---|---|
| **Collect** | Webhook (from Colab) or Form Trigger (CV upload or pasted text) |
| **Validate** | Checks company, role, CV length and email; invalid requests are logged and the student gets a correction email |
| **Research** | If no report was supplied: 3 Brave searches → Claude writes the career report |
| **Plan** | Claude + structured output parser → missing skills and a 15-day learning plan |
| **Deliver** | HTML email via Gmail, request logged to Google Sheets and an n8n Data Table, JSON response to the caller |

## 🛠️ Tech Stack
| Layer | Tools |
|---|---|
| Agents | Python, CrewAI, Tavily |
| LLM | Claude Haiku 4.5 (Anthropic) |
| UI | Gradio, Google Colab, n8n Form |
| Automation | n8n, Brave Search, Gmail, Google Sheets |

## ⚙️ Setup
**Notebook**
1. Open `HakathoanProject.ipynb` in Google Colab.
2. Add `ANTHROPIC_API_KEY` and `TAVILY_API_KEY` in **Colab Secrets** — never hard-code keys.
3. Set your n8n webhook URL, then run all cells to launch the Gradio app.

**n8n**
1. Import `n8n-workflow.json` into n8n.
2. Connect your Anthropic, Gmail, Google Sheets and Brave Search credentials.
3. Replace `YOUR_GOOGLE_SHEET_ID` and `YOUR_DATA_TABLE_ID` with your own sheet and Data Table (`career_copilot_requests`).
4. Replace `YOUR-INSTANCE` in the correction email with your n8n form URL, then activate the workflow.

## 👤 Author
**Omar Alharbi** — Computer Science, KFUPM · [LinkedIn](https://www.linkedin.com/in/omar-alharbi-335737319)
