# CarbonVerified.uk | Agentic Architecture & Technical Overview

> **Note:** This repository serves as a public technical overview of the system architecture and engineering methodologies used in the development of CarbonVerified.uk. The core engine and specific compliance algorithms remain in a private repository for security and intellectual property protection.

## 🚀 The Vision
CarbonVerified.uk is an AI-native platform designed to automate Net Zero compliance for UK SMEs. [cite_start]By leveraging autonomous agentic workflows, the platform bridges the gap between complex climate science and user-friendly reporting. [cite: 7]

## 🏗️ Technical Stack & "Agentic" Orchestration
The project represents a shift from traditional "wrapper" apps to a deep **Multi-Model Pipeline**:

* [cite_start]**Backend:** PHP 8.x & MySQL (advanced schema design for international datasets). [cite: 9, 46]
* [cite_start]**AI Models:** Orchestrated pipeline using **Claude 4.6 (Sonnet/Opus)**, **GPT-5.3-Codex**, and **Gemini 3.1 Pro**. [cite: 9, 15]
* [cite_start]**Inference Optimization:** Implemented **Speculative Decoding** using locally-hosted Qwen2.5-Instruct-0.5B (via LM Studio) as a draft model to reduce agentic latency. [cite: 9, 16]
* [cite_start]**Protocols:** **Model Context Protocol (MCP)** integration to connect AI agents directly to GitHub and MySQL for context-aware automation. [cite: 9, 19]

## 🧠 Core Architectural Features

### 1. Autonomous Emission Mapping
[cite_start]The system utilizes agents to automate data mapping for **Scope 1, 2, and 3** emissions, pulling from sources like gas, electricity, and mileage logs. [cite: 14]

### 2. Regulatory Compliance Logic (DESNZ 2025/2026)
[cite_start]Built to strictly adhere to the latest UK Government/DESNZ reporting standards, ensuring all outputs are audit-ready and compliant with current SRS regulations. [cite: 21]

### 3. Developer Velocity
[cite_start]The build process utilizes **Google Antigravity IDE** and **Lovable** for rapid UI generation, coupled with custom MCP tools to automate version control tasks. [cite: 9, 18, 19]

## 📊 Repository Activity
* [cite_start]**Project Phase:** Beta / Founding Partner Initiative. [cite: 23]
* **Build Velocity:** 280+ commits in Feb 2026 alone, focusing on PPN 06/21 Compliance and Stripe Pro features.

---
**Architect:** Paul D Jacobs  
**Portfolio:** [pauljacobs.dev](https://pauljacobs.dev) | **LinkedIn:** [linkedin.com/in/pauldjacobs](https://www.linkedin.com/in/pauldjacobs/)