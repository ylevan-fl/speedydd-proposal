# SPEEDYDD - New Development Proposal

**Table of Contents**

- [Concept](#concept)
  - [Overview](#overview)
  - [Backend Architecture](#backend-architecture)
  - [Frontend Architecture](#frontend-architecture)
- [Migration](#migration)
  - [Core Principles](#core-principles)
  - [Migration Phases](#migration-phases)
    - [Phase 1: Foundation Setup](#phase-1-foundation-setup)
    - [Phase 2: Feature Migration](#phase-2-feature-migration)
    - [Phase 3: Legacy Retirement](#phase-3-legacy-retirement)
- [Migration Setup](#migration-setup)
  - [Frontend](#frontend)
    - [Monorepo Setup](#monorepo-setup)
    - [Component Library Development](#component-library-development)
    - [API and State Management](#api-and-state-management)
  - [Backend](#backend)
    - [NestJS Modular Architecture](#nestjs-modular-architecture)
    - [Development Principles](#development-principles)
    - [Pathway to Microservices](#pathway-to-microservices)
- [Migration Steps](#migration-steps)
  - [Repositories and Code Base](#repositories-and-code-base)
    - [Repository Structure](#repository-structure)
    - [Ensuring Service Continuity](#ensuring-service-continuity)
    - [Code Base](#code-base)
  - [Deployment](#deployment)
    - [Turborepo-Optimized Deployment Pipeline](#turborepo-optimized-deployment-pipeline)
    - [Frontend Deployment Strategy](#frontend-deployment-strategy)
    - [Backend Deployment Strategy](#backend-deployment-strategy)
- [Documentation](#documentation)
  - [Documentation Strategy](#documentation-strategy)
  - [Documentation Management](#documentation-management)
  - [Feature Specification Requirements](#feature-specification-requirements)
    - [Feature Specification Template](#feature-specification-template)
    - [Documentation Tools](#documentation-tools)

# Concept

## Overview

We propose a comprehensive architecture redesign for Speedydd based on modularization principles and modern architectural patterns. This proposal aims to address current limitations while providing a scalable foundation for future growth.

## Backend Architecture

**Modular NestJS Architecture with Microservices Path**

We propose transitioning from the current Express.js monolith to a modular NestJS architecture:

1. **NestJS Framework Adoption**
   - Replace Express.js with NestJS to leverage its modular architecture
   - Utilize NestJS's built-in dependency injection, modules, and services pattern
   - Benefit from TypeScript integration for enhanced type safety and developer experience

2. **Module-Based Feature Organization**
   - Organize codebase into domain-driven modules
   - Each module will encapsulate related functionality, controllers, services, and database models
   - Clear boundaries between different business domains

3. **Future-Ready Microservices**
   - Design with microservices evolution in mind
   - Initially deploy as a modular monolith
   - Enable gradual extraction of modules into independent microservices as needed
   - Use NestJS's microservices transport layer for eventual service communication

## Frontend Architecture

**Micro-Frontend Approach within Monorepo**

We propose restructuring the Next.js frontend into a more organized and scalable architecture:

1. **Monorepo Structure**
   - Implement a monorepo using Turborepo or Nx
   - Centralize package management while maintaining logical separation
   - Enable code sharing between applications and packages

2. **Micro-Frontend Organization**
   - Divide the application into feature-based micro-frontends
   - Each micro-frontend will have well-defined boundaries and responsibilities
   - Independent development and deployment capabilities for teams

3. **Component Library and Design System**
   - Create a shared UI component library following atomic design principles
   - Implement a cohesive design system with clear guidelines
   - Ensure consistent UI/UX across all features

4. **API and State Management Layer**
   - Structured API client with domain-specific services
   - Organized TanStack Query implementation with consistent patterns
   - Clear separation between UI components and data fetching logic

# Migration

## Core Principles

The migration strategy is designed around preserving business continuity while implementing architectural improvements. Our approach prioritizes:

1. **Uninterrupted Development**
   - Ensure ongoing feature development is not blocked during migration
   - Maintain delivery timelines for critical business features
   - Allow teams to continue work with minimal disruption

2. **Incremental Implementation**
   - Adopt a phased migration approach rather than a complete rewrite
   - Gradually roll out migrated features while maintaining existing functionality
   - Replace system components step-by-step to minimize risk

## Migration Phases

### Phase 1: Foundation Setup

1. **Infrastructure Preparation**
   - Set up monorepo structure for frontend
   - Establish new NestJS project structure alongside existing Express backend
   - Create shared libraries and tooling for cross-project use

2. **Design System Implementation**
   - Build core UI component library based on atomic design
   - Document component usage patterns and guidelines
   - Implement initial theme and design tokens

3. **Development Standards**
   - Define coding conventions and architectural patterns
   - Create documentation for development workflows
   - Set up linting and testing frameworks

### Phase 2: Feature Migration

1. **Feature Prioritization**
   - Identify key features for initial migration based on business priority and complexity
   - Create migration roadmap with timeline and dependencies
   - Establish metrics to measure migration progress and success

2. **Parallel Implementation**
   - Implement selected features in new architecture while maintaining existing ones
   - Create integration points between old and new systems
   - Establish feature flags for controlled rollout

3. **Incremental Replacement**
   - Gradually replace existing components with new implementations
   - Implement A/B testing for critical features to validate new implementations
   - Roll back capability for any issues encountered

### Phase 3: Legacy Retirement

1. **Complete Transition**
   - Finalize migration of all critical features
   - Validate system performance and functionality
   - Switch all traffic to new implementation

2. **Legacy Code Removal**
   - Safely remove deprecated code paths
   - Archive legacy repositories for reference
   - Document knowledge transfer from old to new systems

3. **Optimization**
   - Performance tuning of new architecture
   - Refinement of development workflows
   - Planning for future enhancements

# Migration Setup

## Frontend

### Monorepo Setup

We will create a new repository with a monorepo structure to support our micro-frontend architecture:

1. **Repository Structure**
   - Create a new Git repository dedicated to frontend code
   - Implement Turborepo as our monorepo management tool
   - Configure workspaces for applications and packages

2. **Core Packages Organization**
   ```
   speedydd-frontend-v2/
   ├── apps/
   │   ├── web/               # Current frontend
   │   └── feature-modules/   # Feature-specific micro-frontends
   ├── packages/
   │   ├── ui/                # Shared UI component library
   │   ├── config/            # Shared configuration
   │   ├── hooks/             # Shared React hooks
   │   └── utils/             # Shared utilities
   ├── tools/                 # Build and development tools
   └── package.json           # Root package configuration
   ```

3. **Dependency Management**
   - Centralized dependency management at the root level
   - Consistent versioning across all packages and applications
   - Shared configuration for TypeScript, ESLint, and other tools

### Component Library Development

1. **Atomic Design Implementation**
   - Structure the UI library following atomic design principles:
     - Atoms: Basic UI elements (buttons, inputs, icons)
     - Molecules: Simple component combinations
     - Organisms: Complex UI components
     - Templates: Page layout structures

2. **Storybook Integration**
   - Implement Storybook for component development and documentation
   - Create interactive component examples and usage guidelines
   - Implement visual regression testing

### API and State Management

1. **Domain-Driven API Layer**
   - Restructure API services by domain/feature
   - Create typed API client with consistent error handling
   - Implement request/response interceptors

2. **Organized Query Management**
   - Create structured TanStack Query implementation
   - Implement query key factories for consistent cache management
   - Develop standardized patterns for mutations and query invalidation

> [!IMPORTANT]  
> **Preserving Development Continuity**
> 
> We will use Git subtree to clone the existing `speedydd-frontend` project into `apps/web/` within the new monorepo.
> 
> This approach allows us to maintain ongoing development in speedydd-frontend (including bug fixes and new features) while we build new apps (e.g., auth) using the new development architecture.
> 
> Once the migration is complete, we'll integrate the new apps with the existing web app using Next.js multi-zone architecture. This ensures a smooth transition without blocking or disrupting current work.
>
> **Multi-Zone Integration Benefits:**
> - Allows deploying and developing multiple Next.js applications as a single app
> - Enables independent development and deployment cycles for different zones
> - Provides seamless user experience across different applications
> - Supports gradual migration without full codebase rewrites
>
> **Reference:** [Next.js Multi-Zones Documentation](https://nextjs.org/docs/pages/guides/multi-zones)

## Backend

### NestJS Modular Architecture

We will set up a NestJS workspace to support a modular backend architecture with the flexibility to evolve into microservices:

1. **Repository Structure**
   - Create a new repository dedicated to the backend migration
   - Initialize a NestJS workspace with clear separation of concerns
   - Establish standardized project structure for consistency
   - **Implement monorepo structure using Turborepo** for efficient workspace management

2. **Monorepo Structure Organization**
   ```
   speedydd-backend-v2/
   ├── apps/
   │   ├── api/                      # Main API application
   │   │   ├── src/
   │   |   |   ├── server/           # Current Api service
   │   │   │   ├── app.module.ts     # Root application module
   │   │   │   └── main.ts           # Application entry point
   │   ├── admin-api/                # Admin API (future microservice)
   │   └── notification-service/     # Notification service (future microservice)
   ├── libs/
   │   ├── common/                   # Shared utilities, guards, filters
   │   │   ├── decorators/           # Custom decorators
   │   │   ├── filters/              # Exception filters
   │   │   ├── guards/               # Authentication guards
   │   │   ├── interceptors/         # Request/response interceptors
   │   │   └── utils/                # Shared utility functions
   │   ├── config/                   # Configuration modules
   │   └── modules/                  # Feature modules as libraries
   │       ├── auth/                 # Authentication module
   │       │   ├── controllers/      # Route controllers
   │       │   ├── dto/              # Data transfer objects
   │       │   ├── entities/         # Database entities
   │       │   ├── services/         # Business logic
   │       │   └── auth.module.ts    # Module definition
   │       ├── users/                # Users module
   │       ├── products/             # Products module
   │       └── [other-domains]/      # Additional domain modules
   ├── tools/                        # Build and development tools
   ├── turbo.json                    # Turborepo configuration
   └── package.json                  # Root package configuration
   ```

3. **Monorepo Benefits**
   - Shared code between applications through libraries
   - Consistent dependency management across all modules
   - Optimized build pipelines with caching through Turborepo
   - Easier coordination of cross-cutting changes
   - Simplified CI/CD integration

4. **Module Organization**
   - Each domain feature will be encapsulated in its own module
   - Modules contain controllers, services, entities, and DTOs
   - Clear boundaries between different business domains
   - Independent testing and deployment capabilities

### Development Principles

1. **Domain-Driven Design**
   - Organize code around business domains rather than technical concerns
   - Encapsulate domain logic within bounded contexts
   - Create clear interfaces between different domains
   - Enable domain experts to understand and contribute to their areas

2. **Clean Architecture**
   - Implement layers with clear responsibilities
   - Ensure dependency flows from outer layers (controllers) to inner layers (services, repositories)
   - Use interfaces to abstract implementation details
   - Facilitate testing through dependency injection

3. **Gradual Migration Approach**
   - Develop new services in NestJS while maintaining existing Express services
   - Refactor existing services into isolated, maintainable NestJS modules
   - Create adapter layers for interoperability during transition
   - Prioritize migration based on business value and complexity

### Pathway to Microservices

1. **Initial Monolithic Structure**
   - Begin with a modular monolith for simplicity
   - Establish clear module boundaries as if they were separate services
   - Implement proper dependency injection and avoid tight coupling

2. **Service Extraction Preparation**
   - Design modules with future service extraction in mind
   - Implement message-based communication patterns between modules
   - Use NestJS microservices transport layer for internal communication

3. **Service-Level Ownership**
   - Enable team ownership of specific domains/services
   - Establish clear responsibilities and accountability
   - Support independent development cycles for different modules

> [!NOTE]
> This architecture will allow us to scale backend development efficiently and provide the flexibility to adopt microservices incrementally as our needs evolve, without committing to the operational complexity of microservices from the start.

> [!WARNING]  
> **Conceptual Architecture - Not Yet Production-Ready**
> 
> The structure and plans outlined above represent the initial concept for the new architecture. They are not yet production-ready.
> 
> We will need to deep dive into the structure, evaluate trade-offs, and refine the implementation details before moving forward.
> 
> Key considerations will include:
> - Performance implications of the proposed architecture
> - Team readiness and training requirements
> - Integration complexities with existing systems
> - Potential impacts on development velocity during transition

> [!IMPORTANT]
> **Preserving Backend Development Continuity**
>
> To preserve development continuity, we will use Git subtree to clone the existing `speedydd-apiservice` into `apps/api/src/server`.
>
> Since NestJS is built on top of Express.js, we can continue serving the existing Express.js code within the NestJS application. This allows us to gradually migrate to the NestJS architecture without affecting current services or blocking ongoing development.
>
> **Integration Benefits:**
> - Existing Express.js routes continue to function within NestJS
> - Gradual migration of routes to NestJS controllers
> - Shared middleware and utilities between old and new code
> - No disruption to existing services during migration 

# Migration Steps

## Repositories and Code Base

### Repository Structure

We will create two new repositories for the new architecture:

1. **Frontend Repository**
   - Name: `speedydd-frontend-v2`
   - Purpose: House the new Next.js monorepo structure with micro-frontends
   - Technology: Next.js within Turborepo monorepo

2. **Backend Repository**
   - Name: `speedydd-backend-v2`
   - Purpose: House the new NestJS modular architecture
   - Technology: NestJS within Turborepo monorepo

### Ensuring Service Continuity

A key priority during migration is maintaining stability of existing services:

1. **Behavioral Consistency**
   - All existing functionality must maintain consistent behavior in the new architecture
   - API contracts must remain unchanged until explicit versioning changes
   - Thorough testing to verify equivalent functionality before replacement

2. **Integration Strategy**
   - Use Git subtree to import existing codebases into the new repositories
   - Set up comprehensive test suites to verify functionality before and after migration
   - Implement feature flags to control the transition between old and new implementations

3. **Backward Compatibility**
   - Maintain compatibility with existing clients and integrations
   - Avoid breaking changes to public APIs and interfaces
   - Create adapter layers where necessary to translate between old and new systems

4. **Monitoring and Validation**
   - Implement robust monitoring to detect any behavioral differences
   - Create comprehensive logging of migration-related activities
   - Establish clear rollback procedures in case of unexpected issues

### Code Base

The codebase needs to be prepared according to our development requirements, including several key aspects:

1. **Standardized Development Practices**
   - Logging and monitoring infrastructure
   - Authentication and authorization flows
   - API endpoint structure and documentation
   - Error handling and reporting mechanisms
   - Testing strategies (unit, integration, e2e)

2. **Shared Development Tools**
   - Code formatting and linting rules
   - Pre-commit and CI/CD hooks
   - Documentation generation
   - Development environment setup

3. **Cross-Cutting Concerns**
   - Security implementations
   - Performance optimization techniques
   - Accessibility standards
   - Internationalization and localization support

> [!IMPORTANT]
> The detailed codebase proposal with specific implementation recommendations will be introduced at a later stage. This section serves as a placeholder for future elaboration on the technical specifications and best practices.

## Deployment

### Turborepo-Optimized Deployment Pipeline

To maximize the benefits of our monorepo architecture, we will implement deployment processes that follow Turborepo's best practices:

1. **Optimized Build Process**
   - Leverage Turborepo's incremental builds to only rebuild affected packages
   - Utilize remote caching to share build artifacts across environments and CI/CD pipelines
   - Implement parallel task execution for faster build times

2. **Deployment Configuration**
   - Configure deployment pipelines with `turbo.json` to define task dependencies and outputs
   - Set up workspace-specific deployment scripts in each package's configuration
   - Standardize environment variable management across workspaces

3. **CI/CD Integration**
   - Implement pruned builds to only include necessary dependencies for each deployment
   - Configure monorepo-aware CI/CD pipelines that understand workspace relationships
   - Set up scoped testing and deployments based on affected workspaces

### Frontend Deployment Strategy

1. **Application-Specific Deployments**
   - Configure independent deployment processes for each Next.js application
   - Implement staged deployments with preview environments
   - Utilize Next.js optimizations like static generation where appropriate

2. **Shared Package Management**
   - Version and publish internal packages to a private registry
   - Configure dependency management to ensure consistent versions across applications
   - Set up automated dependency updates and security scanning

### Backend Deployment Strategy

1. **Service Deployment**
   - Deploy NestJS applications as containerized services
   - Implement infrastructure-as-code for consistent environment configuration
   - Configure service discovery and API gateway integration

2. **Database Migration Management**
   - Synchronize database schema changes with application deployments
   - Implement zero-downtime migration strategies
   - Set up database version control and rollback capabilities

> [!NOTE]
> By following Turborepo's best practices, we can achieve more efficient and scalable deployments across multiple apps and packages within our monorepo structure. This approach will reduce build times, improve deployment reliability, and enable more flexible release strategies.
>
> **Reference:** [Turborepo Documentation - Developing Applications](https://turborepo.org/docs/crafting-your-repository/developing-applications)

# Documentation

## Documentation Strategy

Comprehensive documentation is essential for the success of our new architecture and migration. Our documentation strategy will focus on several key areas:

1. **Architecture Documentation**
   - System architecture overviews and diagrams
   - Design decisions and rationales
   - Technical standards and conventions
   - Integration patterns between components

2. **Code Documentation**
   - Inline code documentation standards
   - API documentation generation
   - Component and module documentation
   - Repository README and contribution guidelines

### Documentation Management

All specifications and technical documentation will be maintained in a dedicated repository:

1. **Specifications Repository**
   - Name: `speedydd-spec`
   - Purpose: Centralized location for all feature specifications
   - Organization: Structured by domain/feature area
   - Access: Available to all development teams

2. **Documentation Workflow**
   - New features begin with a specification in the `speedydd-spec` repository
   - Specifications undergo review before implementation begins
   - Specifications are updated as features evolve
   - Documentation review is part of the feature completion criteria

## Feature Specification Requirements

To ensure clarity, consistency, and quality in our development process, each new or migrated feature will require a detailed specification document that includes:

### Feature Specification Template

1. **Overview**
   - Feature name and ID
   - Clear description of the feature and its purpose
   - Business context and requirements
   - Success criteria and metrics

2. **Technical Design**
   - Database schema changes (if any)
   - Data models and relationships
   - Sequence diagrams showing interaction flows
   - Component architecture within the feature

3. **API Documentation**
   - Request/response flow diagrams
   - Comprehensive list of API endpoints
   - Request parameters and validation rules
   - Response formats and status codes
   - Authentication and authorization requirements

4. **Implementation Guidelines**
   - Coding standards specific to the feature
   - Performance considerations
   - Security requirements
   - Testing requirements (unit, integration, e2e)

5. **Special Considerations**
   - Important notes for developers
   - Known limitations or constraints
   - Potential future extensions
   - Compatibility considerations with existing systems

### Documentation Tools

We will use the following tools to maintain our documentation:

1. **Technical Documentation**
   - Markdown files in the repository for developer-focused documentation
   - Automated API documentation generation with OpenAPI/Swagger

2. **Knowledge Management**
   - Centralized documentation portal for all project documentation
   - Searchable knowledge base for architectural decisions and patterns
   - Regular documentation reviews and updates

> [!TIP]
> The Specification document will also include how the feature should be developed, so we no longer need to write separate developer notes.
>
> It will serve as a single source of truth for both implementation and collaboration, ensuring that all team members understand the development approach, logic, and technical details upfront.
> 

> [!IMPORTANT]
> Complete and up-to-date documentation for each feature is mandatory, not optional. Feature implementation will not be considered complete until its documentation meets the requirements outlined above.
> 
> This documentation-first approach will help maintain consistency, reduce misunderstandings, and make onboarding and handoffs easier for all team members.

> [!IMPORTANT]
> **Specification Review and Approval Process**
>
> Writing a development specification may slightly delay the start of feature implementation, but it ensures that our development process is heading in the right direction and helps reduce mistakes during development.
>
> Any newly proposed or updated feature must be:
> - Discussed, reviewed, and gather feedback from the development team
> - Approved by the Tech Lead before implementation begins
>
> While this adds a few more steps to the process, it helps us maintain a clear, consistent, and maintainable codebase. It also ensures that developers who haven't worked on a particular feature — including new team members — can easily understand the feature and source code. 