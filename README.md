## Smart India Hackathon Workshop — Project Document

## Date:17/11/2025
## Register Number:212224220032
## Name:Hariharan V
```
Problem Title: SIH 1710: Enhancing Navigation for Railway Station Facilities and Locations

Problem Description (summary)

Railway stations are busy, complex spaces where passengers — especially first-time visitors, elderly, children, and people with disabilities — struggle to find ticket counters, platforms, restrooms, food courts, gates, and exits. The project aims to build a multi-platform, real-time indoor navigation system (mobile + digital kiosks + voice) with 3D interactive maps, accessibility features, and live updates so passengers can navigate stations quickly and confidently.

Idea

Create a unified navigation platform for railway stations that combines accurate indoor maps (2D/3D), multiple indoor positioning methods, real-time facility status, and multimodal guidance:

Visual 3D/2D step-by-step routes.

Turn-by-turn voice guidance (including for visually impaired users).

Digital kiosks at decision points.

Admin tools for station staff to update layouts, facility statuses and handle incidents.

Integration with existing railway systems (arrival/departure boards, IRCTC/railway APIs) to guide passengers to the correct platform and exits.

Goal: reduce confusion, improve accessibility, decongest footpaths, and speed transfers.

Proposed Solution / Architecture Diagram
High level architecture (components)
+------------------+        +-----------------------+        +------------------+
| Mobile App (iOS/ | <----> |   Backend API Layer   | <----> | Admin Dashboard  |
| Android/ PWA)    |        |  (Node/Express / Go)  |        | (web)            |
+------------------+        +----------+------------+        +------------------+
       ^  ^                          |   ^  ^                       
       |  |                          |   |  |                       
       |  |                          |   |  |                       
+------+  +------+         +---------v---v------------+    +----------------+
| Kiosk Touchscreen | <---> | Indoor Positioning Honeypot |    | Third-party  |
| (Electron/React)  |      | (BLE Beacons / UWB / Wi-Fi)  |    | Integrations  |
+-------------------+      +---------+---+---------------+    | - IRCTC APIs  |
                                    |   |                    | - Live PIS     |
                                    |   |                    +----------------+
                             +------v---v-------+
                             |  Map & Routing   |  (Mapbox/MapLibre +
                             |  Engine          |   PostGIS / Graph DB)
                             +------+---+-------+
                                    |   |
                             +------v   v-------+
                             |  Data Layer      | (Postgres+PostGIS,
                             |  Redis cache     |  TimescaleDB for events)
                             +------+---+-------+
                                    |   |
                          +---------v   v---------+
                          | Real-time Messaging   | (Kafka / MQTT / WebSockets)
                          +-----------------------+

Component roles and tech suggestions

Mobile App (React Native / Flutter / Progressive Web App)
3D map viewer, step-by-step navigation, voice guidance, accessibility settings, offline map cache.

Kiosk UI (React + Electron/Chromium Kiosk)
Touch-optimized, large fonts, QR-code to push route to phone.

Backend API
Auth, routing API, facility metadata, event ingestion, push notifications.

Map & Routing Engine
PostGIS for geometry, Graph DB or routing engine (pgRouting or Open Source alternatives) for shortest/safest paths, Mapbox/MapLibre GL for rendering 3D tiles.

Indoor Positioning
Hybrid: BLE beacons (cost-effective), optional UWB for high accuracy at critical zones (platform entries), and Wi-Fi RTT where available. Sensor fusion with smartphone IMU for improved dead reckoning.

Real-time Messaging
MQTT or Kafka for pushing status updates (e.g., restroom closed, escalator out of order), WebSockets for live UI updates.

Admin Dashboard / CMS
Update facility locations, add temporary obstacles, publish events, schedule maintenance, upload floorplans.

TTS & Voice
Cloud TTS (or on-prem for privacy) for voice instructions; offline TTS for kiosks.

Security & Privacy
OAuth2 for user login (optional), anonymized location telemetry, GDPR/Indian privacy compliance.

Monitoring
Logs, traces (Prometheus + Grafana), and mapping quality metrics.

Use Cases (user journeys)

First-time traveler — reach platform from entry gate

User scans station QR code or opens app → chooses destination (platform/platform number) → app shows fastest walking route, time to reach, escalator/elevator options (if user chooses accessible mode) → voice + visual step-by-step guide.

Visually impaired passenger — voice-only guidance

Switch to “accessible mode” → app provides spoken turn-by-turn instructions, announces landmarks (ticket counters, platform entrances), notifies when to change levels, and indicates nearest accessible restroom.

Passenger with heavy luggage — elevator-only route

Toggle constraints → routing engine avoids stairs/escalators, prefers elevators and ramps.

Visitor looking for food or services

Search: “Food court” → list sorted by distance + opening hours + queue length (if available) → route to selected outlet.

Platform change or emergency reroute

Backend receives update (train platform revised) → system pushes new route to affected users + updates kiosks.

Kiosk-to-phone: continue navigation on mobile

Kiosk generates QR / send-to-phone link for route; user continues on mobile.

Station staff updates layout temporarily

Staff uses admin dashboard to mark an area closed → system recalculates routes and displays alerts.

Technology Stack (recommended)

Frontend (Mobile): React Native or Flutter (cross-platform), MapLibre GL / Mapbox SDK for rendering.

Frontend (Kiosk & Admin): React, Electron for kiosk packaging.

Backend: Node.js (Express) or Go (Gin); GraphQL or REST.

Database: PostgreSQL + PostGIS (spatial queries), TimescaleDB for event time-series.

Routing: pgRouting or custom Dijkstra/A* on graph built from station floorplan (Graph DB optional).

Cache / Session: Redis.

Real-time: Kafka or MQTT for backend pub-sub; WebSockets for mobile/kiosk pushes.

Indoor Positioning Hardware: BLE beacons (iBeacon / Eddystone), optional UWB anchors for high-accuracy zones.

Map Tiles: 3D tiles via Mapbox / MapTiler / custom vector tiles.

TTS / Voice: Google Cloud Text-to-Speech / Amazon Polly / on-prem engine for privacy-sensitive deployments.

CI/CD & Infra: Docker, Kubernetes (optional), GitHub Actions or GitLab CI.

Monitoring & Logging: Prometheus, Grafana, ELK stack.

Authentication: OAuth2 / JWT; integrate with railway single-sign if available (e.g., IRCTC).

Accessibility: WCAG-compliant UI, screen-reader compatibility, haptic + audio cues.

Dependencies

Station floorplans (vectorized CAD / SVG / GeoJSON) and permission from railway authorities for mapping.

Hardware: BLE beacons, optional UWB anchors, kiosks (touchscreen PCs), mounting and power.

Real-time feeds: Platform assignment APIs, arrival/departure info (PIS), station event feeds.

Network infrastructure: reliable indoor Wi-Fi or cellular coverage, or local mesh for kiosks/beacons.

Maintenance team for beacons/kiosks and for keeping layouts updated.

Legal & privacy approvals to collect/anonymize location telemetry.

Integration agreements with Indian Railways / Ministry of Railways / IRCTC for data sharing.

Features (MVP vs. Stretch)

MVP

2D station maps with searchable facility list.

Turn-by-turn routes (visual).

Kiosk with QR-to-phone transfer.

Admin dashboard for basic edits.

BLE-based positioning (room-level/zone-level accuracy).

Voice prompts for key steps (simple TTS).

Stretch / Future

3D building model with level transitions and AR guidance.

High-precision UWB positioning (sub-meter).

Real-time crowd density heatmaps and dynamic routing to avoid congestion.

Multilingual voice support (English + major Indian languages).

Integration with train schedules and ticketing (IRCTC) to time-route when boarding gates open.

Predictive routing (accounting for train dwell-time and walking speed).

Offline-first mobile operation with cached maps and routing.

Data Model (brief)

Stations: id, name, geo-boundary, floors

Levels: station_id, floor_number, floorplan_geometry

Nodes: id, type (entrance, platform, restroom...), coordinates (x,y,z), metadata

Edges: from_node_id, to_node_id, weight (distance, time), constraints (stairs/elevator)

Facilities: node_id, type, hours, status

Events: event_id, station_id, type, start_time, end_time, impact

Telemetry: anonymized device_id hash, timestamp, location_estimate

Testing & Validation

Functional tests: routing correctness, search, kiosk flows.

Localization tests: multiple languages, accessibility mode.

Indoor positioning calibration: site survey (fingerprinting for Wi-Fi/BLE) and accuracy measurement (RMSE).

Load tests: scale to expected concurrent users and kiosks.

Acceptance criteria: average routing accuracy within 5–10 meters (MVP), directions completed within acceptable time (user study), accessibility compliance with WCAG levels.

KPIs / Success Metrics

Average time saved per passenger (compared to baseline).

Reduction in missed connections attributed to wayfinding errors.

Average positioning accuracy (meters).

User satisfaction (NPS / app store rating).

Number of successful kiosk → mobile transfers.

Accessibility usage metrics (people using accessible routes).

Implementation roadmap (phased)

Discovery & data collection (2–4 weeks)

Acquire floorplans, hardware assessment, network check, stakeholder approvals.

MVP development (8–12 weeks)

Backend, PostGIS model, simple mobile app, BLE-based positioning, one kiosk prototype, admin panel.

Pilot deployment (4–8 weeks)

Deploy in one medium-sized station; on-site beacon calibration; user testing.

Iterate & scale (ongoing)

Add platforms, integrate PIS/IRCTC, multi-language TTS, crowd management features.

Full roll-out & ops

Station-by-station rollout with training for maintenance teams.

(Adjust timelines per team size & approvals.)

Risks & Mitigations

Poor indoor positioning accuracy → use sensor fusion (IMU + BLE fingerprinting) and fallback zone-based guidance.

Outdated floorplans or frequent layout changes → give station staff an easy CMS to push updates and use versioning.

Hardware maintenance overhead (beacons/kiosks) → plan maintenance contracts, remote monitoring, and battery alerts.

Privacy concerns → anonymize telemetry, provide opt-in and clear privacy policy, store minimal Personally Identifiable Information (PII).

Example Use Case Walk-through (concise)

User arrives at Station A → opens app → selects “Platform 5” → app checks train schedule (IRCTC/PIS), calculates path from Entry Gate B to Platform 5 avoiding stairs (user selected elevator-only), shows ETA 6 minutes, user taps “Start”, app announces: “Walk forward 30 metres, take the ramp on the left, then take elevator to Platform level; destination will be on the right.” If platform changes, backend pushes new route.

Next deliverables I can produce for the workshop (choose from)

Detailed API specification (OpenAPI) for backend services.

PostGIS schema + sample SQL to build routing graph.

Sample React Native proof-of-concept app (map + BLE scan stub).

Admin dashboard wireframes and kiosk UI mockups.

Deployment checklist and bill of materials for beacons/kiosks.
```
