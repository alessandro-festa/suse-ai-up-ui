# SUSE AI Rancher Extension

A Rancher UI Extension for managing SUSE AI applications across Kubernetes clusters. This extension provides a unified interface for installing, managing, and monitoring AI workloads in Rancher-managed clusters.

## Architecture Overview

This extension follows established domain-model-driven architecture patterns that provide:

- **Domain Models**: Rich resource models with computed properties and actions
- **Centralized Store Management**: Vuex-style state management following standard patterns
- **Standard UI Components**: Consistent integration with Rancher's design system
- **Feature Flag System**: Version-aware feature management
- **Utility-First Architecture**: Reusable utility modules and services

### Project Structure

```
pkg/suseai-rancher-ext/
├── assets/                   # Static assets (icons, images)
├── components/               # Reusable Vue components
│   ├── app/                 # App-specific components
│   │   ├── AppStatusBadge.vue
│   │   ├── AppResourceTable.vue
│   │   ├── ClusterChips.vue
│   │   └── wizard/          # Installation/management wizards
│   ├── forms/               # Form components
│   │   ├── ValuesEditor.vue
│   │   └── ClusterSelector.vue
│   └── shared/              # Shared utility components
│       ├── LoadingButton.vue
│       ├── ErrorBoundary.vue
│       └── FeatureFlag.vue
├── config/                  # Configuration files
│   ├── suseai.ts           # Main product configuration
│   ├── feature-flags.ts    # Feature definitions
│   ├── settings.ts         # Application settings
│   ├── table-headers.ts    # ResourceTable configurations
│   ├── labels-annotations.ts # K8s metadata helpers
│   └── doc-links.ts        # Documentation links
├── models/                  # Domain models
│   ├── base/               # Base classes and mixins
│   │   ├── suseai-resource.ts
│   │   └── resource-mixin.ts
│   ├── app/                # App domain models
│   │   ├── app-resource.ts
│   │   ├── app-collection.ts
│   │   └── app-installation.ts
│   ├── cluster/            # Cluster management models
│   │   ├── cluster-resource.ts
│   │   └── repository-resource.ts
│   └── chart/              # Chart handling models
│       ├── chart-resource.ts
│       └── chart-values.ts
├── pages/                   # Vue page components
│   ├── Apps.vue            # Main apps listing (ResourceTable)
│   ├── Install.vue         # App installation page
│   ├── Manage.vue          # App management page
│   └── components/         # Page-specific components
├── services/                # Business logic services
│   ├── app-lifecycle-service.ts    # App install/upgrade/delete
│   ├── chart-service.ts            # Chart metadata and values
│   ├── chart-values.ts             # Chart values processing
│   ├── cluster-resources.ts        # Cluster resource operations
│   ├── cluster-service.ts          # Cluster management
│   ├── app-collection.ts           # App discovery and collection
│   ├── rancher-apps.ts             # Core Rancher API integration
│   ├── repo-auth.ts                # Repository authentication
│   └── ui-persist.ts               # UI state persistence
├── store/                   # Vuex store modules
│   ├── suseai-common.ts    # Main store factory
│   ├── modules/            # Store modules
│   │   ├── apps.ts
│   │   ├── clusters.ts
│   │   ├── installations.ts
│   │   └── repositories.ts
│   └── types/              # Store type definitions
│       ├── store-types.ts
│       └── state-types.ts
├── types/                   # TypeScript type definitions
│   ├── app-types.ts
│   ├── cluster-types.ts
│   ├── installation-types.ts
│   ├── chart-types.ts
│   └── action-types.ts
├── utils/                   # Utility functions
│   ├── feature-flags.ts    # Feature flag management
│   ├── app-status.ts       # Status calculation utilities
│   ├── cluster-operations.ts # Cluster utility functions
│   ├── chart-resolver.ts   # Chart resolution logic
│   ├── validation.ts       # Form validation utilities
│   ├── promise.ts          # Promise utilities
│   └── constants.ts        # Shared constants
├── index.ts                # Extension entry point
├── product.ts              # Product registration
└── routing.ts              # Route definitions
```

## Key Architectural Patterns

### Domain Models (Resource-Centric Architecture)

The extension uses rich domain models that encapsulate both data and behavior:

```typescript
// Example: AppResource with computed properties and actions
const app = new AppResource(data);
console.log(app.stateDisplay);     // "Installed", "Available", etc.
console.log(app.availableActions); // [install, upgrade, uninstall]
await app.install({ clusterId: 'c-m-xyz', values: {...} });
```

### Store Management

Centralized state management following standard patterns:

```typescript
// Installation discovery with progress tracking
await this.$store.dispatch('suseai/discoverInstallations');
const apps = this.$store.getters['suseai/installedApps'];
const clusterApps = this.$store.getters['suseai/appsForCluster']('c-m-xyz');
```

### Feature Flags

Version-aware feature management:

```typescript
if (featureEnabled('multiClusterInstall', clusterVersion)) {
  // Show multi-cluster installation options
}
```

### Standard UI Components

All components use Rancher's standard UI patterns as much as possible:

- `ResourceTable` for data display with built-in filtering and actions
- `Header` component for consistent page headers
- `LoadingButton` for actions with loading states
- Standard form components for consistent user experience

## Development

### Prerequisites

- Node.js 16+ and Yarn
- Access to a Rancher cluster
- Extension developer features enabled in Rancher

### Setup

1. **Clone and install dependencies:**
   ```bash
   git clone <repository-url>
   cd suseai-rancher-ext
   yarn install
   ```

2. **Build the extension:**
   ```bash
   yarn build-pkg suseai-rancher-ext
   ```

3. **Serve during development:**
   ```bash
   yarn serve-pkgs
   ```
   Copy the URL shown in the terminal.

4. **Load in Rancher:**
   - In Rancher, go to your user profile (top right) → Preferences
   - Enable "Extension Developer Features"
   - Navigate to Extensions from the side nav
   - Click the 3 dots (top right) → Developer Load
   - Paste the URL from step 3, select "Persist"
   - Reload the page

### Testing

The extension provides a comprehensive interface for:

- **Apps Management**: Browse and install AI applications
- **Multi-cluster Operations**: Install apps across multiple clusters
- **Lifecycle Management**: Upgrade, configure, and uninstall applications
- **Status Monitoring**: Real-time status tracking and error reporting

### Building for Production

```bash
yarn build-pkg suseai-rancher-ext --mode production
```

## Contributing

When contributing to this extension:

1. **Follow Standard Patterns**: Use the established domain model and store patterns
2. **Use Standard Components**: Prefer Rancher's UI components over custom implementations
3. **Type Safety**: Maintain strict TypeScript usage, avoid `any` types
4. **Feature Flags**: Use feature flags for new functionality
5. **Testing**: Ensure all functionality works across multi-cluster scenarios

## Configuration

The extension supports various configuration options through the config system:

- **Feature Flags**: Control feature availability per cluster version
- **Table Headers**: Customize ResourceTable displays
- **Settings**: Application-wide settings and preferences
- **Documentation Links**: Contextual help and documentation

## Troubleshooting

### Common Issues

1. **Extension not loading**: Verify URL in developer tools console
2. **Build errors**: Check Node.js version compatibility
3. **API errors**: Verify cluster permissions and connectivity

### Debug Mode

Enable debug logging in development:

```bash
NODE_ENV=development yarn build-pkg suseai-rancher-ext
```
