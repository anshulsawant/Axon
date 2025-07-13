# Axon: Your Personal Knowledge Refinery

**Axon** is a private, low-friction, state-aware environment for turning AI conversations into durable, interconnected knowledge. It is a highly opinionated, developer-first tool designed to overcome the limitations of web-based chat UIs for high-intensity research and development.

Think of it as a personal, version-controlled "thought processor" that uses Google's Gemini models as a collaborative partner.

--

## System Architecture

Here is a detailed overview of the Axon system architecture:

```mermaid
graph TD
    subgraph User's Local Machine

        %% --- The TUI Client ---
        subgraph TUI_Client [TUI Client]
            style TUI_Client fill:#e6f3ff,stroke:#333,stroke-width:2px
            Client_App[axon_client.py<br/>(Textual)]
            Client_UI(UI Rendering)
            Client_Input(Input Capture)
            Client_Dispatch(Command Dispatcher)

            Client_App --> Client_UI
            Client_App --> Client_Input
            Client_App --> Client_Dispatch
        end

        %% --- The Gemini Daemon ---
        subgraph Gemini_Daemon [Gemini Daemon]
            style Gemini_Daemon fill:#e6ffe6,stroke:#333,stroke-width:2px
            Daemon_App[geminid.py<br/>(asyncio)]
            Daemon_State(State Management<br/>Active Sessions)
            Daemon_WS(WebSocket Server)
            Daemon_API(Gemini API Gateway)
            Daemon_Logic(Knowledge Refinery Logic<br/>Tool Use Engine)
            Daemon_VC[Version Control Module<br/>(GitPython)]

            Daemon_App --> Daemon_State
            Daemon_App --> Daemon_WS
            Daemon_App --> Daemon_API
            Daemon_App --> Daemon_Logic
            Daemon_App --> Daemon_VC
        end

        %% --- Local Data Stores & File System ---
        subgraph Obsidian_Vault [Obsidian Vault (Git Repo)]
            style Obsidian_Vault fill:#fff5e6,stroke:#333,stroke-width:2px
            Vault_C[sources/C-Notes.md]
            Vault_Z[zettels/Z-Notes.md]
            Vault_A[assets/*.*]
            Vault_Git[.git/]
        end

        subgraph Local_FS [Local File System]
            style Local_FS fill:#f0f0f0,stroke:#333,stroke-width:2px
            FS_PDFs(/path/to/pdfs)
            FS_Images(/path/to/images)
        end
    
    end

    %% --- External Services ---
    subgraph External_Services
        style External_Services fill:#fde6e6,stroke:#333,stroke-width:2px
        API_Gemini[Google Gemini API]
        Repo_GitHub[GitHub Remote]
    end

    %% --- Connections ---

    %% Client <--> Daemon
    Client_Dispatch -- "WebSocket: JSON Commands & Events" --> Daemon_WS

    %% Daemon --> External Services
    Daemon_API -- "API Calls" --> API_Gemini
    Daemon_VC -- "Git Push" --> Repo_GitHub

    %% Daemon --> Local Data
    Daemon_Logic -- "File I/O" --> Obsidian_Vault
    Daemon_VC -- "Git Commands" --> Obsidian_Vault
    Daemon_Logic -- "Read" --> Local_FS
```

## The Core Problem Axon Solves

Standard AI chat interfaces are great for casual queries, but they falter during complex, long-term projects. Axon is built to solve these specific pain points:

*   **Ephemeral Conversations:** Chats are lost in a browser's history. Axon treats every conversation as a first-class, archivable asset.
*   **Context Window Limits:** Long chats degrade in performance and eventually fail. Axon has intelligent, built-in mechanisms for conversation branching and state pruning.
*   **Friction in Knowledge Capture:** Manually copying, pasting, and organizing insights is slow and disruptive. Axon automates the distillation of knowledge from raw dialogue into a structured Zettelkasten.
*   **Lack of Integration:** Web UIs are disconnected from your local development environment. Axon is designed to live on your local file system and integrate directly with your tools (Obsidian, Git).
*   **No Audit Trail:** Who knows how an idea evolved? Axon uses Git to create an atomic, auditable history of your entire knowledge base creation process.

## Key Features

*   **Terminal-First UI:** A fast, lightweight, and keyboard-driven interface built with [Textual](https://github.com/Textualize/textual) that lives where you work, eliminating browser overhead.
*   **Local-First & Private:** All your conversations, notes, and assets are stored locally in an [Obsidian](https://obsidian.md/)-compatible Markdown vault. You own your data, completely.
*   **Automated Knowledge Distillation:** Use the power of Gemini to automatically process your conversations and extract two types of knowledge:
    *   **Foundational Notes:** Key facts, decisions, and objective summaries.
    *   **Insight Notes:** Novel ideas, hypotheses, and conclusions driven by *you*.
*   **Intelligent Conversation Management:** Proactively manages context window size with powerful, low-friction branching and summarization workflows.
*   **Built-in Version Control:** Every change to your knowledge base—every new chat, every distilled insight—is automatically committed to a local Git repository. Push to a private GitHub repo for a bulletproof backup.
*   **Powerful Research Agent:** Go beyond your own thoughts. Axon is equipped with tools to perform curated literature surveys from sources like ArXiv and Google Scholar, finding impactful papers based on your personal criteria (reputable authors, conferences, citation counts).
*   **Extensible Architecture:** Built on a robust client-server model. The backend "Gemini Daemon" handles all the logic, allowing for the future development of alternative clients (e.g., a Web UI, VS Code Extension, or Emacs mode).

## The Axon Workflow

1.  **Converse:** Interact with the Gemini API through the fast TUI client.
2.  **Evolve:** When conversations grow long or change direction, use a simple command to branch the session, automatically pruning the context window.
3.  **Refine:** At any point, trigger the "Refinery" process. Axon uses Gemini to analyze the conversation and auto-generates a set of atomic, interlinked knowledge notes (Zettels) in your Obsidian vault.
4.  **Discover:** Use Axon's built-in research tools to perform literature surveys, transcribe lectures, and pull in external knowledge.
5.  **Commit:** Relax. Every step of this process is being automatically committed to your Git history, creating a perfect, recoverable record of your work.

## Getting Started

*(This section will be filled out as we complete the implementation)*

1.  **Prerequisites:** Python 3.10+, Git.
2.  **Installation:**
    ```bash
    git clone https://github.com/your-username/axon.git
    cd axon
    python -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    ```
3.  **Configuration:**
    *   Copy `.env.example` to `.env` and add your Google AI API Key.
    *   Edit `config.yaml` to point to the absolute path of your Obsidian vault. Initialize this vault as a Git repository (`git init`).
4.  **Running Axon:**
    *   In one terminal, start the daemon: `python daemon/geminid.py`
    *   In another terminal, start the client: `python client/axon_client.py`

## Project Status

**Current Phase:** Phase 1 - Core Engine (MVP) Development.

This project is currently under active development. The goal is to follow the multi-phase execution plan to build a robust and feature-complete personal knowledge engine.

--
