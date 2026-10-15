# Fixing Slow Next.js Setup in China: npm Registry Mirror

## Problem

Creating a new Next.js project with `npx create-next-app@latest` may take a long time in China because connections to the default npm registry (`https://registry.npmjs.org/`) can be slow or unreliable due to network routing or firewall restrictions.

For example, `npm ping` took around 79 seconds against the default registry, while the npmmirror registry responded in about 452 ms.

> Results vary by network. A fast ping does not guarantee every package or download host will be equally fast.

## Solution: Use npmmirror

### 1. Test the mirror

```bash
npm ping --registry=https://registry.npmmirror.com
```

If it responds quickly, continue.

### 2. Set it as the default npm registry

```bash
npm config set registry https://registry.npmmirror.com
```

Verify the setting:

```bash
npm config get registry
```

Expected output:

```text
https://registry.npmmirror.com/
```

### 3. Create a Next.js project

```bash
npx create-next-app@latest my-app --yes
```

If you prefer not to change the default registry, use the mirror for just this command:

```bash
npm_config_registry=https://registry.npmmirror.com npx create-next-app@latest my-app --yes
```

**Note:** If `my-app` already exists, check its contents before running the command again.

### 4. Start the app

```bash
cd my-app
npm run dev
```

Open http://localhost:3000 in your browser.

## If installation is still slow

Check whether the mirror is reachable:

```bash
curl -I --max-time 15 https://registry.npmmirror.com/
```

For more detail about npm requests:

```bash
npm ping --verbose
```

If needed, inspect proxy settings:

```bash
npm config get proxy
npm config get https-proxy
env | grep -i proxy
```

A local proxy or VPN may be required by your network. Do not remove proxy settings unless you know they are unnecessary.

Some packages download binaries or other assets from hosts outside the npm registry, so using a mirror may not fix every download issue.

## Switch back to the official registry

```bash
npm config set registry https://registry.npmjs.org/
```

## Reusing a Next.js starter with GitHub

After creating and testing a starter project, push it to a GitHub repository. For future projects, clone the repository and install dependencies:

```bash
git clone https://github.com/YOUR_USERNAME/nextjs-starter.git my-new-project
cd my-new-project
npm install
npm run dev
```

Replace `YOUR_USERNAME` with your GitHub username. Cloning downloads the source code, but dependencies generally need to be installed for each clone.

## Security and reliability notes

- Use a registry mirror you trust and check that it supports the package versions you need.
- A mirror is a workaround for registry connectivity, not a guarantee that all network restrictions are resolved.
- Review package install-script warnings rather than approving scripts blindly.
- Never commit secrets such as API keys or `.env.local` files to GitHub.
