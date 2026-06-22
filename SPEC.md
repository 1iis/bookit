# Bookit SPEC

> A runtime for hyperbooks.

Draft: `v0.1`  
Status: north-star specification  
Primary example: The 1iis Book of Computers  
Scope: hyperbook-agnostic

## 1. Core Proposition

`bookit` turns an ordinary computing environment into a place where **hyperbooks** can be loaded, run, inspected, modified, shared, and removed.

A **hyperbook** is a software-backed book: part text, part media, part program, part interface, part stateful learning or experience system.

A hyperbook may be:

- a practical course;
- an interactive textbook;
- a rich media album;
- a documentary;
- a game-like learning world;
- a piece of fiction with programmable structure;
- a research notebook;
- a guided software environment;
- a living manual for a real system.

The simplest metaphor:

> `bookit` is like the console.  
> A hyperbook is like the cartridge.

This is only a metaphor. Technically, a hyperbook is a folder, repository, or bundle containing content, assets, metadata, optional code, and optional state rules. But the cartridge metaphor matters because it captures the desired UX:

> You can grab a hyperbook, move it, load it, run it, inspect it, fork it, share it, and keep it.

## 2. Naming

| Name | Meaning |
|---|---|
| `bookit` | The runtime, CLI, library, and host integration layer. |
| `hyperbook` | The medium/object being run. |
| `hyperbook.toml` | The manifest describing one hyperbook. |
| cartridge | Informal metaphor for a portable hyperbook package. |
| host | The environment where `bookit` runs: Solveit, local PC, VPS, server, container, etc. |
| surface | A way to experience the hyperbook: CLI, web UI, Solveit dialog, notebook, PWA, terminal, dashboard, game view, etc. |

`bookit` does **not** run “bookits.”  
`bookit` runs **hyperbooks**.

## 3. Design Goal

The endgame UX should feel like this:

```bash
bookit load https://github.com/1iis/bookofcomputers
bookit run bookofcomputers
```

Or, from inside a hyperbook repo:

```bash
bookit run .
```

The exact commands may change. The shape should not.

The reader should not need to understand the whole framework before beginning. The first experience should be:

1. get `bookit`;
2. load a hyperbook;
3. run it;
4. see something real;
5. inspect deeper when ready.

## 4. Guiding Principles

### 4.1 Hyperbooks are portable

A hyperbook should be movable.

At minimum, it should work as a folder or Git repository. Later, it may also work as a single-file bundle such as a `.tar`, `.hbook`, or similar package.

The packaging format is secondary. The core invariant is:

> A hyperbook is a coherent portable object.

### 4.2 The runtime owns host integration

A hyperbook should not need to reinvent installation logic for every machine.

`bookit` is responsible for knowing how to run inside:

- Solveit;
- a local Python environment;
- a VPS;
- a container;
- a web server;
- a future dedicated app;
- other compatible hosts.

The hyperbook describes what it needs.  
The host/runtime decides how to provide it.

### 4.3 Declarative first, code when earned

The default hyperbook interface should be declarative.

A hyperbook should begin with a manifest:

```text
hyperbook.toml
```

That manifest should describe title, version, entry point, required `bookit` version, assets, capabilities, dependencies, and surfaces.

Arbitrary install scripts should not be the default interface. They are powerful, but too opaque and too risky as the first pattern.

Preferred order:

1. manifest;
2. simple static content/assets;
3. optional declared dependencies;
4. optional setup hooks;
5. optional executable glue code;
6. explicit capabilities/permissions when code touches the host.

### 4.4 Content and state are separate

The hyperbook source should remain distinct from reader state.

A reader may complete exercises, write notes, generate files, change settings, receive AI feedback, unlock sections, or customize characters. Those changes should not corrupt the original hyperbook.

A useful split:

| Layer | Example |
|---|---|
| Hyperbook source | lessons, media, exercises, manifests, included code |
| Reader state | progress, answers, notes, generated files, preferences |
| Host state | installed packages, secrets, services, ports, caches |

This makes hyperbooks easier to update, fork, reset, share, and archive.

### 4.5 The book is inspectable

A hyperbook should not be a sealed blob.

Readers should be able to inspect:

- the manifest;
- the content;
- the assets;
- the exercises;
- the tests;
- the code;
- the generated state;
- the runtime behavior where practical.

This is especially important for educational hyperbooks. A course that teaches agency should itself be inspectable.

### 4.6 The book may be nonlinear

A hyperbook is not just a sequence of pages.

It may contain:

- chapters;
- sections;
- scenes;
- tracks;
- quests;
- exercises;
- generated explanations;
- alternate difficulty paths;
- dashboards;
- simulations;
- state-dependent routes;
- optional rabbit holes;
- remixable media;
- programmable transformations.

A hyperbook can behave like a book, but it can also behave like software.

### 4.7 The book may contain glue

A hyperbook may include small programs that make otherwise tedious operations trivial.

Examples:

- rename a character across a fiction series;
- reorder an album or documentary experience;
- add AI explanations to every paragraph;
- generate beginner/intermediate/expert views of a section;
- turn a command into a button;
- turn a lesson into a test;
- turn a reader’s answers into a dashboard;
- transform a static chapter into an interactive scene.

This should not be overspecified too early, but the architecture must preserve it.

The long-term idea is:

> Content is software.  
> A hyperbook is programmable media.

## 5. Hyperbook Shape

A minimal hyperbook should be understandable as a directory.

Illustrative layout:

```text
my-hyperbook/
├── hyperbook.toml
├── README.md
├── content/
├── assets/
├── exercises/
├── programs/
└── bookit/
```

Only `hyperbook.toml` and an entry point should be required at first.

A richer hyperbook may include:

| Path | Purpose |
|---|---|
| `hyperbook.toml` | Manifest. |
| `README.md` | Human orientation. |
| `content/` | Chapters, sections, scenes, pages, notebooks, markdown, etc. |
| `assets/` | Images, audio, video, datasets, fonts, static files. |
| `exercises/` | Checks, tasks, tests, answer schemas, grading hooks. |
| `programs/` | Optional code used by the hyperbook. |
| `surfaces/` | Optional UI definitions or surface-specific adapters. |
| `bookit/` | Optional local configuration or extension points. |

This layout is illustrative, not frozen.

## 6. Manifest

The manifest is the contract between the hyperbook and `bookit`.

A tiny first manifest might describe:

| Field | Purpose |
|---|---|
| `id` | Stable machine name. |
| `title` | Human title. |
| `version` | Hyperbook version. |
| `entry` | Default entry point. |
| `requires.bookit` | Compatible `bookit` version. |
| `authors` | Authors/contributors. |
| `license` | License information. |
| `surfaces` | Supported surfaces. |
| `capabilities` | Host abilities requested by the hyperbook. |
| `dependencies` | Python/system/runtime dependencies, if any. |

A future manifest may support much more, but the first useful version should stay small.

Design rule:

> If a thing can be declared safely, declare it.  
> If it must run code, make that code visible and intentional.

## 7. Lifecycle

A hyperbook has a simple lifecycle.

### 7.1 Discover

Find a hyperbook in a repo, folder, URL, registry, local library, or bundle.

### 7.2 Inspect

Read the manifest and show what the hyperbook is, what it needs, and what it wants to do.

Inspection should be available before execution.

### 7.3 Load

Copy, clone, download, mount, or register the hyperbook with the local `bookit` environment.

Loading should not automatically mean trusting arbitrary code.

### 7.4 Prepare

Install or validate declared dependencies where needed.

Preparation should support dry-run behavior where possible:

> “Here is what would happen.”

### 7.5 Run

Open the hyperbook through the best available surface.

Examples:

- a Solveit dialog;
- a local web app;
- a CLI;
- a notebook;
- a PWA;
- a dashboard;
- a game-like view.

### 7.6 Save State

Reader progress and generated artifacts are stored separately from the source.

### 7.7 Update

The source hyperbook may be updated while preserving reader state where possible.

### 7.8 Fork

A reader or author may copy the hyperbook and modify it.

Forking is a first-class behavior, not an accident.

### 7.9 Eject

Remove or detach the hyperbook cleanly from the host without destroying unrelated state.

The cartridge metaphor is useful here:

> Loading should be easy.  
> Ejecting should be clean.

## 8. Safety Model

`bookit` should not normalize blind execution.

A hyperbook may be passive or active.

| Kind | Behavior |
|---|---|
| Passive | Text, images, audio, video, static assets. |
| Interactive | UI, navigation, local state, exercises. |
| Executable | Scripts, services, tests, model calls, shell commands, host operations. |

Executable behavior should be explicit.

Important rules:

- The manifest should declare requested capabilities.
- Host secrets belong to the host, not the hyperbook.
- Setup hooks should be visible and inspectable.
- Dangerous actions should have dry-run or confirmation paths.
- The host should be able to say no.
- Beginner UX should not rely on “curl pipe shell” as the core trust model.

This does not mean hyperbooks cannot be powerful. It means power should be legible.

## 9. Surfaces

A hyperbook should not be tied to one frontend.

Possible surfaces:

| Surface | Use |
|---|---|
| CLI | Commands, checks, simple reading, automation. |
| Web | Public reading, rich media, dashboards, interaction. |
| Solveit | Literate computing, code execution, AI-assisted learning. |
| Notebook | Source-like authoring and technical lessons. |
| PWA | App-like local or hosted experience. |
| Terminal | Systems lessons, shell tasks, deployment work. |
| Game view | Long-term interactive/game-like experiences. |

`bookit` should allow a hyperbook to declare supported surfaces without forcing every hyperbook to support every surface.

A plain text hyperbook should be valid.  
A rich interactive game-like hyperbook should also be valid.

## 10. Book of Computers Example

The 1iis Book of Computers is the first major expected `bookit` dogfood project.

As a hyperbook, it may include:

- chapters and sections;
- Solveit dialogs;
- markdown explanations;
- code examples;
- exercises;
- tests;
- local scripts;
- dashboards;
- API calls;
- AI lessons;
- generated reader state;
- optional website views;
- optional local/server paths.

The Book of Computers should demonstrate the core promise:

> A reader learns computers by using a real software book that is itself made of inspectable computer parts.

But `bookit` must not be specific to the Book of Computers.

The same runtime should support a rich music album, an interactive fiction work, a practical legal guide, a philosophy reader, a game-like course, or a technical manual.

## 11. Content as Software

A traditional book is mostly fixed.

A hyperbook may be transformed.

This is one of the core long-arc ideas behind the project.

Because a hyperbook is software, it can support operations that would be impossibly tedious in ordinary media:

- generate alternate explanations;
- translate or adapt reading level;
- reorganize sections;
- add commentary layers;
- switch between linear and exploratory paths;
- attach exercises to paragraphs;
- generate quizzes from chapters;
- visualize dependencies;
- personalize examples;
- let readers mod the material;
- connect content to live systems.

This continues a historical arc:

```text
books → hypertext → multimedia → web apps → notebooks → games → AI-assisted programmable media → hyperbooks
```

The first implementation does not need to realize the whole arc.

It only needs to avoid blocking it.

## 12. Packaging

The first packaging format should be boring:

- folder;
- Git repository;
- downloadable archive.

Later, a one-file bundle may be useful.

Possible future names:

```text
.hbook
.hyperbook
.bookit
```

A bundle would likely be a structured archive containing:

- manifest;
- content;
- assets;
- optional code;
- integrity metadata;
- maybe precomputed indexes.

But this should come after the folder/repo model works.

Design rule:

> A bundle is a convenience format, not the essence of a hyperbook.

## 13. Minimal Viable `bookit`

The first useful `bookit` should probably do very little.

Minimum useful behavior:

| Feature | Purpose |
|---|---|
| Read `hyperbook.toml` | Establish the contract. |
| Validate a hyperbook folder | Check that it is loadable. |
| Load from local path | Start with simple development UX. |
| Load from Git URL | Support public hyperbooks. |
| Run default entry | Open or serve the first experience. |
| Keep reader state separately | Avoid corrupting source. |
| Inspect manifest/deps | Make behavior legible. |
| Basic `doctor` command | Help users understand host readiness. |

Candidate command vocabulary:

```text
bookit doctor
bookit inspect <hyperbook>
bookit load <source>
bookit run <id-or-path>
bookit eject <id>
```

This is enough to prove the console/cartridge UX without building a platform.

## 14. Non-Goals

At first, `bookit` is not:

- a full publishing marketplace;
- a DRM system;
- a replacement for Git;
- a replacement for Python packaging;
- a universal app store;
- a mandatory frontend;
- a giant LMS;
- a game engine;
- a notebook system;
- a cloud platform.

It may later connect to some of those categories.

The first job is simpler:

> Load and run software-backed books cleanly.

## 15. Strategic Invariants

These should remain true even as implementation changes.

1. `bookit` runs hyperbooks.
2. A hyperbook is portable.
3. A hyperbook is inspectable.
4. A hyperbook may be passive, interactive, or executable.
5. Reader state is separate from source.
6. The manifest is the first contract.
7. Host integration belongs to `bookit`.
8. Arbitrary code is powerful but should be explicit.
9. Hyperbooks are not limited to linear reading.
10. The first implementation should be small enough to actually ship.

## 16. Slogans

Useful phrases for keeping the design honest:

> Bookit runs hyperbooks.

> A hyperbook is software you can read.

> A hyperbook is media you can inspect.

> A hyperbook is a book-shaped program.

> Load the book. Run the book. Inspect the book. Fork the book.

> The reader receives the meal; over time, the kitchen becomes visible.

> The bundle is not the book. The folder is not the book. The repo is not the book. The hyperbook is the coherent object they contain.

## 17. North Star

The long-term dream is a computing-native medium that recovers some of the satisfying clarity of physical media while gaining the full power of software.

A hyperbook should feel ownable in the old sense:

- you can have it;
- open it;
- move it;
- lend it;
- copy it;
- inspect it;
- annotate it;
- mod it;
- preserve it.

And it should feel alive in the new sense:

- it can run;
- respond;
- explain;
- test;
- adapt;
- connect;
- generate;
- simulate;
- become an interface.

`bookit` exists to make that medium practical.

Its first proof should be small, concrete, and boringly useful.

Its horizon is broad:

> a console for programmable media.