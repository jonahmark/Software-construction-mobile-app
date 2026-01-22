# Software Construction: Behind the App – WhatsApp

## Understanding the App

### 1. App Overview
- **Problem Solved:** WhatsApp enables instant global communication through messaging, voice/video calls, and media sharing. It replaces SMS and reduces international call costs while ensuring secure communication.  
- **Primary Users:** Individuals, groups, and businesses worldwide; from casual users to professional teams requiring fast and reliable messaging.

### 2. Core Features
1. Text Messaging  
2. Voice & Video Calls  
3. Media Sharing (photos, videos, documents, voice notes)  
4. Group Chats  
5. Status Updates  
6. End-to-End Encryption  
7. Notifications  


## Thinking Behind the Scenes

| Feature | Software Components | Likely Technologies / Implementation | Internet Required? | Behavior if Network is Slow/Unavailable |
|---------|-------------------|------------------------------------|-----------------|----------------------------------------|
| **Text Messaging** | UI: chat screen, input field; Business Logic: message routing, delivery status; Network/API: server routing; Data Storage: local SQLite/Realm DB | React Native / Android XML, WebSocket API, SQLite / Room DB | Yes | Messages may queue locally; delivery status shows “pending” |
| **Voice & Video Calls** | UI: call screen; Business Logic: call management, codec selection; Network/API: WebRTC or SIP servers; Data Storage: call logs | WebRTC, Opus / VP8 codecs, Firebase Cloud Messaging | Yes | Calls may lag, drop, or fail; poor audio/video quality |
| **Media Sharing** | UI: file picker, preview; Business Logic: compression/encoding; Network/API: multipart upload/download, REST API; Data Storage: local cache | LZ4 or JPEG / MP4 compression, AWS S3 or Google Cloud Storage | Yes | Upload/download may fail; partial media delivery |
| **Group Chats** | UI: group interface; Business Logic: member management, message routing; Network/API: server fan-out; Data Storage: local DB sync | PostgreSQL / NoSQL backend (Cassandra), WebSocket / WebPush | Yes | Messages delayed or unsynced; inconsistencies may occur |
| **Status Updates** | UI: editor, viewer; Business Logic: lifecycle (24h expiry), server sync; Network/API: REST API; Data Storage: cache | AWS S3 / CDN, local caching | Yes | Status may not upload; viewers may see incomplete updates |
| **End-to-End Encryption** | Business Logic: encrypt/decrypt messages; Network/API: secure transport; Data Storage: encrypted storage | Signal Protocol (AES-256), TLS / HTTPS | Yes | Messages may fail to decrypt if keys not synced |
| **Notifications** | UI: banners, badges; Business Logic: triggers; Network/API: Firebase Cloud Messaging / APNs; Data Storage: minimal | FCM (Android), APNs (iOS) | Partially | Notifications may be delayed; offline notifications queued |



## Change and Maintainability

**Chosen Change Scenario:** Add offline support  

- **Parts that would need changes:**  
  - Local database (SQLite / Realm) to store messages and media offline  
  - Message queue system for pending messages  
  - UI to indicate offline status and pending messages  
  - Synchronization logic to merge offline changes with server  

- **Existing features that could break:**  
  - Real-time message delivery may show inaccurate status  
  - Group chat synchronization may have conflicts  
  - Media uploads may duplicate or fail  
  - Notifications may not update correctly  

- **Why this is difficult:**  
  - Requires robust **state management** and **conflict resolution**  
  - Needs **queueing and retry mechanisms** for offline messages  
  - Complex **testing across devices and OS versions**  
  - High risk of **data consistency bugs** across multiple devices  


## Software Construction Challenges

1. **Performance and Scalability:** Handling billions of messages daily with low latency; may require distributed servers, caching (Redis / Memcached), and load balancing.  
2. **Security and Data Privacy:** End-to-end encryption with Signal Protocol; protecting user data against attacks; secure key management.  
3. **Testing Across Devices and OS Versions:** Hundreds of Android / iOS versions; multiple screen sizes; testing offline/online modes.  
4. **Tight Coupling Between Features:** Messaging, calls, media, and notifications are interconnected; changing one may affect others.  
5. **Reliability Under Poor Network Conditions:** Must handle low bandwidth, intermittent connectivity, and network interruptions gracefully; requires offline queues and retry logic.  


## Personal Reflection

1. **What surprised me most about the complexity:**  
   - Even a simple “send message” involves multiple components: UI, local storage, network, encryption, server sync, and notification delivery. Coordination between frontend and backend is critical.  

2. **Why “working code” isn’t enough:**  
   - Large-scale apps require **maintainability, scalability, reliability, and security**. Code that “works” in one scenario may fail for millions of users across varied devices, OS versions, and network conditions.  

3. **What I learned about software construction:**  
   - Thinking like a software engineer involves considering **architecture, network, storage, encryption, user experience, and edge cases** together.  
   - Proper documentation and reasoning are as important as writing code.  


## Contribution Declaration

- **Student Name:** Mutebi Jonah Mark 
- **Contribution:** Entire assignment completed independently.