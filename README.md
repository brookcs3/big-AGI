# big-AGI

Big-AGI is an experimental framework for building AI-powered workflows.
It uses **Next.js** and **tRPC** to provide a fast, interactive UI that integrates
with many language and multimodal models.

The project started as a personal playground and grew into a modular toolkit
used to explore multi-model reasoning, voice interaction and drawing tools.

## Features

- Chat interface with persona support
- "Beam" multi-model reasoning to merge responses from different LLMs
- Voice input, OCR and drawing canvas modules
- Easy local or cloud deployment via Docker or Vercel

## Getting Started

```bash
npm install
cp .env.sample .env.local # edit with your API keys
npm run dev
```

Open `http://localhost:3000` to start chatting. Environment variables are
documented in [docs/environment-variables.md](docs/environment-variables.md).

For a production build:

```bash
npm run build
npm start
```

## Repository Layout

- `src/apps` – top level app containers (chat, beam, draw, ...)
- `src/modules` – feature modules and API integrations
- `pages/` – Next.js route entrypoints
- `docs/` – guides and configuration notes

## Architecture

See `docs/architecture.md` for a high level overview.

## Highlight: Beam Fusion Engine

One of the most interesting pieces is the _Beam_ system. In
[`beam.gather.execution.tsx`](src/modules/beam/gather/instructions/beam.gather.execution.tsx)
the app orchestrates a sequence of instructions that merge outputs from multiple
models. Each step updates the UI reactively while the fusion runs:

```ts
export function gatherStartFusion(initialFusion, chatMessages, rayMessages, onUpdateBFusion) {
  // abort previous runs and validate inputs...
  const chainedInitialValue = '';
  let promiseChain: Promise<string> = Promise.resolve(chainedInitialValue);
  for (const instruction of instructions) {
    promiseChain = promiseChain.then(value => {
      return executeInstruction(instruction, value);
    });
  }
  promiseChain
    .then(() => onUpdateBFusion({ stage: 'success' }))
    .catch(err => onUpdateBFusion({ stage: 'error', errorText: err.message }));
}
```

This asynchronous chain drives the fusion of multiple model responses into a
single answer presented to the user.

---
2023–2025 · Enrico Ros / Big‑AGI
