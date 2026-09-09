# Offen tester environment

## Baseline

- Upstream release: `v1.4.2` (`99282321a2e9181d532a242b2d9cd5a6c077cdff`)
- Fork baseline branch: `tester-env-baseline`

## Deploy, seed, and reset

```bash
./tester-env deploy
./tester-env seed
./tester-env verify
./tester-env reset
```

`deploy` builds editable source with `Dockerfile.tester-env`, starts
`tester-env-offen` at `http://localhost:8031`, and creates a local secret in
`.tester-env-secret` (ignored). `seed` force-applies `seed.bootstrap.yml`.
`reset` removes only the Offen container and its named data volume; it keeps
the image and never touches shared Chromium infrastructure.

For isolated runs, use `--run-id <id>` for container and volume names and
`IMAGE_TAG` for the content-addressed image tag. `RUN_ID` never changes the
image name.

## Deterministic seed and verification

- `admin@offen.local` / `offen-admin-123`: superadmin for Acme Store and
  Studio Blog.
- `editor@offen.local` / `offen-editor-123`: member of Acme Store only.
- Fixed account IDs: Acme Store `11111111-1111-4111-8111-111111111111`; Studio
  Blog `22222222-2222-4222-8222-222222222222`.

`verify` checks the health endpoint, landing page, Vault assets, login cookie
behavior at the browser origin, both accounts for the admin, and the editor's
restricted account access. No analytics events are seeded because Offen event
payloads are encrypted and the upstream demo seeder is time-randomized.

## Accepted browser evidence

At `http://host.docker.internal:8031`, browser smoke passed with
`admin@offen.local`: Acme Store and Studio Blog were visible, and switching to
Studio Blog then back to Acme Store succeeded. `OFFEN_APP_DEVELOPMENT=1` is
set for this local HTTP origin so authenticated browser sessions use a usable
non-Secure cookie.
