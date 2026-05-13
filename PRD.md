# ResumeBot AI — Product Requirements Document

> **Course:** CAP 942 — AI Application Development  
> **Document Type:** Product Requirements Document (PRD)  
> **Version:** 1.0.0  
> **Phase:** Phase 1 — Research & Planning

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Goals & Success Metrics](#3-goals--success-metrics)
4. [User Stories & Use Cases](#4-user-stories--use-cases)
5. [Functional Requirements](#5-functional-requirements)
6. [Conversation Flow & Bot Commands](#6-conversation-flow--bot-commands)
7. [Technical Architecture](#7-technical-architecture)
8. [Tools & Technology Stack](#8-tools--technology-stack)
9. [Non-Functional Requirements](#9-non-functional-requirements)
10. [Development Phases & Milestones](#10-development-phases--milestones)
11. [Risks & Mitigations](#11-risks--mitigations)
12. [Out of Scope](#12-out-of-scope)
13. [Capstone Deliverables Checklist](#13-capstone-deliverables-checklist)

---

## 1. Executive Summary

**InstaCV** is a Telegram-based AI assistant that helps job seekers optimize their resumes and craft compelling cover letters. Users interact with the bot through a familiar messaging interface, submitting their resume and a target job description as text, a file upload, or a URL. The bot analyzes the match between the two, scores it, suggests keyword improvements, rewrites the resume, generates a tailored cover letter, and delivers a professionally formatted downloadable resume in multiple design templates.

The application is built on a flexible LLM backend: a local open-source model (via Ollama) runs by default for privacy and zero cost, while optional API keys for OpenAI or Anthropic can be plugged in for higher-quality outputs. This dual-mode architecture makes the project both academically rigorous and practically deployable.

| Field | Details |
|---|---|
| **Project Name** | InstaCV |
| **Type** | Telegram Bot — AI-powered career assistant |
| **Primary LLM** | Ollama (local) — Llama 3 8B / Mistral 7B |
| **Optional LLMs** | OpenAI GPT-4o, Anthropic Claude 3.5 Sonnet |
| **Tech Stack** | Python, python-telegram-bot, LangChain, Ollama, ReportLab |
| **Target Users** | Job seekers, career changers, recent graduates |
| **Capstone Phase** | Phase 1 — Research & Planning |
| **Delivery Format** | Python application + GitHub repository |

---

## 2. Problem Statement

### 2.1 The Job Seeker's Pain Points

Modern hiring is largely automated. Most companies use Applicant Tracking Systems (ATS) that scan resumes for keyword matches before a human ever reads them. Studies show that up to **75% of resumes are rejected by ATS** before reaching a recruiter. Job seekers face three compounding problems:

- **ATS Rejection** — resumes not keyword-optimized for a specific job description are filtered out automatically, regardless of the candidate's actual qualifications.
- **Time Cost** — tailoring a resume and cover letter for every application takes 1–3 hours per job, which is unsustainable for active job seekers applying to dozens of roles.
- **Lack of Feedback** — job seekers rarely receive actionable feedback on why their application was rejected, leaving them unable to improve.

### 2.2 Why Existing Tools Fall Short

Existing solutions such as resume review websites or LinkedIn Premium features are either expensive, generic, or both. They provide one-size-fits-all advice rather than real-time, job-specific analysis. None of them operate in a conversational interface that most users already have open all day.

**InstaCV** solves this by providing instant, job-specific, AI-powered resume analysis and optimization through an interface users already know and trust: **Telegram**.

---

## 3. Goals & Success Metrics

### 3.1 Project Goals

1. Deliver a fully functional Telegram bot that runs end-to-end with no paid APIs required.
2. Implement at least one open-source LLM (Ollama) as the primary inference engine.
3. Provide clear, actionable resume analysis with a quantified match score.
4. Generate a polished, downloadable resume in at least 2 visual design templates.
5. Demonstrate prompt engineering techniques: scoring, rewriting, keyword extraction, cover letter generation.
6. Document the architecture clearly enough for a non-technical reviewer to understand the data flow.

### 3.2 Success Metrics

| Metric | Target | Measurement Method |
|---|---|---|
| End-to-end run without errors | 100% demo completion | Live demo during presentation |
| Core features implemented | 5 of 5 must-have features | Feature checklist review |
| Resume score accuracy | Logical, explainable output | Manual review of 5 test cases |
| Resume download | 2+ templates working | File received in Telegram chat |
| URL reading | Scrape & parse within 10s | Test with 3 real job posting URLs |

---

## 4. User Stories & Use Cases

### 4.1 Primary Persona

> **Alex, 28 — Software Engineer, 3 YoE**  
> Currently employed but looking for a senior role. Applies to 3–5 jobs per week. Frustrated by ATS rejections without feedback. Has one "master" resume and no time to customize it for each application.

### 4.2 User Stories

| ID | User Story | Acceptance Criteria |
|---|---|---|
| US-01 | As a job seeker, I want to send my resume and a job description to the bot so that I can see how well they match. | Bot returns a numeric score (0–100) and a summary of strengths and gaps. |
| US-02 | As a job seeker, I want to paste a URL to a job posting instead of copying the text, so that the process is faster. | Bot scrapes the URL and extracts job description text automatically. |
| US-03 | As a job seeker, I want the bot to suggest which keywords from the JD are missing from my resume. | Bot lists at least 5 missing keywords/phrases with context for where to add them. |
| US-04 | As a job seeker, I want the bot to rewrite my resume with the missing keywords incorporated. | Bot returns an improved resume text that sounds natural and human-written. |
| US-05 | As a job seeker, I want to provide info about the company so the bot can write a tailored cover letter. | Bot generates a 3-paragraph cover letter referencing company values and goals. |
| US-06 | As a job seeker, I want to download my optimized resume as a nicely formatted PDF or DOCX. | Bot sends a file in at least 2 visual design templates. |
| US-07 | As a privacy-conscious user, I want the bot to run on a local LLM so my resume data is not sent externally. | Default mode uses Ollama; external APIs are opt-in via `/settings`. |
| US-08 | As a power user, I want to switch the bot to use GPT-4o or Claude for higher-quality output. | Bot accepts API keys via `/settings` and switches LLM provider accordingly. |
| US-09 | As a job seeker, I want the bot to make my resume sound less AI-generated. | Bot applies a humanization step producing varied sentence lengths and natural transitions. |
| US-10 | As a job seeker, I want to choose between a simplified and a detailed resume version. | Bot offers `/simplify` and `/expand` commands to adjust resume complexity. |

---

## 5. Functional Requirements

### 5.1 Feature Breakdown by Priority

> **Priority legend:** 🔴 Must Have · 🟡 Should Have · ⚪ Nice to Have

| ID | Feature | Description | Priority |
|---|---|---|---|
| F-01 | Resume + JD Upload | Accept resume and job description as text, file upload (.pdf, .docx, .txt), or URL. | 🔴 Must Have |
| F-02 | Match Score Analysis | Analyze keyword overlap, skills match, experience relevance. Return score 0–100 with breakdown. | 🔴 Must Have |
| F-03 | Keyword Gap Report | Extract top keywords from JD not present in resume. Categorize by type: technical skills, soft skills, qualifications. | 🔴 Must Have |
| F-04 | Resume Rewriter | Rewrite resume incorporating missing keywords while preserving the user's authentic voice. | 🔴 Must Have |
| F-05 | Resume Downloader | Generate downloadable resume in PDF and DOCX formats with 2–3 visual design templates. | 🔴 Must Have |
| F-06 | URL Scraper | Scrape and parse job posting pages and company pages from raw URLs. | 🔴 Must Have |
| F-07 | Cover Letter Generator | Generate a tailored cover letter using resume data, JD analysis, and optional company profile. | 🟡 Should Have |
| F-08 | Company Values Analyzer | Accept company URL or text, extract mission/values/goals, map them to user profile. | 🟡 Should Have |
| F-09 | Humanization Mode | Post-process LLM output to reduce AI-detection markers: vary sentence structure, add natural transitions, first-person narrative. | 🟡 Should Have |
| F-10 | Complexity Toggle | Commands `/simplify` and `/expand` to adjust reading level and length of generated resume. | 🟡 Should Have |
| F-11 | LLM Provider Switcher | Allow users to configure OpenAI or Anthropic API keys via `/settings`, switching from local Ollama. | 🟡 Should Have |
| F-12 | Session Memory | Retain uploaded resume and JD within a single user session; allow updating specific sections. | 🟡 Should Have |
| F-13 | Multi-language Support | Detect language of resume/JD and respond in same language. | ⚪ Nice to Have |
| F-14 | Admin Dashboard | Lightweight Streamlit UI showing bot usage stats, active sessions, and LLM mode. | ⚪ Nice to Have |
| F-15 | LinkedIn Profile Import | Accept a LinkedIn profile URL and parse it as the resume source. | ⚪ Nice to Have |

---

## 6. Conversation Flow & Bot Commands

### 6.1 Core Command Reference

| Command | Description |
|---|---|
| `/start` | Welcome message and onboarding instructions. |
| `/help` | Show all available commands with short descriptions. |
| `/analyze` | Begin the resume analysis flow: bot requests resume, then job description. |
| `/score` | Re-run or display the current match score for the loaded session. |
| `/keywords` | Show the keyword gap report for the current session. |
| `/rewrite` | Rewrite the resume with missing keywords included. |
| `/simplify` | Produce a shorter, simpler version of the current resume. |
| `/expand` | Expand the resume with more detail and richer language. |
| `/humanize` | Apply humanization post-processing to the current generated text. |
| `/cover_letter` | Generate a tailored cover letter for the current JD. |
| `/company` | Submit company URL or description for values alignment analysis. |
| `/download` | Choose and download the resume in a selected design template. |
| `/settings` | Configure LLM provider (Ollama / OpenAI / Anthropic) and API keys. |
| `/reset` | Clear current session and start over. |

### 6.2 Primary Conversation Flow

The following describes the standard happy-path interaction:

```
1. User sends /start
   └── Bot responds with welcome message + quick-start guide

2. User sends /analyze
   └── Bot requests resume input (text, file, or URL)

3. User provides resume
   └── Bot confirms receipt → requests job description (text, file, or URL)

4. User provides job description
   └── Bot processes both inputs and returns:
       ├── Match score (0–100) with breakdown by category
       ├── Top 3 strengths from resume relative to JD
       ├── Top 3 gaps / missing elements
       └── Inline keyboard: [See Keywords] [Rewrite Resume] [Cover Letter] [Download]

5. User taps [See Keywords]
   └── Bot lists missing keywords by category with placement suggestions

6. User taps [Rewrite Resume]
   └── Bot generates optimized resume text
   └── Asks: "Would you like to humanize this to sound less AI-generated?"

7. User sends /company with URL (optional)
   └── Bot analyzes company values
   └── Appends alignment notes: "Your profile aligns with their mission because..."

8. User taps [Cover Letter]
   └── Bot generates cover letter tailored to JD + company values (if provided)

9. User taps [Download]
   └── Bot shows template selector: [Classic] [Modern] [Minimal]
   └── Bot sends file (PDF or DOCX) on selection
```

---

## 7. Technical Architecture

### 7.1 System Components

The system is composed of five primary layers, each with a clearly defined responsibility:

| Layer | Components & Responsibility |
|---|---|
| **Interface Layer** | `python-telegram-bot` — handles all Telegram Bot API interactions: message routing, file handling, inline keyboard callbacks, session state management. |
| **Orchestration Layer** | `LangChain` — manages prompt chains, memory (`ConversationBufferMemory`), tool routing, and sequential multi-step workflows (analyze → score → rewrite → generate). |
| **LLM Layer** | Pluggable provider abstraction. Default: **Ollama** (local, Llama 3 8B or Mistral 7B). Optional: **OpenAI API** or **Anthropic API**, selected via `/settings`. |
| **Document Processing Layer** | `BeautifulSoup + requests` for URL scraping. `PyPDF2 / python-docx` for file parsing. `Tiktoken` for token counting to stay within LLM context window limits. |
| **Output Generation Layer** | `ReportLab + WeasyPrint` for PDF rendering. `python-docx` for DOCX generation. `Jinja2` HTML templates for multiple design themes (Classic, Modern, Minimal). |

### 7.2 High-Level Data Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                          USER (Telegram)                            │
│              text / file (.pdf, .docx) / URL                        │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    INTERFACE LAYER                                  │
│                  python-telegram-bot                                │
│         Message router · File handler · Session state              │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                  DOCUMENT PROCESSING LAYER                          │
│   URL → BeautifulSoup scraper                                       │
│   PDF → PyPDF2 / pdfplumber                                         │
│   DOCX → python-docx                                                │
│   Token check → Tiktoken (truncate if needed)                       │
└──────────────────────────────┬──────────────────────────────────────┘
                               │  normalized plain text
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                  ORCHESTRATION LAYER (LangChain)                    │
│                                                                     │
│   Chain 1 — Score:        Resume + JD → JSON score report           │
│   Chain 2 — Rewrite:      Resume + keywords → optimized text        │
│   Chain 3 — Cover Letter: Resume + JD + company → cover letter      │
│                                                                     │
│   Memory: ConversationBufferMemory (per user session)               │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       LLM LAYER                                     │
│                                                                     │
│   [ Ollama — local ]  ←── default (Llama 3 8B / Mistral 7B)        │
│   [ OpenAI API ]      ←── opt-in via /settings                      │
│   [ Anthropic API ]   ←── opt-in via /settings                      │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                  OUTPUT GENERATION LAYER                            │
│   Resume text + selected template                                   │
│   → PDF  (ReportLab / WeasyPrint + Jinja2 CSS template)             │
│   → DOCX (python-docx)                                              │
└──────────────────────────────┬──────────────────────────────────────┘
                               │  file attachment
                               ▼
                        USER (Telegram)
```

### 7.3 Prompt Engineering Strategy

Three primary LangChain prompt chains are defined:

**Chain 1 — Scoring Chain**
```
System: You are a professional resume analyst. Compare the resume and job description below.
        Return ONLY valid JSON in this exact format:
        {
          "score": <int 0-100>,
          "strengths": ["...", "...", "..."],
          "gaps": ["...", "...", "..."],
          "missing_keywords": ["...", "...", "..."]
        }
        
User:   RESUME: {resume_text}
        JOB DESCRIPTION: {jd_text}
```

**Chain 2 — Rewriting Chain**
```
System: You are a professional resume writer. Rewrite the resume below to naturally
        incorporate the missing keywords. Preserve first-person voice, vary sentence
        length, avoid buzzword stacking. Sound like a human, not AI.
        
User:   ORIGINAL RESUME: {resume_text}
        KEYWORDS TO ADD: {missing_keywords}
```

**Chain 3 — Cover Letter Chain**
```
System: You are a career coach writing a personalized cover letter.
        Write 3 paragraphs: (1) hook + role interest, (2) key strengths mapped to JD,
        (3) company values alignment + closing. Tone: {tone}.
        
User:   CANDIDATE PROFILE: {resume_text}
        TARGET ROLE: {jd_text}
        COMPANY INFO: {company_info}
```

---

## 8. Tools & Technology Stack

| Category | Tool / Library | Purpose |
|---|---|---|
| Programming Language | Python 3.11+ | Primary development language |
| Telegram Integration | `python-telegram-bot` 20.x | Bot API, handlers, inline keyboards, file I/O |
| LLM Orchestration | `LangChain` | Prompt chains, memory, provider abstraction |
| Local LLM Runtime | `Ollama` | Run Llama 3 8B or Mistral 7B locally |
| Default LLM Model | Llama 3 8B / Mistral 7B | Open-source models via Ollama |
| Optional LLM (Cloud) | OpenAI GPT-4o / Anthropic Claude | Premium output quality (user-supplied key) |
| URL Scraping | `BeautifulSoup4` + `requests` | Extract text from job posting URLs |
| PDF Parsing | `PyPDF2` / `pdfplumber` | Extract text from uploaded resume PDFs |
| DOCX Parsing | `python-docx` | Extract text from uploaded resume DOCX files |
| Token Management | `Tiktoken` | Count and truncate tokens for context window |
| PDF Generation | `ReportLab` / `WeasyPrint` | Render styled PDF resumes from templates |
| DOCX Generation | `python-docx` | Generate styled DOCX resume files |
| HTML Templates | `Jinja2` | Design templates for resume rendering |
| Admin UI (Optional) | `Streamlit` | Usage dashboard and bot config panel |
| Environment Config | `python-dotenv` | Manage API keys and config securely |
| Hosting / Runtime | Local machine or Google Colab | No paid cloud required |

### 8.1 System Requirements

| Component | Minimum | Recommended |
|---|---|---|
| RAM | 16 GB | 32 GB |
| LLM Model Size | 3B parameters | 7–8B parameters |
| Python | 3.10+ | 3.11+ |
| GPU | Not required | Optional (speeds up Ollama) |
| OS | Windows / macOS / Linux | Any |

---

## 9. Non-Functional Requirements

| Category | Requirement |
|---|---|
| **Performance** | Local LLM response: under 30 seconds on a 16 GB RAM machine. Cloud LLM response: under 10 seconds. |
| **Privacy** | No user data stored persistently. Session data exists only in memory during the active conversation. No logging of resume content to disk. |
| **Portability** | Runs on Windows, macOS, and Linux without modification. No GPU required. |
| **Reliability** | Graceful error handling for all external calls (URL scraping, LLM timeout, file parse failure) with user-friendly error messages. |
| **Security** | API keys stored in `.env` file only, never logged or sent to Telegram. No hardcoded credentials in source code. `.env` in `.gitignore`. |
| **Scalability** | Designed for single-user / small-group capstone demo. One active session per user. Not intended for production-scale load. |
| **Usability** | All bot responses include next-step suggestions. No dead ends in conversation flow. All commands accessible from `/help`. |

---

## 10. Development Phases & Milestones

| Phase | Name | Deliverables | Duration |
|---|---|---|---|
| **Phase 1** | Research & Planning | This PRD document. Project proposal (1–2 pages). Tool evaluation and setup (Ollama, python-telegram-bot, LangChain). Local LLM running with test prompts. | Week 1–2 |
| **Phase 2A** | Core Bot & LLM | Working Telegram bot skeleton. Ollama integration via LangChain. Text-based resume + JD input. Score chain returning structured JSON output. | Week 3–4 |
| **Phase 2B** | File & URL Input | PDF/DOCX parsing. URL scraping with BeautifulSoup. Input router (text / file / URL). | Week 4–5 |
| **Phase 2C** | Rewrite & Generate | Resume rewriter chain. Humanization post-processor. Cover letter generator. Company values analyzer. | Week 5–6 |
| **Phase 2D** | Output & Design | PDF/DOCX template rendering (3 designs). `/download` command with template selector. `/simplify` and `/expand` commands. LLM switcher via `/settings`. | Week 6–7 |
| **Phase 3** | Deployment & Presentation | GitHub repository with README and setup instructions. 5–10 min presentation. Workflow diagram. Final written documentation. | Week 7–8 |

---

## 11. Risks & Mitigations

| Risk | Severity | Mitigation |
|---|---|---|
| Local LLM too slow on dev machine | 🟡 Medium | Test with Mistral 3B as fallback. Provide Google Colab setup instructions as alternative runtime. |
| Job posting URL scraping blocked by site | 🟡 Medium | Implement retry logic and fallback to raw text paste. Handle common anti-scraping responses gracefully. |
| LLM returns unstructured output (JSON parse failure) | 🔴 High | Use strict JSON output prompts with LangChain's `OutputParser`. Implement retry-with-correction logic. |
| PDF rendering quality inconsistent across OS | 🟢 Low | Use WeasyPrint with fixed CSS templates; test on all three platforms. Provide DOCX as reliable fallback. |
| Scope creep — too many features for the timeline | 🔴 High | Must-Have features are locked in Phase 2A. Should-Have features are implemented only after Phase 2A–2B are complete and tested. |
| API key security / accidental commit | 🟡 Medium | `.env` in `.gitignore` from day one. README includes explicit warning. No keys ever in source code. |

---

## 12. Out of Scope

The following items are explicitly **out of scope** for this capstone project:

- Training or fine-tuning any machine learning or deep learning models
- Building a persistent database for storing user resumes or history across sessions
- Real-time job search or application submission via third-party platforms
- Payment processing or subscription management
- Multi-user concurrency management or production-scale deployment (Docker, Kubernetes)
- ATS simulation — the bot provides AI analysis, not a certified ATS compatibility engine
- Mobile app or web app frontend (Telegram is the only UI)

---

## 13. Capstone Deliverables Checklist

| Deliverable | Capstone Requirement | Status |
|---|---|---|
| Project Proposal (1–2 pages) | Required — Phase 1 | 🔄 In Progress |
| This PRD Document | Required — Phase 1 | 🔄 In Progress |
| Working Python application (Telegram bot) | Required — Phase 2 | 📋 Planned |
| Runs without paid APIs (Ollama local mode) | Required — Phase 2 | 📋 Planned |
| PDF / DOCX resume download (2+ templates) | Required — Phase 2 | 📋 Planned |
| URL scraping of job descriptions | Optional Enhancement | 📋 Planned |
| Cover letter generation | Optional Enhancement | 📋 Planned |
| GitHub repository with README | Required — Phase 3 | 📋 Planned |
| Technical workflow diagram | Required — Phase 3 | 📋 Planned |
| 5–10 min presentation with live demo | Required — Phase 3 | 📋 Planned |
| Full written project documentation | Required — Phase 3 | 📋 Planned |

---

*InstaCV — PRD v1.0 · CAP 942 AI Application Development*
