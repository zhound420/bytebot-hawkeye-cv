# Repository Guidelines

## Project Structure & Module Organization
Bytebot Hawkeye is an npm workspace monorepo. Key packages live under `packages/`:
- `shared` exposes cross-package types and helpers; build it first to populate `dist/`.
- `bytebot-cv` wraps OpenCV bindings and platform patches (see `scripts/` for GPU setup helpers).
- `bytebot-agent` is the NestJS service orchestrating CV, LLM, and Prisma state.
- `bytebot-ui` hosts the Next.js desktop control surface served by `server.ts`.
Supporting directories include `scripts/` for stack automation, `docker/` for container configs, and `tests/` for integration and vision regression assets.

## Build, Test, and Development Commands
Install dependencies once with `npm install`. Rebuild packages in dependency order using `npm run build --workspace=@bytebot/shared`, `npm run build --workspace=@bytebot/cv`, `npm run build --workspace=bytebot-agent`, and `npm run build --workspace=bytebot-ui`. For an end-to-end refresh use `./scripts/fresh-build.sh`. Start the full Docker stack with `./scripts/start-stack.sh` and stop via `./scripts/stop-stack.sh`.

## Coding Style & Naming Conventions
Code is TypeScript-first with Prettier (two-space indentation, single quotes) and ESLint enforcing the rule sets defined in each package. Use camelCase for functions/variables, PascalCase for NestJS providers and React components, and kebab-case for filenames (e.g., `packages/bytebot-agent/src/app.service.ts`). Run `npm run lint --workspace=<package>` and, when available, `npm run format --workspace=<package>` before sending changes.

## Testing Guidelines
`bytebot-agent` uses Jest—run `npm test --workspace=bytebot-agent` and ensure `npm run test:cov --workspace=bytebot-agent` stays green. UI component tests run with `npm test --workspace=bytebot-ui`. GPU-sensitive regression checks live in `tests/integration/`; execute targeted scripts such as `node tests/integration/test-cv-improvements.js` after modifying CV logic. Attach logs or screenshots for failures affecting OmniParser or computer vision outputs.

## Commit & Pull Request Guidelines
Follow the existing Conventional Commit pattern (`feat:`, `fix:`, `chore:`). Scope commits tightly so reviewers can trace CV and agent changes independently. Pull requests should include a concise summary, linked issue or ticket, the commands/tests you ran, and any imagery that demonstrates UI or vision updates. Call out platform implications (e.g., CUDA vs CPU) and configuration changes to `docker/.env` or `scripts/` so deployers can reproduce your results.

## Security & Configuration Tips
Never commit API keys; populate `docker/.env` locally per the README. Run `./scripts/setup-omniparser.sh` after cloning or when swapping machines to ensure GPU detection and model downloads stay in sync.
