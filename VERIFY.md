# How to verify a session

You need: `openssl` (built into macOS and Linux; included with Git for Windows) and,
optionally, Python with `pip install opentimestamps-client`.

## 1. Find the session's fingerprint

Open `ledger.csv` and find the row for the session (for example `2026-11-02`). Note:

- `sha256`: the SHA-256 fingerprint of that session's forecast file
- `deadline_utc`: the market open; the forecast must be timestamped before it
- `tsa`: which timestamp authority signed it

## 2. Check the RFC 3161 timestamp (when the fingerprint existed)

Show the signed time:

    openssl ts -reply -in proofs/2026-11-02.tsr -text

The `Time stamp:` line must be earlier than `deadline_utc`, and the `Message data:` bytes must
equal the `sha256` value.

To check the signature too, for tokens from freetsa.org, download `cacert.pem` and `tsa.crt`
from https://freetsa.org and run:

    openssl ts -verify -digest <sha256> -in proofs/2026-11-02.tsr -CAfile cacert.pem -untrusted tsa.crt

It should print `Verification: OK`.

For tokens from DigiCert (the fallback; the `tsa` column says which), first list the
certificates inside the token:

    openssl ts -reply -in proofs/2026-11-02.tsr -token_out -out token.der
    openssl pkcs7 -inform DER -in token.der -print_certs

then download the DigiCert root certificate named as the last issuer from
https://www.digicert.com/kb/digicert-root-certificates.htm (save it as `digicert-root.pem`) and run:

    openssl ts -verify -digest <sha256> -in proofs/2026-11-02.tsr -CAfile digicert-root.pem

## 3. Check the OpenTimestamps proof (anchored in Bitcoin)

    ots info proofs/2026-11-02.json.ots

shows the fingerprint and its Bitcoin attestation once complete (usually within a day).
Full verification needs a Bitcoin node (`ots verify -d <sha256> proofs/2026-11-02.json.ots`)
or, once forecast files are published, the file itself at https://opentimestamps.org.

## 4. Once forecast files are published: check the file and the chain

    sha256sum forecasts/2026-11-02.json        (macOS: shasum -a 256)

must equal the ledger's `sha256`. Each file also contains `prev_sha256`, the fingerprint of the
previous session's file, so no session can be added, removed or changed later without
breaking the chain.
