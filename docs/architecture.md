# Architecture

This document describes the public high-level architecture of SoberFlare. It is intentionally scoped to system boundaries, product layers, and trust assumptions, without exposing the core implementation details of the private codebase.

## System Overview

SoberFlare is structured around five product layers:

- `Identity`: address-based identity and local wallet ownership
- `Content`: feed, posts, comments, and content participation flows
- `Relationships`: interpretable relationship graph built from on-chain and in-app context
- `Communication`: E2EE direct chat, E2EE private groups, and regular group chat
- `Capital Flow`: assets, transfers, receive flow, and swap entry

## Client Layer

The current product experience is delivered through a React Native stack:

- `macOS desktop client`: optimized for a multi-pane workflow
- `mobile client`: built with React Native + Expo and Expo Router

The client is responsible for:

- local identity state
- wallet setup and address presence
- local signing
- rendering feed, network, contacts, chat, and profile flows
- keeping sensitive secrets on-device

## Backend Layer

The backend is a NestJS API mounted under `/api`.

At a high level, the backend handles:

- authenticated app requests
- feed and post APIs
- relationship aggregation and query APIs
- notification delivery
- wallet transfer preparation and broadcasting
- health reporting and worker coordination

The backend also runs dedicated workers for long-polling operational tasks related to encrypted chat sync and relationship refresh.

## Data Layer

SoberFlare uses a split data model:

- `PostgreSQL / Supabase`: source of truth for shared server-side state
- `SQLite`: local client-side cache
- `secure local storage`: device secrets, wallet material, and chat-related sensitive state

This split allows the product to keep shared product state on the server while preserving stronger local ownership for sensitive materials.

## Security and Trust Boundaries

SoberFlare is designed around a few important trust assumptions:

- wallet private keys remain local to the client
- transaction signing happens on-device
- chat plaintext is not treated as server-readable product data
- E2EE private-group materials remain on the local device
- for encrypted chat flows, the server only sees delivery metadata and exchange counts between addresses, not plaintext content

In practice, this means the server helps coordinate product flows, but the most sensitive user-controlled materials stay outside server custody.

## Relationship Network Layer

The relationship layer combines:

- multi-chain transfer context
- encrypted chat exchange counts between addresses
- address identity resolution across multiple wallets

The goal is not to expose raw relationship telemetry, but to turn fragmented interaction signals into a product-readable social graph.

## Content and Distribution Layer

The content layer combines:

- multi-channel feed surfaces
- post creation and interaction flows
- on-chain participation mechanisms

The public repository intentionally does not document the full internal ranking or distribution implementation. At a high level, the system is designed to explore better information distribution in the AI era and to help valuable content remain visible beyond a simple reverse-chronological feed.

## Public vs. Private Materials

This repository is the public documentation layer.

It is intended to show:

- product direction
- architecture boundaries
- release status
- public-facing materials

The full production implementation remains outside this repository during the hackathon review period.
