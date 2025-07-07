# Service Routing Fix Decision

**Date**: 2025-07-07  
**Issue**: API routes not being properly exposed when only "web" service is specified in SERVICES env var

## Analysis

### Problem
- Outline documentation states "The web server hosts the Application and API" (SERVICES.md line 21-22)
- The web service implementation DOES mount API routes at `/api` (server/services/web.ts line 65)
- However, startup logic in server/index.ts (lines 241-243) treats "web" and "api" as separate services
- No actual `api.ts` service file exists - this is inconsistent

### Two Approaches Considered

#### Approach 1: Config-only Fix
- **Action**: Add "api" to SERVICES env var (e.g., `SERVICES=web,api,worker`)
- **Pros**: Quick fix, no code changes, explicit configuration
- **Cons**: Inconsistent with documentation, adds unnecessary config complexity

#### Approach 2: Code Fix (CHOSEN)
- **Action**: Modify service detection logic so "web" automatically includes API
- **Pros**: Aligns with documentation, more intuitive, reduces config complexity  
- **Cons**: Requires code change

## **Decision: Approach 2 (Code Fix)**

**Rationale**:
1. **Documentation alignment**: Makes code behavior match documented expectations
2. **Logical consistency**: Web service already mounts API routes, should be recognized as such
3. **Better UX**: Developers expect web service to include API without extra config
4. **Removes ambiguity**: Eliminates confusion between what's documented vs. implemented

## Implementation Plan

Modify `server/index.ts` lines 241-243 to treat "web" service as automatically including API functionality, or alternatively ensure the web service is recognized as sufficient for API needs.

## Team Alignment

This decision prioritizes:
- Consistency with existing documentation
- Intuitive developer experience  
- Reducing configuration complexity
- Aligning implementation with stated architecture

**Next Steps**: Implement the code change and test thoroughly before deployment.
