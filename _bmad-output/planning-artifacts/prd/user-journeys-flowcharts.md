# Visual User Journey Flowcharts

This document provides visual state-machine representations of the textual click-map found in [`user-journeys.md`](./user-journeys.md). It is designed to visually map every constraint, validation boundary, and the exact flow from the finalized canonical workflow document.

## 1. End-to-End Integration Flow

This diagram tracks the UI interactions from the Canva Share Menu through to the final X Publish execution, explicitly mapping the Post Attributes and Settings evaluated before publishing.

```mermaid
graph TD
    classDef uiState fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef action fill:#d4e6f1,stroke:#2980b9,stroke-width:2px;
    classDef auth fill:#fcf3cf,stroke:#f1c40f,stroke-width:2px;
    classDef postAttr fill:#e8daef,stroke:#8e44ad,stroke-width:2px;
    classDef postSet fill:#d5f5e3,stroke:#27ae60,stroke-width:2px;

    %% Entry
    Start([User creates a design]) --> Share[Selects Share button on top right corner]:::action
    Share --> SelectX[Select X from the social media apps list]:::action
    
    %% Onboarding Check
    SelectX --> Q1{Is first time user?}
    Q1 -->|YES| Onboard[Show user onboarding page]:::uiState
    Onboard --> ClickOpen[User clicks on Open button]:::action
    ClickOpen --> SettingsUI[User can see design frame along with other upload settings]:::uiState
    Q1 -->|NO| SettingsUI
    
    %% Auth Check
    SettingsUI --> Q2{Is X account connected?}
    
    Q2 -->|NO| PreAuth[User can see Connect button at the bottom]:::uiState
    PreAuth --> ClickConnect[User clicks connect to trigger auth flow]:::action
    ClickConnect --> AuthFlow[Authentication flow completed]:::auth
    AuthFlow --> PostAuth[User can see selected account option & Publish button]:::uiState
    
    Q2 -->|YES| PostAuth
    
    %% Formatting & Page Selection -> Post Attributes
    SettingsUI -.-> Format[Fixed format type - Post]:::postAttr
    Format --> AddMedia[User can add image/Video to the post, Max 4 pages]:::postAttr
    
    AddMedia --> PostCaption[User writes 'post caption' for the post, 250 char limit]:::postAttr
    
    %% Media Specific Attributes
    PostCaption --> MediaCheck{Selects slot}
    MediaCheck -->|Image slot| AltText[Alt txt field gets enabled]:::postAttr
    MediaCheck -->|Video slot| VideoCap[Video caption file field gets enabled]:::postAttr
    
    AltText --> PostTagging[User can tag people at a post level, upto 10]:::postAttr
    VideoCap --> PostTagging
    
    PostTagging --> Location[User can add a location to the post]:::postAttr
    Location --> Reply[User can change reply settings - 4 options available]:::postAttr
    
    %% Post Settings
    Reply --> PostSet1[User can enable download video option]:::postSet
    PostSet1 --> PostSet2[User can set post content warning by marking specific category]:::postSet
    
    %% Publish
    PostAuth -.-> FinalCheck
    PostSet2 --> FinalCheck{Are all mandatory attributes set & publish enabled?}
    
    FinalCheck -->|YES| ClickPublish[User clicks on publish now]:::action
    ClickPublish --> Execution[Publish execution]:::auth
```
