# ClamAV Verification Evidence

This document records local ClamAV verification performed for the DMS
(Document Management System) project. It is safe to commit to GitHub because
it contains no EICAR test strings, no real malware, and no executable test
fixtures.

## Scope

- Local ClamAV engine (`clamscan`) with official signature database
- DMS real-ClamAV integration test: `tests/Feature/ClamAvIntegrationTest.php`

## Test file used locally (not committed)

The industry-standard **EICAR test string** was used locally only. It was saved
to a local file named `eicar.com`, scanned with ClamAV, and then deleted. The
test string is intentionally redacted from this document and is **not** present
in the repository.

## Sanitized scan result (template)

The terminal output below shows the expected shape of a successful detection.
Actual timestamps and virus-definition counts will vary by installation.

```
$ clamscan eicar.com
eicar.com: Eicar-Test-Signature FOUND
----------- SCAN SUMMARY -----------
Known viruses: 8,xxx,xxx
Engine version: 0.10x.x
Scanned directories: 0
Scanned files: 1
Infected files: 1
Data scanned: 0.00 MB
Data read: 0.00 MB (ratio 0.00:1)
Time: 0.00s (0 m 0 s)
```

A clean (benign) test file returns `OK` with `Infected files: 0`.

## DMS integration test

Run locally with a real ClamAV installation:

```powershell
$env:DMS_REAL_CLAMAV_TESTS = '1'
php artisan test --filter=ClamAvIntegrationTest --compact
```

What the test verifies:

1. A benign synthetic upload is `Pending` until scanned, then becomes `Clean`
   and is downloadable with an audit entry.
2. The EICAR test string is scanned by the real engine and flagged
   (`Quarantined` or fail-closed `Failed` on Windows where the OS blocks the
   file first).
3. The flagged file is not served or previewed (fail-closed behavior).

## Attachments for your leader

Attach a **screenshot** of your local terminal showing:

```
eicar.com: Eicar-Test-Signature FOUND
Infected files: 1
```

Do not paste the EICAR string into GitHub issues, pull requests, or commits.
GitHub security scanners may flag the repository if the string is present.

## Safety statement

No real malware or "poisoned" files were used, created, or committed. The EICAR
test string is a harmless, industry-standard antivirus test file recognized by
ClamAV and all major AV vendors.
