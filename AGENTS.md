# AGENTS.md - import-cache-collection

## Zweck & Verantwortung

Das `import-cache-collection` Modul bietet eine **konkrete Cache-Implementierung** basierend auf In-Memory Collections. Es ist ein **Tier 1 Modul** und implementiert die Interfaces aus `import-cache`.

**Hauptverantwortung:**
- Implementierung von `CacheAdapterInterface` mit Collections
- In-Memory Caching für Import-Operationen
- UUID-basierte Cache-Keys für Eindeutigkeit

## Architektur & Design Patterns

### Implementierungen
- **ConfigurableCacheAdapter**: Konfigurierbare Cache-Implementierung
- **LocalCacheAdapter**: Lokale In-Memory Cache
- **CacheAdapterTrait**: Gemeinsame Cache-Logik

### Verwendete Patterns
- **Adapter Pattern**: Implementiert `CacheAdapterInterface`
- **Trait Pattern**: Gemeinsame Logik via `CacheAdapterTrait`
- **Factory Pattern**: Für Cache-Adapter-Erstellung

### Externe Dependencies
- **ramsey/uuid** - Für UUID-basierte Cache-Keys

## Abhängigkeiten

### Externe Pakete
- **ramsey/uuid** ^1.0 - UUID-Generierung für Cache-Keys

### TechDivision Dependencies
- **import-cache** ^2.0.0 - Implementiert Cache-Interfaces

### Abhängig von diesem Modul (2 Reverse Dependencies)
1. **import** - Core Framework nutzt Cache-Implementierung
2. **import-cli-simple** - Transitiv über andere Module

## Wichtige Entry Points

### Cache Adapter Klassen
```php
// Configurable Cache Adapter
ConfigurableCacheAdapter::get($key): mixed
ConfigurableCacheAdapter::set($key, $value): void
ConfigurableCacheAdapter::has($key): bool
ConfigurableCacheAdapter::remove($key): void

// Local Cache Adapter
LocalCacheAdapter::create(): CacheAdapterInterface
```

### Verwendungsbeispiel
```php
// In Importern
$cacheAdapter = new ConfigurableCacheAdapter();
$cacheAdapter->set('product_' . $sku, $productData);
if ($cacheAdapter->has('product_' . $sku)) {
    $data = $cacheAdapter->get('product_' . $sku);
}
```

## Events & Extension Points

**Keine Events** - Tier 1 Implementierungs-Modul

## Hints für KI-Agenten

### Wichtig zu verstehen
1. **Tier 1 Modul**: Konkrete Implementierung von `import-cache` Interfaces
2. **In-Memory Caching**: Daten werden im RAM gespeichert
3. **UUID-Keys**: Cache-Keys sind UUID-basiert für Eindeutigkeit
4. **Performance-fokussiert**: Für schnelle Import-Operationen

### Bei Änderungen
- **Implementierungs-Details**: Können geändert werden ohne Interface-Änderungen
- **Performance**: Beachte Memory-Footprint bei großen Datenmengen
- **Thread-Safety**: Nicht thread-safe, nur für Single-Threaded Imports

### Implementierungs-Hinweise
- Nutze `ConfigurableCacheAdapter` für flexible Cache-Konfiguration
- Beachte Memory-Limits bei großen Imports
- Erwäge Redis-Implementierung für verteilte Caches

## Bekannte Einschränkungen

- **In-Memory Only**: Daten gehen verloren bei Prozess-Ende
- **Nicht Thread-Safe**: Nicht für Multi-Threaded Imports geeignet
- **Keine Persistierung**: Cache wird nicht auf Disk gespeichert
- **Memory-Limits**: Kann bei sehr großen Imports zu Memory-Problemen führen

## Zusammenfassung

`import-cache-collection` ist ein **Tier 1 Modul**, das eine einfache, schnelle In-Memory Cache-Implementierung bietet. Es ist ideal für Single-Threaded Imports und Development, aber nicht für Production mit großen Datenmengen.

**Für Agenten:** Verstehe dieses Modul als **konkrete Cache-Implementierung** mit In-Memory Storage.
