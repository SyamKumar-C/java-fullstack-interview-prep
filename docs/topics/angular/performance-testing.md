# Angular Performance and Testing

## Performance
Lazy-load routes/features; use OnPush where suitable; use track/trackBy for stable list identity; avoid expensive template functions; use pure pipes; optimize images and bundles; define cache invalidation rules.

## Testing
Unit-test services and pure logic. Test component behavior through DOM/public API. Use HttpTestingController for HTTP. Test guards/routes with router testing utilities. Keep tests deterministic and avoid real network dependencies.

**Interview:** Why trackBy? It helps Angular identify list items by stable identity and avoid recreating DOM nodes unnecessarily.
