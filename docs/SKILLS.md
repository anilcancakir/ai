# AI Skills

Sixteen mirrored skills are available in the registry. Each one is a structured knowledge file that an AI coding agent loads to generate correct code on the first try.

Skills are fetched automatically via `skills/index.json` (OpenCode) or the Claude Code plugin marketplace. See [README.md](../README.md) for installation options.

Every skill here is a mirror of the upstream package's own `skills/` directory, delivered by [`sync.yml`](../.github/workflows/sync.yml) when that package publishes a release. The upstream repo is the place to file an issue or send a fix. The synced version of each skill is the `version:` field in its `SKILL.md` frontmatter, so it is not repeated below.

---

## wind-ui

**Package:** `wind_kit`
**Upstream repo:** https://github.com/anilcancakir/wind_kit

### Triggers on

Writing, editing or auditing Flutter UI in an app that uses `wind_kit`: a `className` string, a W-prefix widget, the Wind theme, or an overflow error.

### What it provides

Utility-first Flutter styling. Every visual decision lives in a `className` string, parsed at build time into an immutable `WindStyle` and cached per breakpoint, brightness, platform and state set. The skill teaches the 27 public widgets (`WDiv`, `WText`, `WButton`, `WInput`, `WSelect`, `WPopover`, the `WForm*` wrappers and more), the token catalogue with its arbitrary values, and the stackable prefixes: responsive, `dark:`, platform (`ios:`, `web:`, ...) and state. It also covers `WindRecipe` and `WindSlotRecipe` variant composition, `WDynamic` JSON trees, and the Flutter constraint rules behind most overflow errors.

Two contracts do the heavy lifting: every colour token carries a `dark:` peer in the same className, and conditional styling routes through `states:` plus prefixed classes rather than interpolated Dart.

Nine reference files: `tokens`, `widgets`, `forms`, `layouts`, `theme`, `tailwind-divergence`, `dynamic`, `debug`, `design-culture`.

### Recommended companion skills

`magic-framework` for architecture, routing, and data layers.

---

## magic-framework

**Package:** `magic`
**Upstream repo:** https://github.com/anilcancakir/magic

### Triggers on

Any file that imports `package:magic/magic.dart` or `package:magic/testing.dart`, or work touching `Magic.init`, a facade, a Model, a `MagicController`, a `MagicView`, `MagicFormData`, a `FormRequest`, a `MagicAction`, a `Repository`, a `SessionScope`, a ServiceProvider, or the `magic:dowel make:*` CLI. It does not trigger for plain Flutter or Wind-only UI with no `package:magic` import.

### What it provides

The Laravel-inspired core of a Magic app: the IoC container, 13 facades (`Auth`, `Http`, `Config`, `Cache`, `Log`, `Event`, `Lang`, `MagicRoute`, `Gate`, `Session`, `Vault`, `Storage`, `Launch`), an Eloquent-style ORM, service providers and the boot lifecycle, and reactive controllers with `MagicStateMixin` and `RxStatus`. It also covers GoRouter-backed routing with resource routes and middleware, forms and validation including async rules, `MagicAction` writes, `Repository` row caches, `SessionScope` tenant resets, client-side Gate abilities and policies, and the test surface (`MagicTest`, facade fakes).

`DB`, `Schema`, `Echo`, `Pick` and `Crypt` are not core facades. The SQLite, broadcasting, picker and encryption layers are plugins, each with its own skill below.

The CLI runs as `dart run magic:dowel <command>`: `magic:install`, the 17 core `make:*` generators (`make:component` among them), and the design commands `previews:refresh`, `design:sync` and `design:lint`. `key:generate` comes from `magic_encryption`; `make:migration`, `make:seeder` and `make:factory` come from `magic_local_storage`.

Twenty-five reference files, including one per ecosystem plugin: `magic_deeplink`, `magic_device`, `magic_devtools`, `magic_encryption`, `magic_local_storage`, `magic_notifications`, `magic_payments`, `magic_picker`, `magic_sentry`, `magic_social_auth`, `magic_socket`, `magic_starter`.

### Recommended companion skills

`wind-ui` for all UI styling; `dowel` for the CLI substrate and MCP wiring; the `magic-*` skill of each plugin the app depends on.

---

## tapir

**Package:** `tapir` (its skill replaces `fluttersdk-dusk`)
**Upstream repo:** https://github.com/anilcancakir/tapir

### Triggers on

Driving, checking or profiling a running Flutter UI through `tapir_*` MCP tools or `tapir:*` CLI commands. App lifecycle and MCP wiring belong to `dowel`; a widget test is `flutter_test`.

### What it provides

An E2E driver for a Flutter app that is already running. The app registers `ext.tapir.*` VM Service extensions, reached as `tapir_*` MCP tools or `./bin/dowel tapir:*` commands; both hit the same handler, though CLI flags can differ from tool parameters. The agent reads the screen (`snap`, `observe`, `screenshot`), acts (`tap`, `type`, `fill`, `drag`, `scroll`, `navigate`) and profiles (`perf_begin`, `perf_end`). A snapshot is a YAML Semantics tree with `e<N>` refs; `tapir_find` and `tapir_observe` mint re-resolvable `q<N>` handles that survive a rebuild where an `e<N>` does not.

Gestures pass a six-step actionability gate in a frozen order. Its failure reasons are substring-parseable (`defunct`, `not enabled`, `zero rect`, `off-viewport`, `not stable`, `obscured by`), so an agent can branch on why a tap was refused instead of retrying blindly.

Four reference files: `mcp-tools`, `cli-commands`, `actionability-and-refs`, `workflows`.

### Recommended companion skills

`dowel` (required substrate), `crumbs` for reading side effects after a gesture.

---

## crumbs

**Package:** `crumbs` (its skill replaces `fluttersdk-telescope`)
**Upstream repo:** https://github.com/anilcancakir/crumbs

### Triggers on

Any `crumbs_*` MCP tool call or `crumbs:*` CLI command used to see what a running Flutter app did: HTTP traffic, logs, exceptions, debug dumps, events, gate checks, queries, cache operations or frame timings.

### What it provides

A passive runtime inspector. Watchers and adapters in the app write records into in-memory ring buffers (500 records each by default), served as `ext.crumbs.*` VM Service extensions and read through `crumbs_*` MCP tools and `./bin/dowel crumbs:*` commands. Most buffers are opt-in: events, gates, queries and caches fill only through `MagicCrumbsIntegration` from `magic_devtools`. Credential keys are masked before a record is buffered, and an optional JSONL timeline on disk holds only records a host redactor has passed.

It pairs with tapir: tapir drives the app, crumbs reads what the app did in response, so a verification step does not depend on what happens to be visible on screen.

Four reference files: `mcp-tools`, `cli-commands`, `records`, `workflows`.

### Recommended companion skills

`dowel` (required substrate), `tapir` for driving the UI whose side effects you are reading.

---

## dowel

**Package:** `dowel` (its skill replaces `fluttersdk-artisan`)
**Upstream repo:** https://github.com/anilcancakir/dowel

### Triggers on

Running, reloading or evaluating in a Flutter app through dowel (`dowel_*` MCP tools, `./bin/dowel` or `dart run dowel`), installing a dowel plugin, or fixing dowel MCP wiring or session errors.

### What it provides

The substrate tapir and crumbs plug into: a Dart CLI framework and a stdio MCP server in one binary. Ten substrate MCP tools start, stop, restart, hot reload, inspect and evaluate a running app; `dowel_tinker` takes one Dart expression. `install`, `make:*`, `plugin:install` and `mcp:*` stay CLI-only. A per-project session under `~/.dowel/sessions/<hash>/` records the app's pid, VM Service URI and FIFO, so an MCP server attaches to an app that is already running.

One wiring detail decides whether plugin tools appear at all: `tapir_*` and `crumbs_*` surface only through the dispatcher entry (`./bin/dowel mcp:serve`), never through the substrate-only `dart run dowel:mcp`.

Five reference files: `cli-commands`, `mcp-tools`, `state-and-recovery`, `testing-commands`, `tinker-eval`.

### Recommended companion skills

`tapir` and `crumbs` are dowel plugins; load them when the task touches E2E driving or runtime inspection.

---

## magic-device

**Package:** `magic_device`
**Upstream repo:** https://github.com/anilcancakir/magic_device

### Triggers on

Code that imports `package:magic_device`, or needs `DevicePower`, `WindowFullscreen`, a charging, unplugged or away check, or a macOS fullscreen toggle.

### What it provides

`DevicePower` answers whether the device is on mains power and whether the viewer is away, so a heavy background job knows when it may run. `WindowFullscreen` reads and toggles macOS window fullscreen. The package has no service provider, config, facade or CLI command, and it does not depend on `magic`.

### Recommended companion skills

None required; `magic-framework` when the host is a Magic app.

---

## magic-notifications

**Package:** `magic_notifications`
**Upstream repo:** https://github.com/anilcancakir/magic_notifications

### Triggers on

Code that imports `package:magic_notifications`, or touches `Notify`, `NotificationChannel`, `PushDriver`, OneSignal push, `push_state` or a `notifications:*` command.

### What it provides

The `Notify` facade reads an inbox the backend writes; the client never creates inbox rows. Push goes through OneSignal (`OneSignalDriver` on iOS and Android, `OneSignalWebDriver` on web), and new rows arrive over a `magic_socket` channel with `NotificationPoller` as the fallback. It ships the inbox and preference screens, a `database` and a `push` channel (there is no mail channel), and the `notifications:install` and `notifications:doctor` commands.

### Recommended companion skills

`magic-framework`; `magic-socket` for realtime delivery; `magic-deeplink` to route a tapped push.

---

## magic-deeplink

**Package:** `magic_deeplink`
**Upstream repo:** https://github.com/anilcancakir/magic_deeplink

### Triggers on

Code that imports `package:magic_deeplink`, or touches `DeeplinkManager`, `RouteDeeplinkHandler`, the deeplink config or a `deeplink:` command.

### What it provides

`DeeplinkServiceProvider` wires `app_links` into `DeeplinkManager`, which offers each incoming `Uri` to its handlers in registration order; the first that claims it wins. `RouteDeeplinkHandler` maps a path pattern onto a Magic route, and a tapped push from `magic_notifications` enters the same chain. `deeplink:generate` writes `apple-app-site-association` and `assetlinks.json`; `deeplink:doctor` checks the native setup, whose gaps fail silently.

### Recommended companion skills

`magic-framework`; `magic-notifications` for the push side.

---

## magic-socket

**Package:** `magic_socket`
**Upstream repo:** https://github.com/anilcancakir/magic_socket

### Triggers on

Code that imports `package:magic_socket`, or touches `Echo`, `BroadcastServiceProvider`, `broadcastingConfig`, `ListensToBroadcasts`, `BroadcastListeners`, `FakeBroadcastManager` or `X-Socket-ID`.

### What it provides

The `Echo` facade subscribes to public, private and presence channels. `ReverbBroadcastDriver` speaks the Pusher protocol to Laravel Reverb and reconnects by itself; `NullBroadcastDriver`, the default, delivers nothing. `BroadcastServiceProvider` starts the connection at boot and adds the `X-Socket-ID` header to `Http` requests. `ListensToBroadcasts` and `BroadcastListeners` share one private channel between controllers, and `Echo.fake` covers tests.

### Recommended companion skills

`magic-framework`.

---

## magic-sentry

**Package:** `magic_sentry`
**Upstream repo:** https://github.com/anilcancakir/magic_sentry

### Triggers on

Code that imports `package:magic_sentry`, or touches `MagicSentry.run`, `SentryServiceProvider`, `SentryTokenScrubber` or Sentry setup.

### What it provides

`MagicSentry.run` starts Sentry before `Magic.init` in one web-safe zone, so a boot failure is reported. `SentryServiceProvider` then wires an HTTP failure interceptor, the scope user, event breadcrumbs and the navigator observer. `SentryTokenScrubber` masks URL-borne tokens before anything is sent. Every part is inert without a DSN, and app data reaches the package only through callbacks.

### Recommended companion skills

`magic-framework`.

---

## magic-picker

**Package:** `magic_picker`
**Upstream repo:** https://github.com/anilcancakir/magic_picker

### Triggers on

Code that imports `package:magic_picker`, or touches `Pick`, `PickerServiceProvider`, `toMagicFile`, `toXFile` or `Storage.download` on mobile or desktop.

### What it provides

The `Pick` facade opens the gallery, camera, file, directory and save dialogs and answers with core's `MagicFile`; a cancel is `null` or an empty list, not an error. `PickerServiceProvider` makes `Storage.download` open the share sheet on mobile, macOS and Windows and the save dialog on Linux. `toMagicFile()` and `toXFile()` convert between the two file types.

### Recommended companion skills

`magic-framework`.

---

## magic-encryption

**Package:** `magic_encryption`
**Upstream repo:** https://github.com/anilcancakir/magic_encryption

### Triggers on

Code that imports `package:magic_encryption`, or touches `Crypt`, `EncryptionServiceProvider`, `MagicEncrypter`, `MagicDecryptException`, `APP_KEY` or `key:generate`.

### What it provides

AES-256-CBC through the `Crypt` facade, keyed either by the configured `app.key` (synchronous) or by a per-device key kept in `Vault` (async). `dart run magic:dowel key:generate` writes `APP_KEY` to `.env`. The payload is `base64(iv):base64(ciphertext)` with no MAC: it gives confidentiality at rest only, and it is not Laravel's format.

### Recommended companion skills

`magic-framework`.

---

## magic-local-storage

**Package:** `magic_local_storage`
**Upstream repo:** https://github.com/anilcancakir/magic_local_storage

### Triggers on

Code that imports `package:magic_local_storage`, or touches `DB`, `Schema`, `Migration`, `Migrator`, `Seeding.run`, `Factory`, `SyncFeed`, `DatabaseServiceProvider` or a `useLocal` model.

### What it provides

`DB` and `Schema` run queries and table changes on one SQLite connection (FFI on native, `sqlite3.wasm` on web). `Migration` classes run through `Migrator`, `Seeder` and `Factory` fill tables, and `SyncFeed` keeps a table consistent with a server. The provider binds the store a `useLocal => true` model persists through. The plugin also adds the `make:migration`, `make:seeder` and `make:factory` generators.

### Recommended companion skills

`magic-framework`.

---

## magic-payments

**Package:** `magic_payments`
**Upstream repo:** https://github.com/anilcancakir/magic_payments

### Triggers on

Code that imports `package:magic_payments`, or touches `Payments`, `WebBillingService`, `StoreBillingService`, `StoreIdentitySync` or `BillingException`.

### What it provides

The `Payments` facade reads the entitlement, plans, usage, invoices and payment method from your backend, which decides who is entitled. Two nullable rails sell: `Payments.web` (hosted checkout, plan swap, cancel, portal) on web builds, and `Payments.store` (App Store and Play purchases through RevenueCat) on iOS and Android. `StoreIdentitySync` keeps the store rail identified across sign-in and subject switches, and `payments:doctor` checks the install.

### Recommended companion skills

`magic-framework`; `dowel` for the command line.

---

## magic-social-auth

**Package:** `magic_social_auth`
**Upstream repo:** https://github.com/anilcancakir/magic_social_auth

### Triggers on

Code that imports `package:magic_social_auth`, or touches `SocialAuth`, `SocialAuthResult`, `SocialAuthButtons`, the `social_auth` config or `social:install`.

### What it provides

`SocialAuth.driver(name).signIn()` runs a native sheet (Google on iOS and Android, Apple on iOS) or a backend-hosted browser flow with PKCE, against the `magic-starter-laravel` backend. A driver answers a `SocialAuthResult` and never creates a session: the caller, or the `magic_starter` bridge, logs the user in. The skill also covers connect and confirm, `SocialAuthButtons`, and `social:install` and `social:doctor`, which write and check the native edits.

### Recommended companion skills

`magic-framework`; `magic-starter` for the sign-in bridge.

---

## magic-starter

**Package:** `magic_starter`
**Upstream repo:** https://github.com/anilcancakir/magic_starter

### Triggers on

Code that imports `package:magic_starter`, or touches `MagicStarter`, `MagicStarterConfig`, a starter view key, an `MS` component or a `starter:*` command.

### What it provides

Ready-made screens behind toggles that default to `false`: sign-in and registration flows, a settings hub, teams, the notification screens, billing over `magic_payments` and a support desk. Every screen, layout and modal resolves through a string-keyed view registry the host can override, and theme setters restyle them. The controllers call `magic-starter-laravel`'s `api/v1` routes one to one.

Four reference files: `backend-contract`, `controllers-and-routes`, `theming`, `views-and-layouts`.

### Recommended companion skills

`magic-framework`, `wind-ui`, and `magic-notifications`, `magic-payments` or `magic-social-auth` for the features that use them.
