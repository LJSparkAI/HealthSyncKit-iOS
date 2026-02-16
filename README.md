# HealthSyncKit

A modular iOS framework for working with Apple HealthKit using a privacy-first, local-first design.

## Overview

`HealthSyncKit` provides a clean abstraction over HealthKit for iOS developers building health and wellness apps.  
It is designed to simplify common tasks such as permissions, data reads, normalization, and aggregation while keeping integration straightforward and reusable across projects.

This repository also includes `HealthSyncKitDemo`, a reference app that demonstrates practical integration patterns.

Core philosophy:

- Privacy-first: user health data stays on-device
- Modular design: separate concerns across adapter, domain, and aggregation layers
- Simple integration: predictable APIs for common HealthKit workflows

## Features

- HealthKit authorization and permission-handling helpers
- Reading common health metrics:
  - Steps
  - Heart rate
  - Sleep
  - Workouts
- Normalized domain models across different HealthKit sample formats
- Aggregation utilities for:
  - Daily summaries
  - Trends/time-series views
- Integration points for local storage and app-specific persistence patterns
- Included demo app for end-to-end usage examples

## Architecture

HealthSyncKit is organized in layered components to keep platform-specific code isolated and the rest of the app-facing API consistent:

1. HealthKit adapter layer  
Handles HealthKit-specific queries, type mapping, and authorization interactions.

2. Domain model layer  
Transforms raw HealthKit samples into normalized app-friendly models.

3. Aggregation layer  
Builds summaries and trend outputs from normalized records.

4. Storage hooks/integration points  
Allows callers to persist normalized or aggregated data in local storage systems.

Conceptual data flow:

```text
Apple HealthKit
    ↓
HealthSyncKit Adapter
    ↓
Normalized Domain Models
    ↓
Aggregation Utilities
    ↓
App Storage / UI
```

## Requirements

- iOS: `iOS 17+` (update as needed)
- Xcode: `Xcode 15+` (update as needed)
- Swift: `Swift 5.9+` (update as needed)

## Installation

> HealthSyncKit is not ready for package installation yet.
> The instructions below are placeholders and will be finalized when the package is published.

## Swift Package Manager (recommended)

In Xcode:

1. Open your app project.
2. Go to **File > Add Packages...**
3. Add this repository URL.
4. Select the `HealthSyncKit` product and add it to your app target.

Or in `Package.swift` (if integrating from another Swift package):

```swift
dependencies: [
    .package(url: "https://github.com/<org>/<repo>.git", from: "0.1.0")
],
targets: [
    .target(
        name: "YourApp",
        dependencies: [
            .product(name: "HealthSyncKit", package: "<repo>")
        ]
    )
]
```

## Quick Start

> The snippets below are intentionally minimal and use representative API names.  
> Adapt the exact symbols to the current `HealthSyncKit` implementation in this repository.

### 1. Import the framework

```swift
import HealthSyncKit
```

### 2. Enable HealthKit capability

For your app target:

1. Open **Signing & Capabilities**
2. Add **HealthKit**
3. Ensure required usage descriptions are present in `Info.plist` (read/write as applicable)

### 3. Request authorization

```swift
let healthSync = HealthSyncClient() // Replace with your concrete entry point

let request = HealthPermissionRequest(
    read: [.steps, .heartRate, .sleep, .workouts],
    write: [] // Add writable types only if your app needs write access
)

try await healthSync.authorize(request)
```

### 4. Query example metrics (steps + workouts)

```swift
let today = Date()
let calendar = Calendar.current
let startOfDay = calendar.startOfDay(for: today)

async let steps = healthSync.fetchSteps(from: startOfDay, to: today)
async let workouts = healthSync.fetchWorkouts(from: startOfDay, to: today)

let (stepRecords, workoutRecords) = try await (steps, workouts)
```

### 5. Fetch a daily summary

```swift
let summary = try await healthSync.fetchDailySummary(for: today)

// Example fields:
// summary.totalSteps
// summary.totalWorkoutDuration
// summary.averageHeartRate
```

## HealthSyncKitDemo

`HealthSyncKitDemo` demonstrates:

- HealthKit capability + permission flows
- Reading and displaying both:
  - Aggregated summaries
  - Raw record lists
- Data sync flow patterns from HealthKit into app state/local persistence

### Running the demo

1. Open the workspace: `sparkApps.xcworkspace`
2. Select the `HealthSyncKitDemo` scheme
3. Run on a physical iPhone (recommended for HealthKit data scenarios)
4. Grant requested Health permissions when prompted

To learn integration patterns, review the demo’s app-layer wiring around authorization, metric queries, and summary rendering.

## 🚀 Try the Demo (TestFlight)

Want to see HealthSyncKit in action?

Download **HealthSyncKitDemo** via Apple TestFlight:

👉 https://testflight.apple.com/join/AbcY73rr

HealthSyncKitDemo is a reference application that demonstrates how to:

- Read health data from Apple Health (HealthKit)
- Aggregate and structure on-device data
- Present both raw and summarized health metrics
- Build privacy-first health data workflows

All health data is processed and stored on-device.

To install:
1. Download the **TestFlight** app from the App Store.
2. Open the link above on your iPhone.
3. Join the beta and install the demo.

## Privacy & Data Handling

- HealthSyncKit is designed for on-device processing.
- No cloud sync is enabled by default.
- Health data access is controlled by user-granted HealthKit permissions.
- Apps can read and optionally write only the data types explicitly authorized by the user.

## Roadmap

- Expand metric coverage across additional HealthKit types
- Add more sample UI components in the demo app
- Improve caching and aggregation strategies
- Continue documentation and integration guide improvements
