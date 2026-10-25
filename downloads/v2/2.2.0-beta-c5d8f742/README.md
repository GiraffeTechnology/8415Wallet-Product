# V2 beta installation candidate

**OFFLINE CANDIDATE — not installed, activated, or production-accepted.**

This directory contains the V2 installation kit only. It is a review-branch candidate, not a published Release. V3 packages and the full source archive are not included.

## Artifact and integrity

- [V2 installation kit](8415wallet-v2-install-c5d8f7423dddaad0c2fff3d13da2a9079834fb32.tar.gz)
- Size: 5,826,348 bytes
- SHA256: `b6354c9557062dc9c5c27cec8633fb346e9e6a18ba671c3c5bb2f903de63ea37`
- [Checksum file](SHA256SUMS)

Verify the checksum before extracting and follow the instructions and notices inside the kit. Installation requires operator-supplied, already-authorized tenant, origin, runtime configuration and storage bindings. The kit does not activate a service.

## Provenance and scope

Source baseline: `5b62118cceec9edd4201072409b85f4012185756`.
The package was built from a modified local source snapshot with content tree `c5d8f7423dddaad0c2fff3d13da2a9079834fb32`; that tree is not a published source commit or a served version.

The kit includes V2 UI and V2 authentication runtime packages, credential-store format 4 and `8415wallet-auth-state/7`. Same-address wallet login authentication supports unbind/rebind without changing asset membership. Replacement with a different authentication signer address is unsupported and requires a defined product policy.

Ordinary NFT sale, when enabled, requires the separately installed external `@artfi/task-listing-runtime/server` provider and its exact manifest binding. The private provider implementation is not bundled or automatically installed. Credentials, private configuration and V3-exclusive execution code are not included.

## Validation boundary

Offline archive integrity and focused local synthetic/HTTP checks are not full remote CI or complete product acceptance. Authoritative runtime/store/provider data and bindings, served SHA, actual browser/device/hardware behavior and on-chain acceptance have not been established. This candidate does not establish completion of all product requirements.

Existing [repository trial terms](../../../LICENSE) and the component notices included in the kit remain applicable. No additional license grant is made here.
