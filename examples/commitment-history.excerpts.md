# Commitment links and effective-time history

These two verbatim TypeScript excerpts show a pure local check used by the
8415Wallet history view. They compare adjacent admitted entries without reading
a network, signing, transferring assets, or changing an entry.

Source: `8415Wallet`, commit
`d4a20a140aaa0671ea2a8b1413180606e9f91ec7`, file `src/wallet/history.ts`,
Git blob `9509b3b009696d1dc46c493d0d02eecec411a094`.

The complete selected code appears below; access to the source repository is not
needed to read it. These are source excerpts, not a standalone executable or an
installation package. Imports, the surrounding reader and the `RegisterEntry`
type are omitted. No test execution is claimed for these excerpts.

## Inputs and scope

- `entry`, `previous` and `next` are already decoded register entries for one token
  in admission order. `undefined` marks the absence of a predecessor or successor.
- `version`, `effectiveAt` and `supersededAt` are bigint values. Effective times use
  the protocol's timestamp scale; `supersededAt === 0n` denotes an open interval.
- `recordCommitment` and `previousCommitment` are normalized 32-byte values.
  `ZERO_BYTES32` is the all-zero 32-byte value. The function compares values; it
  does not calculate a commitment from a private record or reveal its contents.
- The helper assumes decoded, domain-valid inputs and correctly chosen
  neighbors. It does not validate input widths, prove that every entry
  was supplied, check commitment uniqueness, or authenticate the data source.

## Report a fault without rewriting history

### `src/wallet/history.ts`, lines 22–29

```typescript
/** Why a link in the commitment chain does not hold. */
export type LinkFault =
  | 'first-entry-has-nonzero-previous'
  | 'previous-commitment-mismatch'
  | 'version-not-consecutive'
  | 'effective-time-not-increasing'
  | 'interval-not-closed-at-successor'
  | 'latest-entry-has-closed-interval';
```

### `src/wallet/history.ts`, lines 113–147

```typescript
function checkLink(
  entry: RegisterEntry,
  previous: RegisterEntry | undefined,
  next: RegisterEntry | undefined,
): LinkFault[] {
  const faults: LinkFault[] = [];

  if (previous === undefined) {
    if (entry.version !== 1n) faults.push('version-not-consecutive');
    if (entry.previousCommitment !== ZERO_BYTES32) {
      faults.push('first-entry-has-nonzero-previous');
    }
  } else {
    if (entry.previousCommitment !== previous.recordCommitment) {
      faults.push('previous-commitment-mismatch');
    }
    if (entry.version !== previous.version + 1n) {
      faults.push('version-not-consecutive');
    }
    if (entry.effectiveAt <= previous.effectiveAt) {
      faults.push('effective-time-not-increasing');
    }
  }

  // An entry's interval is closed by the register at its successor's effective
  // time, not at the moment the chain learned of it.
  if (next !== undefined && entry.supersededAt !== next.effectiveAt) {
    faults.push('interval-not-closed-at-successor');
  }
  if (next === undefined && entry.supersededAt !== 0n) {
    faults.push('latest-entry-has-closed-interval');
  }

  return faults;
}
```

For the first entry, the helper checks version 1 and the zero previous commitment.
For later entries, it compares the previous commitment, checks the next version
number, and requires a strictly later effective time. It also checks that an
entry's interval closes at its successor's effective time and that the latest
entry remains open. Faults are returned as labels; no input is repaired or hidden.

An empty fault list means only that these comparisons passed for the supplied
neighbors. It does not establish authenticity of an off-chain record, global
uniqueness, holder identity, historical finality, or legal title. Holder resolution
and `isFinalAsOf` remain separate contract answers. No RPC snapshot, contract proof,
transaction authorization, or complete conformance validator is provided here.

## Provenance and permission

The two code blocks are unchanged selections made on 2026-10-19. Their exact UTF-8
bytes, including each final newline, are hashed in
[source-excerpts.json](source-excerpts.json). The explanatory prose is new.

Retain the accompanying
[source licence notice](notices/SOURCE-LICENSE.txt).
The selected source file has no file-level SPDX notice. The scoped non-commercial
permission for public examples is in [LICENSE, Section 4](../LICENSE); applicable
independent rights and third-party terms remain unchanged. This example does not
extend that permission to the rest of the wallet.
