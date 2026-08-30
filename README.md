<div align="center">
  <img src="images/bluelogo.png" alt="WillApp Logo" width="150"/>
  <h1>WillApp: Social Accountability Platform</h1>
  <p><strong>A gamified social productivity app that uses AI photo verification to help university students achieve their life objectives ("Wills") alongside their closest friends.</strong></p>
</div>

> **Note:** This is a showcase repository. The source code for WillApp is proprietary and closed source. This repository provides a high level overview of the architecture, technologies, and technical challenges solved during development.

## 📱 Screenshots

<div align="center">
  <img src="images/main_page.png" width="250" alt="Main Page"/>
  <img src="images/social_page.png" width="250" alt="Social Page"/>
  <img src="images/tutoria_screen.png" width="250" alt="Tutorial Screen"/>
</div>

## 💡 The WillApp Vision & Mechanics

**The Origin Story:** WillApp was conceived by a team of 5 students from UC3M (Universidad Carlos III de Madrid) who were ready to embark on their path in entrepreneurship and innovation. The idea sparked during a casual conversation in a bar, and over the course of an entire year of dedicated development, the team brought that vision to life.

**The Magic of "Wills":** At the core of the platform is the concept of a "Will" (which can be absolutely *any* goal a user wants to achieve). Whether it's passing a quantum physics exam, hitting the gym, or learning how to play basketball, WillApp provides the framework to make it happen. 

**Advanced AI Verification:** To ensure genuine accountability, WillApp utilizes a strict AI verification engine. When a user uploads a photo to verify a completed task, the AI cross references *everything* (from the specific name of the task to the granular details within the picture) ensuring that users are actually putting in the work before rewarding them.

**Gamified Progression:** As users verify their tasks, they earn points that unlock access to exclusive rewards. While the platform currently focuses on individual progression and sharing verifications with a private circle of friends, internal competitions and leaderboards are on the roadmap to take the gamification loop to the next level.

## 🛠 Tech Stack

WillApp leverages a modern, robust, and scalable technology stack spanning mobile and web ecosystems:

### Mobile Application
* **Framework:** Flutter
* **Language:** Dart
* **State Management:** Riverpod (for responsive, scalable state orchestration)
* **Local Storage:** Secure storage for caching and tokens
* **Localization:** AppLocalizations (l10n) for multilingual support (English, Spanish, French)

### Web Dashboard & Landing Page
* **Link:** [willapp.es](https://willapp.es)
* **Framework:** Next.js (React)
* **Styling:** Tailwind CSS + custom Design Tokens (AppColors, AppSpacing)
* **Animations:** Framer Motion (for premium microinteractions and layout transitions)
* **UI Components:** shadcn/ui & Radix UI

### Backend & Cloud Services
* **Database:** Firebase Data Connect (PostgreSQL backend with relational integrity)
* **Authentication:** Firebase Auth
* **App Hosting:** Firebase App Hosting for the web platform
* **Analytics & Stability:** Firebase Crashlytics & Remote Config
* **Storage:** Firebase Storage (for AI task verification photos and audio reactions)

## 📐 Architecture Overview

### High Level System Architecture
The application follows a clean, decoupled architecture separating the client side logic from the cloud infrastructure.

```mermaid
graph TD
    %% Mobile Clients
    subgraph MobileClient [Flutter Mobile App]
        UI[UI Layer / Views]
        State[State Management / Providers]
        Repos[Repositories / Models]
        
        UI <--> State
        State <--> Repos
    end

    %% Web Client
    subgraph WebClient [Next.js Dashboard]
        Pages[Web Pages]
        Components[UI Components]
        API[API Routes / Hooks]
        
        Pages <--> Components
        Components <--> API
    end

    %% Firebase Cloud Infrastructure
    subgraph FirebaseCloud [Firebase Cloud]
        Auth[Firebase Authentication]
        FDC[(Firebase Data Connect <br/> PostgreSQL)]
        Storage[Firebase Storage]
        RC[Remote Config]
    end

    %% Connections
    Repos -->|GraphQL/SDK| FDC
    Repos -->|Auth SDK| Auth
    API -->|GraphQL/SDK| FDC
    API -->|Auth SDK| Auth
    
    %% Styling
    classDef client fill:#e1f5fe,stroke:#0288d1,stroke-width:2px,color:#000
    classDef cloud fill:#fff3e0,stroke:#f57c00,stroke-width:2px,color:#000
    class MobileClient,WebClient client
    class FirebaseCloud cloud
```

### Relational Database Schema (Firebase Data Connect)
WillApp utilizes a robust relational model to handle social connections and complex user data structures seamlessly.

```mermaid
erDiagram
    USER ||--o{ POST : creates
    USER ||--o{ COMMENT : writes
    USER ||--o{ LIKE : makes
    POST ||--o{ COMMENT : contains
    POST ||--o{ LIKE : receives
    
    USER {
        uuid id PK
        string username
        string display_name
        string avatar_url
        timestamp created_at
    }
    
    POST {
        uuid id PK
        uuid author_id FK
        text content
        string media_url
        timestamp created_at
    }
    
    COMMENT {
        uuid id PK
        uuid post_id FK
        uuid user_id FK
        text content
        timestamp created_at
    }
    
    LIKE {
        uuid id PK
        uuid post_id FK
        uuid user_id FK
        timestamp created_at
    }
```

## 🧠 Challenges & Solutions

### 1. Architecting the Firebase SQL Backend (Data Connect)
**Challenge:** WillApp required complex, highly relational data queries (e.g., fetching a user's feed along with the nested comments, like counts, and author details in a single pass). Traditional NoSQL document structures in Firestore would have required excessive client side joins and high read counts, leading to performance bottlenecks and increased costs.

**Solution:** We adopted **Firebase Data Connect**, powered by PostgreSQL. By designing a strict relational schema and utilizing GraphQL for our queries, we were able to offload the heavy joining logic to the database layer. This drastically reduced network payload sizes and simplified the client side repository layer, ensuring a snappy feed experience even on slower mobile networks.

### 2. Optimizing State Management for Deep Widget Trees
**Challenge:** The mobile application features a highly interactive UI with nested components, glassmorphic overlays, and real time updates. Initially, relying on standard `setState` or simple inherited widgets caused unnecessary rebuilds of large widget trees, leading to frame drops during animations and scrolling.

**Solution:** We implemented **Riverpod** to heavily decouple the business logic from the UI. By extracting large widget trees into smaller, private `StatelessWidget` and `StatefulWidget` classes, and carefully scoping provider watchers (`ref.watch`) to only the granular widgets that needed the data, we minimized unnecessary rebuilds. This resulted in a consistent 60 FPS performance, keeping the app smooth.

### 3. Building Complex, Premium Animations on the Web
**Challenge:** The brand identity of WillApp relies on premium aesthetics, specifically "Liquid Glass" effects, ambient orbs, and seamless transitions on the web dashboard. Achieving these microinteractions without causing layout thrashing or draining the user's battery was difficult using standard CSS transitions.

**Solution:** We leveraged **Framer Motion** within the Next.js ecosystem. By using hardware accelerated properties (`transform` and `opacity`) and Framer Motion's `AnimatePresence` for route transitions, we created fluid, organic animations. We also structured the UI with a strict design system relying on Tailwind CSS tokens (`AppColors`, `AppSpacing`), ensuring that all complex components (like glassmorphism panels) remained accessible, performant, and consistent across the entire platform.

## 🌟 Open Source Contributions

As part of the WillApp development process, we extracted our highly customized, premium glassmorphism UI components into an open source Flutter package. 

You can check out our reusable **[Flutter Glass Navigation](https://github.com/brunoramirez/flutter-glass-navigation)** repository, which features:
* `GlassAppBar`: A frosted glass app bar with an expanding search body.
* `GlassBottomNavBar`: A floating, glassmorphic bottom navigation bar with fluid, hardware accelerated microinteractions.

---
*Built with passion by the WillApp team.*


