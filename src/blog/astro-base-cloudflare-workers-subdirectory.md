---
title: "Making Astro base Work with Cloudflare Workers Static Assets"
pubDate: 2026-07-21
description: "Learn why Astro's base option isn't enough when deploying a static site to a subdirectory on Cloudflare Workers Static Assets, and how to fix it."
author: "Andrés Gracia Danies"
image:
  url: "/blog/astro-base-cloudflare-workers-static-assets.png"
  alt: "Astro and Cloudflare Workers"
tags: ["astro", "cloudflare", "cloudflare workers"]
---

If you've ever tried deploying an Astro website to a subdirectory using the `base` configuration, you've probably found that everything works perfectly during local development... until you deploy it to Cloudflare Workers.

I recently ran into this exact problem while deploying **Rischio Academy**, a documentation site built with Astro Starlight. The goal was simple: serve the application from `/academy` instead of the root of the domain.

Locally, everything worked exactly as expected.

```ts
export default defineConfig({
  base: "/academy",
});
```

Running `astro preview` produced the expected behavior:

```
http://localhost:4321/academy/
```

Every asset and every internal link was correctly prefixed with `/academy`.

## The problem

After deploying to **Cloudflare Workers Static Assets**, the generated `workers.dev` URL behaved differently.

Visiting the root returned the site (although completely broken because every asset pointed to `/academy`).

```
https://your-project.workers.dev/
```

Trying to access the correct URL resulted in a 404.

```
https://your-project.workers.dev/academy/
```

At first, this looked like a routing issue.

However, the generated HTML was completely correct.

```html
<link href="/academy/_astro/common.css" />
<a href="/academy/es/soil-laboratory/"></a>
```

Astro was doing exactly what it should.

## Why it happens

The important thing to understand is that **Astro's `base` option only rewrites URLs**.

It does **not** change the output directory structure.

After running the build, the generated files looked like this:

```
dist/
├── es/
├── _astro/
├── favicon.ico
└── ...
```

Cloudflare Workers Static Assets, however, resolves requests directly against the physical files inside the assets directory.

When requesting:

```
/academy/es/
```

Cloudflare looks for something equivalent to:

```
dist/academy/es/index.html
```

But Astro generated:

```
dist/es/index.html
```

The file simply doesn't exist where Cloudflare expects it.

## The solution

Instead of changing Astro, I changed the output structure after the build.

The final directory became:

```
dist/
└── academy/
    ├── es/
    ├── _astro/
    ├── favicon.ico
    └── ...
```

Once the files were physically inside the `academy` folder, everything worked immediately.

The deployment command became:

```bash
npm run build && \
mkdir -p dist/academy && \
find dist -mindepth 1 -maxdepth 1 ! -name academy -exec mv {} dist/academy/ \; && \
wrangler deploy
```

A cleaner alternative is creating a small Node.js script executed as a `postbuild` step.

## The result

After moving the generated files into the subdirectory:

- `https://your-project.workers.dev/academy/` worked correctly.
- All assets loaded correctly.
- Internal navigation worked.
- Astro's `base` option behaved exactly as expected.

## Conclusion

There was nothing wrong with Astro.

The confusing part is that `astro preview` serves your project using the configured `base`, while **Cloudflare Workers Static Assets expects the physical directory structure to match the requested URL**.

If you're deploying an Astro static site under a subdirectory such as `/academy`, `/docs`, or `/blog` on Cloudflare Workers Static Assets, remember that configuring `base` alone is not enough.

You also need your deployment output to mirror that directory structure.
