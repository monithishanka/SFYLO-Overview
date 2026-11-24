# 🔄 Operational Workflows & System Lifecycles: SFYLO

<p align="center">
  <a href="https://sfylo.online"><img src="https://img.shields.io/badge/🌐_Live-sfylo.online-C8F31D?style=for-the-badge&labelColor=0A0A0A" alt="Live Platform"/></a>
  <img src="https://img.shields.io/badge/Workflows-6%20Core%20Pipelines-0EA5E9?style=for-the-badge" alt="Core Pipelines"/>
  <img src="https://img.shields.io/badge/Lifecycle-Event--Driven-10B981?style=for-the-badge" alt="Event Driven"/>
</p>

---

## 1. 💳 Onboarding & Lifetime License Activation Lifecycle

The payment workflow provides instant, automated account provisioning via non-custodial cryptocurrency checkout:

```mermaid
sequenceDiagram
    autonumber
    actor Parent as 👤 Parent User
    participant Web as 🌐 SFYLO Web App
    participant API as 🖥️ Core API Server
    participant Gateway as ⚡ NOWPayments Gateway
    participant DB as 💾 MongoDB Cluster

    Parent->>Web: Select "Lifetime Plan ($49)"
    Web->>API: POST /api/payment/create-invoice
    API->>Gateway: Create Invoice (BTC/ETH/USDT)
    Gateway-->>API: Returns Payment URL & Order ID
    API-->>Web: Redirects to Payment Screen
    Parent->>Gateway: Submits Cryptocurrency Transaction
    Note over Gateway: Blockchain Confirms Payment
    Gateway->>API: POST /api/payment/webhook (IPN Verified)
    API->>DB: Update User { license: { active: true, plan: 'lifetime', maxDevices: 1 } }
    API-->>Web: Socket Event 'license_activated'
    Web->>Parent: Toast: "💎 License Activated! Pair your device."
```

---

## 2. 📱 Device Provisioning & 6-Digit Handshake Pairing

Enables pairing of target hardware in under 60 seconds without sharing parent credentials on the child's device:

```mermaid
sequenceDiagram
    autonumber
    actor Parent as 👤 Parent
    participant Console as 💻 Parent Dashboard
    participant API as 🖥️ SFYLO Server
    participant Target as 📱 Target Phone (Setup Assistant)

    Parent->>Console: Click "+ Add New Device"
    Console->>API: POST /api/devices/pairing-code
    API-->>Console: Returns Code: "SF-8492" (15m TTL)
    Parent->>Target: Type "SF-8492" & Server URL
    Target->>API: POST /api/device/activate { pairingCode, deviceId, model }
    API->>API: Validate Code & Bind Device to Parent Account
    API-->>Target: 200 OK { success: true, token }
    API-->>Console: Socket Broadcast 'device_paired'
    Console->>Console: Auto-close Modal & Select Device
    Parent->>Target: Tap "Complete Setup & Hide App"
    Target->>Target: Conceal Launcher Icon & Start Telemetry Loop
```

---

## 3. 📍 Real-Time Location & Geofence Boundary Pipeline

Sub-second GPS coordinates flow from mobile hardware to the parent map canvas:

```mermaid
flowchart LR
    subgraph TargetHardware ["📱 Target Companion"]
        GPS["Fused GPS Engine"]
    end

    subgraph ServerNode ["🖥️ SFYLO Gateway"]
        Ingest["Raw WS Receiver (/ws)"]
        GeoCheck{"Distance to Safe Zone?"}
        AlertGen["Alert Event Generator"]
        DBStore[("Location History")]
    end

    subgraph ParentScreen ["💻 Parent HUD"]
        LiveMap["Leaflet OSM Canvas"]
        Breadcrumb["Animated Trail"]
        AlertToast["Geofence Alert Toast"]
    end

    GPS -->|Coordinates + Speed| Ingest
    Ingest --> DBStore
    Ingest --> GeoCheck
    GeoCheck -->|Boundary Crossed| AlertGen
    AlertGen -->|Socket: geofence_alert| AlertToast
    Ingest -->|Socket: location_update| LiveMap
    LiveMap --> Breadcrumb
```

---

## 4. ⌨️ Keystroke & Social Chat Ingestion Pipeline

Monitors inputs across chat applications and categorizes sessions for parent review:

```mermaid
flowchart TD
    subgraph MobileRuntime ["📱 Mobile Environment"]
        InputEvent["User Types Message in App\n(e.g., WhatsApp, Instagram, Chrome)"]
        Buffer["In-Memory Keystroke Aggregator\n(Debounced 2.5s)"]
    end

    subgraph CoreBackend ["🖥️ SFYLO Server"]
        WSReceiver["WebSocket Dispatcher"]
        SessionStore[("KeywordSessions Collection")]
        LiveBroadcaster["broadcastToDashboards()"]
    end

    subgraph ConsoleUI ["💻 Parent Console"]
        PillFilter["Per-App Filter Tabs\n(All | WhatsApp | Messenger | Instagram)"]
        LiveFeed["Real-Time Keystroke Feed"]
    end

    InputEvent --> Buffer
    Buffer -->|Encrypted Payload| WSReceiver
    WSReceiver --> SessionStore
    WSReceiver --> LiveBroadcaster
    LiveBroadcaster --> LiveFeed
    PillFilter -.->|Filter Selection| LiveFeed
```

---

## 5. 📳 Remote Command Dispatch & Execution Lifecycle

Executes immediate safety commands (Audible Ring Pulse, Device Screen Lock):

```mermaid
sequenceDiagram
    autonumber
    actor Parent as 👤 Parent User
    participant Dashboard as 💻 Dashboard HUD
    participant Server as 🖥️ SFYLO Core Gateway
    participant Device as 📱 Target Android Device

    Parent->>Dashboard: Clicks "Ring Device" or "Remote Lock"
    Dashboard->>Server: Socket Emit: 'device_command' { command: 'ring', duration: 10 }
    Server->>Device: Dispatches Command Packet over Persistent Socket
    Device->>Device: Executes Command (Plays High-Decibel Alert / Locks Display)
    Device-->>Server: Packet Acknowledgment { success: true, command: 'ring' }
    Server-->>Dashboard: Socket Emit: 'command_response'
    Dashboard->>Parent: Toast: "✓ Command acknowledged by device"
```

---

## 6. 🗑️ Device Deletion & Data Sanitization Pipeline

Ensures complete, parallel data destruction when unlinking hardware:

```mermaid
flowchart TD
    Start["Parent Clicks Trash Button in Left Rail"] --> Confirm{"Confirm Deletion?"}
    Confirm -->|No| Cancel["Aborted"]
    Confirm -->|Yes| API["DELETE /api/device/remove { deviceId }"]

    subgraph ParallelPurge ["Parallel Database Cleanup"]
        API --> D1["Delete Device Records"]
        API --> D2["Purge All GPS Locations"]
        API --> D3["Purge Keystroke Sessions"]
        API --> D4["Delete Stored Recordings"]
        API --> D5["Remove ID from User.linkedDevices"]
        API --> D6["Terminate Active Sockets"]
    end

    ParallelPurge --> Broadcast["Broadcast 'device_removed' to Dashboards"]
    Broadcast --> UIUpdate["Remove Card from Rail & Reset Map HUD"]
```

---

<p align="center">
  <a href="https://sfylo.online"><strong>Explore the Live Platform at sfylo.online →</strong></a>
</p>
