# Zehnama releases

Signed Windows installers for **Zehnama**, an electronic patient record for
private psychiatric practices. This repository holds releases only: the
installers, their checksums and release notes. There is no source code here.

Installed copies of Zehnama check this repository for updates. They read it
anonymously and verify every download themselves before installing it (see
below), so nothing here needs to be trusted blindly.

## Channels

| Channel | What it is | On GitHub |
|---|---|---|
| **Stable** | Releases approved for clinical use | Full releases, `vX.Y.Z` |
| **Beta** | Pre-releases for testing ahead of the next stable release | Pre-releases, `vX.Y.Z-beta.N` |

A copy on the stable channel is offered only stable releases. A copy on the
beta channel is offered the highest version of either kind. Nobody is ever
offered a version lower than the one they are running.

## Verifying a download

Each release carries exactly two files: `zehnama_setup_<version>.exe` and
`SHA256SUMS.txt`. Before you install, in PowerShell, in the folder you
downloaded to:

```powershell
# 1. The checksum: must equal the line in SHA256SUMS.txt
Get-FileHash .\zehnama_setup_<version>.exe -Algorithm SHA256
Get-Content .\SHA256SUMS.txt

# 2. The signature: Status must be Valid, the signer's subject must be
#    Zehnama's publisher (below), and it must carry a timestamp
Get-AuthenticodeSignature .\zehnama_setup_<version>.exe |
  Format-List Status, SignerCertificate, TimeStamperCertificate

# 3. The version inside the installer: must equal the release's version
(Get-Item .\zehnama_setup_<version>.exe).VersionInfo.ProductVersion
```

**Expected publisher:** `<certificate subject - filled in when this repository is set up>`

When you run the installer, Windows asks for permission and names the
verified publisher. If it names nobody, or anyone else, do not install it.

The checksum proves the file is the one published here. The signature proves
it was built and signed by Zehnama's publisher: anyone able to change a file
here could change the checksum too, but not the signature. The version
inside the installer is part of the signed file, so an old installer cannot
be passed off as a new release.

## Each release is checked twice

The release script downloads every release back from GitHub while it is
still a draft and verifies it before publishing it. Then the
[validate-release](.github/workflows/validate-release.yml) workflow checks the
published release again on a clean Windows machine: the assets, the checksum,
the signature and its publisher, the installer's version and the pre-release
flag. A release that fails is turned back into a draft at once, which hides
it from every installed copy.

## Rules for this repository

- **Never delete or move a tag.** A tag names one installer forever. A bad
  release is withdrawn - `gh release edit vX --draft=true`, which hides it
  from every installed copy at once - and superseded by the next version
  number. A version number is never reused.
- Releases are published only by the maintainer, through the release script.
- Never rename this repository or its organisation: installed copies look
  for it by name.
- If a release changed the practice's data (its notes have a **Database**
  section), going back means installing the older installer and restoring the
  backup taken **before** the update. Never install an older version over data
  a newer one has opened.

## Licences

Third-party components bundled in the installer are listed in
[NOTICE](NOTICE) and [THIRD_PARTY_LICENSES.txt](THIRD_PARTY_LICENSES.txt).

> **Scratch repository for a release dry run (docs/APP_RELEASE_PROCESS.md). Not a distribution channel; to be deleted.**
