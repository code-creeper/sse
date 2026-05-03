# SSE Learning Monorepo

This is a monorepo built for learning and experimentation purposes. The goal is to understand how Server-Sent Events (SSE) work in a real-world setup, both on the backend and frontend, without additional framework noise or business logic.

## Purpose

The main objective of this project is to explore the raw behavior of SSE:

- How events are streamed from backend to client
- How persistent connections behave in real time
- How frontend clients consume and react to SSE streams

This is intentionally kept minimal so the focus stays on the transport layer itself, not application complexity.

## Architecture

### Backend (Java)

Implements the SSE server. Responsible for emitting events and maintaining the event stream connection.

### Frontend (TanStack / React Query ecosystem)

Consumes SSE streams and updates UI in real time using TanStack utilities.

## What This Is Not

- Not a production-ready application
- Not tied to any specific business domain
- Not optimized for scale or deployment
- No authentication, persistence, or complex state management (by design)

## Goal

To build intuition around real-time communication patterns by stripping everything down to the essentials and observing how SSE behaves across backend and frontend boundaries.
