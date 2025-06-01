# Zadanie 2 - Pipeline GitHub Actions

## Architektura Pipeline

### 1. Tagowanie obrazów

Pipeline wykorzystuje inteligentny system tagowania:

```yaml
tags: |
  type=ref,event=branch          # main, develop
  type=ref,event=pr              # pr-123
  type=semver,pattern={{version}} # v1.2.3
  type=semver,pattern={{major}}.{{minor}} # v1.2
  type=semver,pattern={{major}}   # v1
  type=sha,prefix=sha-,format=short # sha-abc1234
  type=raw,value=latest,enable={{is_default_branch}} # latest
```

**Uzasadnienie**: Ten schemat zapewnia:
- Śledzenie wersji semantycznych (semver)
- Identyfikację commitów przez SHA
- Łatwe zarządzanie środowiskami (latest dla main)
- Izolację PR-ów

### 2. Strategia Cache

Wykorzystujemy **registry cache** z trybem `max`:

```yaml
cache-from: type=registry,ref=username/cache:cache
cache-to: type=registry,ref=username/cache:cache,mode=max
```

**Uzasadnienie**:
- **Registry cache** jest współdzielony między runnerami
- **Mode=max** cache'uje wszystkie warstwy (nie tylko finalne)
- Znacznie przyspiesza buildy (szczególnie dla multi-arch)
- Zgodne z [Docker best practices](https://docs.docker.com/build/cache/backends/registry/)

### 3. Skanowanie CVE

Wybrano **Trivy** zamiast Docker Scout:

**Zalety Trivy**:
- Darmowy i open-source
- Integracja z GitHub Security
- Wsparcie dla SARIF
- Szeroka baza danych CVE
- Aktywnie rozwijany przez Aqua Security

**Porównanie z Docker Scout**:
- Docker Scout wymaga płatnej subskrypcji dla CI/CD
- Trivy ma lepszą integrację z GitHub Actions
- Trivy oferuje więcej formatów wyjściowych

## Wykorzystane technologie

- **Go 1.21** - backend
- **Docker multi-stage builds** - optymalizacja obrazu
- **UPX** - kompresja binarek
- **GitHub Actions** - CI/CD
- **Trivy** - skanowanie bezpieczeństwa
- **GitHub Container Registry** - hosting obrazów

## Autor

**Jakub Nowosad**