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

## Häufige Use Cases

### Szenarien
1. **Development/Testing**: Schnelle Tests ohne externe Cache-Dependency
2. **Small Imports**: Imports < 10.000 Produkten mit flachen Hierarchien
3. **Single-Process Imports**: CLI-basierte Imports ohne Parallelisierung
4. **Memory-Rich Environment**: Server mit 16GB+ RAM für größere Caches

### Performance-Profile
- **1.000 Items**: ~10MB Memory, ~50-100ms Setup
- **10.000 Items**: ~100MB Memory, ~100-200ms Setup
- **50.000 Items**: ~500MB Memory, ~200-500ms Setup (Warnung!)
- **100.000+ Items**: Nicht empfohlen - zu viel Memory

## Performance-Überlegungen

- **Memory-Footprint**: Lineare Skalierung - 10KB pro Item durchschnittlich
- **Lookup-Speed**: O(1) UUID-Lookup (~0.1ms pro Zugriff)
- **GC-Pressure**: Große Collections verursachen GC-Pausen bei >50% Heap-Auslastung
- **Optimal für**: Imports bis 50k Items auf Maschinen mit 8GB+ RAM
- **Nicht optimal für**: Production mit >100k Items oder Multi-threaded

## Verwandte Module

- **import-cache**: Definiert Cache-Interfaces die dieses Modul implementiert
- **import-dbal-collection**: Nutzt `import-cache-collection` für DBAL-Caching
- **import-cache-collection** ← **diese Datei**
- **import**: Core Framework nutzt Cache für Performance

## Troubleshooting & FAQ

**Q: "Out of Memory" bei großem Import**
- A: Reduziere Batch-Size oder nutze Redis-Cache statt In-Memory. Max: PHP Memory-Limit minus 20% Puffer.

**Q: Cache-Lookups werden langsam**
- A: Das ist normal bei >50k Items. Nutze `import-cache-redis` für größere Datenmengen.

**Q: "UUID not found" Fehler**
- A: Cache-Key nicht registriert. Prüfe UUID-Generation: `$cacheAdapter->set('product_' . $uuid, $data)`

## Bekannte Einschränkungen

- **In-Memory Only**: Daten gehen verloren bei Prozess-Ende
- **Nicht Thread-Safe**: Nicht für Multi-Threaded Imports geeignet
- **Keine Persistierung**: Cache wird nicht auf Disk gespeichert
- **Memory-Limits**: Kann bei sehr großen Imports zu Memory-Problemen führen

## Zusammenfassung

`import-cache-collection` ist ein **Tier 1 Modul**, das eine einfache, schnelle In-Memory Cache-Implementierung bietet. Es ist ideal für Single-Threaded Imports und Development, aber nicht für Production mit großen Datenmengen.

**Für Agenten:** Verstehe dieses Modul als **konkrete Cache-Implementierung** mit In-Memory Storage.
