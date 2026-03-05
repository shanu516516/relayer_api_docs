---
title: Ephemeral (Relayer) API Reference Documentation

language_tabs:
  - shell
  # - ruby
  # - python
  - javascript
  - rust

toc_footers:
  - Twilight © 2025

includes:
  - public
  - private
  - websocket
  #- zkos
  #- sdk
  #- indexer

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Ephemeral (Twilight Relayer) API
---

# Introduction

Welcome to the Ephemeral Relayer API Documentation.

This documentation provides a reference for developers integrating with the Ephemeral relayer, which exposes the operational APIs used to interact with the Twilight Protocol's trading infrastructure. Through these endpoints, clients can submit and manage trading operations, retrieve market and pool information, query order and account state, and access aggregated analytics where users have opted in to share their activity.

All API interactions are performed through JSON-RPC requests over HTTP, while WebSocket streams are available for real-time market data such as prices and candle updates.

The sections below describe the available API endpoints, including their request parameters, response structures, and example usage.
