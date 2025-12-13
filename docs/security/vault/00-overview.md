# Vault Overview

## Problem
Modern platforms rely on secrets: credentials, tokens, certificates, and encryption keys.
Kubernetes Secrets provide storage but no strong authentication, authorization, rotation, or audit guarantees.

## Vault Responsibilities
Vault is the centralized authority for:
- Secret storage and lifecycle
- Authentication and identity verification
- Authorization via explicit policies
- Time-bound access (leases, TTL)
- Auditing of all access attempts

## What Vault Is Not
Vault is not:
- A configuration management system
- An application dependency
- A place for developers to manually store secrets
- A runtime requirement for application startup

## Platform Positioning
Vault is a platform security primitive.
Applications never interact with Vault directly.
The platform mediates access through Kubernetes-native mechanisms.
