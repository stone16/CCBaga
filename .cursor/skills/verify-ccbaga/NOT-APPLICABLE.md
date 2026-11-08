# Verification skill N/A

CCBaga has no `verify-ccbaga` skill. Fleet init did not generate one. `create-verification-skill` says a skill that was never executed is a draft, and this checkout cannot launch the app.

## What a person touches

The primary app is CCBaga-A, one macOS window. The sidebar opens Easy setup, then Network, Claude cleanup, Browser, and Computer. The second app is CCBaga-B, the companion Fingerprint Chromium, plus the local test page `browser.html`. Scripts under `scripts/` package and release the apps. They are not the product a person drives.

## How it starts

The documented command is `bun run tauri dev`, after `bun install --frozen-lockfile`, `python3 scripts/fetch-browser.py`, `sh scripts/make-icons.sh`, and `sh scripts/package-browser.sh`. Vite listens at `http://localhost:1420` with `strictPort` true. The window is usable only when the native host is up. `src-tauri/tauri.conf.json` sets `minimumSystemVersion` to `12.0` and bundles an `.app`. `src-tauri/build.rs` compiles Objective-C and links AppKit, Security, CoreLocation, and other macOS frameworks. The README says macOS 12 or later on Apple Silicon, and that Intel, Windows, and Linux application builds are not supported.

## How an agent would drive it

The repo has no Playwright, Cypress, expect, or PTY harness. Sidebar controls take their accessible names from the YAML catalogs, including `主要页面` and the page titles. Actions call Tauri `invoke`. `browser.html` has no Tauri command access. It reports measurements to the host over a random-token loopback session. `bun test` checks models and catalogs. It does not open the window.

## What would count as proof

On an Apple Silicon Mac, proof is the window after a real sidebar action, plus the native side effect. That side effect is a file removed, a setting read back, or a browser report received. Cleanup logs stay in memory. Do not treat a Vite-only page as the app.

## Isolation

Two instances are not safe. `src-tauri/src/main.rs` registers `tauri_plugin_single_instance`. The browser profile is the shared app-data directory `browser/profile`. A future run must refuse the installed copies in `/Applications`.

## Measured blocker

`uname -srm` on this runner printed `Linux 6.12.94+ x86_64`. `command -v bun`, `command -v xcrun`, and `command -v orca` each exited 1. `clang -fsyntax-only -x objective-c` on `#import <AppKit/AppKit.h>` exited 1 with `fatal error: 'AppKit/AppKit.h' file not found`.

## Decision

Do not add `.cursor/skills/verify-ccbaga/SKILL.md` until an Apple Silicon Mac can launch CCBaga-A, drive one real sidebar feature, and keep the proof after cleanup. Write the skill from that run. Do not weaken a journey so a stand-in passes.

`/maintain-verification-skill` has nothing to maintain until that skill exists.
