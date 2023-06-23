# Change Log

## 1.2.0

### Patch Changes

- Bump versions on all dependencies to latest

## 1.1.0

### Patch Changes

- Added CHANGELOG
- Combined the two components (Turnstile and TurnstileElement) back into one component.  Using the two separate elements is no longer necessary since we are using a client-side svelte store ($turnstileLoaded) to track whether turnstile is loaded, and this store can be accessed across all component instances.
