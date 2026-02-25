# CarbonVerified.uk | Agentic Architecture & Technical Overview

> **Note:** This repository serves as a public technical overview of the system architecture and engineering methodologies used in the development of CarbonVerified.uk. The core engine and specific compliance algorithms remain in a private repository for security and intellectual property protection.

## 🚀 The Vision
CarbonVerified.uk is an AI-native platform designed to automate Net Zero compliance for UK SMEs. By leveraging autonomous agentic workflows, the platform bridges the gap between complex climate science and user-friendly reporting.

## 🏗️ Technical Stack & "Agentic" Orchestration
The project represents a shift from traditional "wrapper" apps to a deep **Multi-Model Pipeline**:

* **Backend:** PHP 8.x & MySQL (advanced schema design for international datasets).
* **AI Models:** Orchestrated pipeline using **Claude 4.6 (Sonnet/Opus)**, **GPT-5.3-Codex**, and **Gemini 3.1 Pro**.
* **Inference Optimization:** Implemented **Speculative Decoding** using locally-hosted Qwen2.5-Instruct-0.5B (via LM Studio) as a draft model to reduce agentic latency.
* **Protocols:** **Model Context Protocol (MCP)** integration to connect AI agents directly to GitHub and MySQL for context-aware automation.

## 🧠 Core Architectural Features

### 1. Autonomous Emission Mapping
The system utilizes agents to automate data mapping for **Scope 1, 2, and 3** emissions, pulling from sources like gas, electricity, and mileage logs.

### 2. Regulatory Compliance Logic (DESNZ 2025/2026)
Built to strictly adhere to the latest UK Government/DESNZ reporting standards, ensuring all outputs are audit-ready and compliant with current SRS regulations.

### 3. Developer Velocity
The build process utilizes **Google Antigravity IDE** and **Lovable** for rapid UI generation, coupled with custom MCP tools to automate version control tasks.

### 4. Technical Architecture & Engineering Standards
For a detailed breakdown of the application's directory structure, design patterns (Repository Pattern, Singleton PDO), security standards, and AI agent interaction guidelines, please refer to the [ARCHITECTURE.md](ARCHITECTURE.md) document.

## 📊 Repository Activity
* **Project Phase:** Beta / Founding Partner Initiative.
* **Build Velocity:** 280+ commits in Feb 2026 alone, focusing on PPN 06/21 Compliance and Stripe Pro features.

---
**Architect:** Paul D Jacobs  
**Portfolio:** [pauljacobs.dev](https://pauljacobs.dev) | **LinkedIn:** [linkedin.com/in/pauldjacobs](https://www.linkedin.com/in/pauldjacobs/)