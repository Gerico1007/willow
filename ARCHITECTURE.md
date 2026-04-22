# Willow System Architecture

**A technical overview of Jerry's distributed natural language orchestration system**

---

## System Overview

```
┌─────────────────────────────────────────────────────────┐
│                    Jerry's Devices (7 total)             │
│  (Pixel phones, iPhone, iPad, Ubuntu Studio, Computer)  │
└──────────────────────┬──────────────────────────────────┘
                       │
                ┌──────▼──────┐
                │  Tailscale  │ (secure mesh VPN)
                │   Network   │
                └──────┬──────┘
                       │
    ┌──────────────────┼──────────────────┐
    │                  │                  │
┌───▼────┐      ┌─────▼─────┐      ┌────▼────┐
│Telegram │      │ OpenClaw  │      │   SSH   │
│  Bot    │◄────►│  Agent    │◄────►│ Tunnels │
│(Input)  │      │(Execution)│      │(Fallback)│
└─────────┘      └─────┬─────┘      └─────────┘
                       │
                ┌──────▼──────┐
                │Ubuntu Studio│
                │  (Executor) │
                │  bash/tools │
                └─────────────┘
```

---

## Core Components

### 1. **OpenClaw Agent** (Ubuntu Studio)
- **Role**: Central intelligence & executor
- **Capabilities**: 
  - Natural language understanding
  - Bash command execution
  - Email sending
  - Web page opening/downloading
  - File operations
- **Architecture**: Runs on Ubuntu Studio with full bash access

### 2. **Telegram Bot**
- **Role**: Natural language interface
- **Functionality**:
  - Receives user messages (from anywhere)
  - Forwards to OpenClaw agent
  - Returns results back to user
- **Access**: Via Tailscale network

### 3. **Tailscale Mesh**
- **Role**: Secure network backbone
- **Purpose**: 
  - Connects all 7 devices securely
  - Enables remote access from Pixel phone, iPhone, iPad
  - Provides private network topology

### 4. **SSH Tunneling** (Optional)
- **Role**: Direct terminal access fallback
- **When used**: When natural language interface isn't sufficient

---

## Data Flow

```
User Message
    │
    ▼
Telegram Bot
    │
    ├─► Validate message
    ├─► Extract intent
    │
    ▼
Tailscale VPN
    │
    ▼
OpenClaw Agent (Ubuntu Studio)
    │
    ├─► Parse natural language
    ├─► Determine action
    ├─► Execute bash command
    │
    ▼
System Output
    │
    ▼
Telegram Bot
    │
    ▼
User (response)
```

---

## Network Topology

**Current Devices** (as of 2026-04-21):
- Ubuntu Studio (primary executor) — *our focus*
- Computer (Jerry's main machine)
- Pixel Phone #1, #2, #3 (Android)
- iPhone 17 Pro (iOS)
- iPad (iOS 13 or 16)

**Network Layer**: All connected via Tailscale VPN

---

## Security Model

**Authentication**:
- Telegram bot requires user token/auth
- OpenClaw agent validates all commands
- Tailscale provides network-level security

**Authorization**:
- Only trusted commands execute
- Bash access is intentional (power user setup)
- SSH as fallback with standard auth

---

## Future Extensions

- Multi-user access control
- Command history & logging
- Advanced scheduling
- Cross-device synchronization
- Mobile agents (Android/iOS specific tasks)

---

*Architecture evolves as we build. Updates documented in JOURNEY.md*