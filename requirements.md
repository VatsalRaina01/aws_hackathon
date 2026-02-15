# Requirements: LokSarthi (लोकसारथी)

## Overview

**LokSarthi** ("Charioteer of the People") is an AI-powered, voice-first multilingual platform that empowers India's underprivileged citizens to discover government schemes they're entitled to, file RTI applications and grievances, and gain financial literacy — all through natural conversation in their own language.

### Problem Statement

~300 million Indians are eligible for government schemes they have never applied for. RTI applications go unfiled because citizens can't draft them. Predatory lenders exploit financial illiteracy. These three problems share one root cause: **information is locked behind language, literacy, and bureaucratic barriers.** India runs 750+ Central Government schemes and thousands more at the state level, yet a tribal widow in Jharkhand who is eligible for multiple schemes often knows about none of them.

### Proposed Solution

A unified AI application that integrates three critical citizen services:
1. **Scheme Discovery Engine** — Matches citizen profiles to eligible government schemes
2. **RTI & Grievance Assistant** — Drafts formal RTI applications and complaints from plain-language input
3. **Financial Literacy Advisor** — Explains financial products, detects predatory lending, and guides basic tax/GST filing

All delivered through a **voice-first, zero-literacy-required** interface supporting 22+ Indian languages via Bhashini API, powered by AWS Bedrock (Claude 3) for intelligent conversation.

---

## User Stories

### US-1: Scheme Discovery for Eligible Citizen

**As a** rural citizen who cannot read or write,
**I want** to speak about my life situation in my own language and learn which government schemes I'm eligible for,
**So that** I can access benefits like pensions, housing, rations, and insurance that I didn't know about.

#### Acceptance Criteria
- AC-1.1: Citizen can initiate interaction via voice (web microphone, WhatsApp voice note, or phone call)
- AC-1.2: System detects spoken language automatically from 22+ Indian languages using Bhashini ASR
- AC-1.3: AI asks 5-7 simple profiling questions conversationally (age, gender, occupation, location, category, income, family status)
- AC-1.4: System matches citizen profile against 50-100+ curated government schemes and returns eligible schemes ranked by relevance
- AC-1.5: For each matched scheme, system provides: scheme name, benefit amount, required documents, how to apply, and nearest office
- AC-1.6: All responses are spoken aloud in the citizen's language via Bhashini TTS
- AC-1.7: A text summary is sent via SMS/WhatsApp for future reference

---

### US-2: RTI Application Filing

**As a** citizen who has a grievance against a government department,
**I want** to describe my problem in plain language and have the system generate a formal RTI application,
**So that** I can exercise my right to information without needing to understand legal formats.

#### Acceptance Criteria
- AC-2.1: Citizen can describe their complaint/query in their own words via voice or text
- AC-2.2: AI identifies the correct government department, scheme, and Public Information Officer (PIO) to address
- AC-2.3: System generates a properly formatted RTI application under Section 6(1) of the RTI Act, 2005
- AC-2.4: Generated RTI includes: appropriate recipient (PIO), relevant questions framed from the complaint, fee information (₹10 IPO/DD), and applicant details
- AC-2.5: System provides guidance on how to submit (online via rtionline.gov.in, or by post) and expected timelines (30 days for response)
- AC-2.6: For grievances (non-RTI), system routes to CPGRAMS or state-level grievance portals and explains the process
- AC-2.7: Citizen can ask follow-up questions about escalation (first appeal, Information Commission)

---

### US-3: Financial Literacy & Fraud Protection

**As a** low-income citizen who is considering taking a loan or has been approached by a money lender,
**I want** to understand the actual cost of borrowing and whether the terms are fair,
**So that** I can avoid debt traps and predatory lending practices.

#### Acceptance Criteria
- AC-3.1: Citizen can describe a financial product (loan, insurance, investment) in plain language
- AC-3.2: System explains the product in simple terms: "If you borrow ₹10,000 at this rate, you will pay back ₹14,200 in 12 months"
- AC-3.3: System flags predatory terms (interest > 36% APR, hidden charges, mandatory insurance bundling)
- AC-3.4: System recommends safer government alternatives (PM Mudra Yojana, PM SVANidhi, KCC loans) when applicable
- AC-3.5: System provides real-time scam alerts for common frauds (UPI OTP scams, fake loan apps, QR code fraud)
- AC-3.6: For small business owners, system guides through basic GST filing steps and Udyam registration
- AC-3.7: All financial explanations use concrete rupee amounts, not percentages or technical jargon

---

### US-4: Multilingual Voice Interaction

**As a** citizen who speaks a regional language and has limited literacy,
**I want** to interact with the entire system using only my voice in my mother tongue,
**So that** I don't need to read, write, or understand English to access government services.

#### Acceptance Criteria
- AC-4.1: System supports voice input via Bhashini ASR in Hindi, Tamil, Telugu, Bengali, Marathi, Gujarati, Kannada, Malayalam, Odia, Punjabi, Assamese, and other scheduled languages
- AC-4.2: Language is auto-detected from speech — no manual selection required
- AC-4.3: All AI responses are converted to speech via Bhashini TTS in the detected language
- AC-4.4: System uses confirmation loops: repeats key information back and asks "Is this correct?" before proceeding
- AC-4.5: No screen navigation is required — entire flow is linear and conversational
- AC-4.6: Web interface provides a large "🎤 Speak" button as the primary interaction method
- AC-4.7: System gracefully handles noisy audio, dialect variations, and code-switching (mixing languages)

---

### US-5: Citizen Profile Management

**As a** returning citizen,
**I want** the system to remember my profile from previous interactions,
**So that** I don't have to repeat my details every time I use it.

#### Acceptance Criteria
- AC-5.1: Citizen profile (age, location, occupation, category, family details) is stored securely in DynamoDB after first interaction
- AC-5.2: On return visit, system greets by name and asks "Should I use your existing profile or has anything changed?"
- AC-5.3: Citizen can update individual fields without re-entering everything
- AC-5.4: Conversation history is stored per session, allowing the citizen to reference previous scheme recommendations
- AC-5.5: All personal data is encrypted at rest and in transit
- AC-5.6: Profile data can be deleted on citizen's request ("Mera data delete karo")

---

## Functional Requirements

### FR-1: Scheme Eligibility Engine
- FR-1.1: System shall maintain a curated database of 50-100+ government schemes with structured eligibility rules (age, gender, income, occupation, category, state, BPL status)
- FR-1.2: System shall implement rule-based matching with scoring (hard filters + soft relevance ranking)
- FR-1.3: System shall use Amazon Bedrock (Claude 3) to generate natural-language explanations of matched schemes
- FR-1.4: System shall update scheme data periodically from MyScheme API (api.myscheme.gov.in) via API Setu

### FR-2: RTI/Grievance Generation Engine
- FR-2.1: System shall maintain a library of 50+ RTI templates for common issues (ration card, pension, housing, water supply, road repair)
- FR-2.2: System shall maintain a PIO (Public Information Officer) directory mapped by department and district
- FR-2.3: System shall use Bedrock to extract structured intent from citizen's plain-language complaint
- FR-2.4: System shall generate RTI applications in the legally mandated format under Section 6(1), RTI Act 2005
- FR-2.5: System shall provide tracking guidance (30-day response timeline, first appeal process, Information Commission escalation)

### FR-3: Financial Literacy Engine
- FR-3.1: System shall compute EMI, total repayment, and effective interest rate for any loan described by the citizen
- FR-3.2: System shall maintain a database of common scam patterns (UPI fraud, fake loan apps, OTP theft) with detection heuristics
- FR-3.3: System shall recommend government-backed financial alternatives when predatory products are detected
- FR-3.4: System shall explain GST filing basics and Udyam registration for micro-enterprises

### FR-4: Language Processing
- FR-4.1: System shall integrate Bhashini API for ASR (Speech-to-Text), TTS (Text-to-Speech), and NMT (Neural Machine Translation)
- FR-4.2: System shall auto-detect language from voice input without manual selection
- FR-4.3: System shall fall back to Amazon Transcribe + Amazon Polly if Bhashini API is unavailable
- FR-4.4: System shall maintain all LLM prompts with instructions to respond at a "5th-grade reading level" in the citizen's language

### FR-5: Data & Security
- FR-5.1: All citizen data shall be encrypted at rest (AES-256) and in transit (TLS 1.3)
- FR-5.2: DynamoDB shall store user sessions with TTL-based auto-expiry (30 days for anonymous, 1 year for registered)
- FR-5.3: No Aadhaar or biometric data shall be collected or stored
- FR-5.4: System shall comply with India's Digital Personal Data Protection Act, 2023

---

## Non-Functional Requirements

### NFR-1: Performance
- NFR-1.1: Voice-to-response latency shall be < 5 seconds (including ASR + LLM + TTS)
- NFR-1.2: Scheme matching shall return results within 2 seconds for profiles against 100 schemes
- NFR-1.3: System shall handle 1,000 concurrent users without degradation

### NFR-2: Accessibility
- NFR-2.1: Web interface shall be usable with zero typing — voice-only interaction
- NFR-2.2: Web interface shall use minimum 18px font, high-contrast colors, and large touch targets (48px+)
- NFR-2.3: All icons shall have accessible labels for screen readers
- NFR-2.4: System shall work on 2G/3G networks with compressed audio

### NFR-3: Availability & Scalability
- NFR-3.1: System shall achieve 99.9% uptime using serverless AWS architecture
- NFR-3.2: All Lambda functions shall auto-scale based on demand
- NFR-3.3: DynamoDB shall use on-demand capacity mode for unpredictable traffic patterns

### NFR-4: Localization
- NFR-4.1: System shall support a minimum of 10 Indian languages at launch
- NFR-4.2: All UI labels shall be translatable (i18n-ready)
- NFR-4.3: Scheme amounts shall be displayed in both numerals and words in the local language

---

## Data Sources

| Source | URL | Data Provided | Access Method |
|---|---|---|---|
| MyScheme API | api.myscheme.gov.in | 1000+ scheme eligibility & benefits | REST API via API Setu |
| Open Government Data | data.gov.in | Scheme budgets, beneficiary counts | REST API (free key) |
| RTI Online | rtionline.gov.in | RTI format, PIO directory | Manual curation |
| CPGRAMS | pgportal.gov.in | Grievance filing format | Manual curation |
| RBI Financial Education | rbi.org.in | Interest rate caps, banking rules | Curated |
| Bhashini | bhashini.gov.in | ASR, TTS, Translation (22 languages) | REST API (free key) |
| CERT-In | cert-in.org.in | Fraud/scam advisories | Curated |

---

## Target Users

| Segment | Population | Key Needs |
|---|---|---|
| Rural women & widows | ~150M | Pension, housing, ration, LPG |
| Small & marginal farmers | ~126M holdings | PM-KISAN, crop insurance, KCC loans |
| SC/ST communities | ~300M | Scholarships, Stand-Up India, tribal welfare |
| Urban informal workers | ~80M+ | e-Shram, PM-SYM pension, Ayushman Bharat |
| Senior citizens | ~140M | Old age pension, health cover, food security |
| Persons with disabilities | ~27M | Assistive devices, disability pension, UDID |

---

## Success Metrics

| Metric | Target |
|---|---|
| Schemes discovered per citizen interaction | ≥ 3 relevant schemes |
| RTI application generation accuracy | ≥ 90% correct format & routing |
| Voice recognition accuracy (Bhashini) | ≥ 85% across supported languages |
| End-to-end interaction time | < 3 minutes for scheme discovery |
| User satisfaction (post-interaction survey) | ≥ 4/5 rating |
| Estimated annual benefit unlocked per user | ₹25,000 - ₹3,00,000 |
