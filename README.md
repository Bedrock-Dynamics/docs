# Bedrock Dynamics Docs

Developer documentation for [Bedrock Dynamics](https://bedrockdynamics.studio) products, built with [Mintlify](https://mintlify.com).

**Live site**: [bedrockdynamics.studio/docs](https://bedrockdynamics.studio/docs)

## Structure

```
docs/
├── index.mdx                    # Docs home
├── substrate/
│   ├── introduction.mdx         # What is Substrate
│   ├── installation.mdx         # Download & install
│   ├── quickstart.mdx           # First simulation in 5 min
│   ├── features/                # Feature deep-dives
│   ├── guides/                  # Task-oriented tutorials
│   └── reference/               # Shortcuts, platforms, architecture
└── docs.json                    # Mintlify config
```

## Local development

```bash
npm i -g mint
mint dev
```

Preview at `http://localhost:3000`.

## Deployment

Pushes to `main` auto-deploy via the Mintlify GitHub app. The site is served at `bedrockdynamics.studio/docs` through a Vercel rewrite.
