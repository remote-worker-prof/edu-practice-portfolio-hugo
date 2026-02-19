# Настройка кэша изображений Hugo

Добавьте в конфиг Hugo блок `caches` с переносом кэша изображений в `:cacheDir/images`.

## TOML
```toml
[caches]
  [caches.images]
    dir = ':cacheDir/images'
```

## YAML
```yaml
caches:
  images:
    dir: :cacheDir/images
```

## JSON
```json
{
  "caches": {
    "images": {
      "dir": ":cacheDir/images"
    }
  }
}
```
