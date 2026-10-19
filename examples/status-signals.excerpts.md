# Separate status signals

The following blocks are selected implementation excerpts. They are incomplete source fragments; omitted imports, types, helpers and surrounding code are required by the original implementation. The adjacent Node file is a separately adapted model, not these functions under test.

### Selected excerpt

```typescript
  const observedAt = await reader.chainInstant();
  const owner = await reader.ownerOf(tokenId);

  const entryCount = await reader.entryCount(tokenId);
  if (entryCount === 0n) {
    // No entries means the projection has not been initialized. The wallet
    // says so rather than presenting the position as if it were the record.
    throw new ProjectionNotInitialized(tokenId);
  }
  const latest = await reader.currentEntry(tokenId);

  const presentFinal = await reader.isFinalAsOf(tokenId, observedAt);
```

### Selected excerpt

```typescript
    tradeablePosition: { owner, disclosure: DISCLOSURE_POSITION },
    confirmedHolder: {
      holder: latest.holder,
      version: latest.version,
      effectiveAt: latest.effectiveAt,
      recordCommitment: latest.recordCommitment,
      previousCommitment: latest.previousCommitment,
      registryReference: latest.registryReference,
      entryCount,
      disclosure: DISCLOSURE_HOLDER,
    },
    alignment: {
      aligned: owner === latest.holder,
      note: owner === latest.holder ? NOTE_ALIGNED : NOTE_DIVERGED,
    },
    presentFinality: {
      instant: observedAt,
      final: presentFinal,
      latestEffectiveAt: latest.effectiveAt,
      explanation: explainPresentFinality(presentFinal, observedAt, latest.effectiveAt),
    },
    gap: await buildGapView(reader, tokenId, conformance, observedAt),
```

### Selected excerpt

```typescript
const DISPLAY_BY_STATUS: Record<Freshness, FreshnessDisplay> = {
  UNKNOWN: 'unknown',
  STALE: 'stale',
  FRESH_PENDING: 'fresh-reorg-exposed',
  FRESH_FINAL: 'reorg-safe',
};
```

These reads preserve separate owner, admitted holder, temporal finality and gap facts. They are sequential observations, not an atomic transaction authorization. The freshness mapping changes presentation only.
