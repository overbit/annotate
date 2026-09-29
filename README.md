# reviewjs

![A Visual screenshot of Annotatejs](./og-image.png)
**A drop-in visual review & annotation layer for any website.** Highlight text,
draw rectangles & circles, drop pins, sketch freehand and leave threaded
comments — directly on top of your live page.

No backend. No database. No tracking. Comments live in the visitor's own
browser (`localStorage`) and can be **downloaded to / imported from a portable
JSON file** to share with your team.

```html
<script src="https://overbit.github.io/annotate/annotate.js" defer></script>
```

That single line is the whole installation.

---

## Why reviewjs?

- **One `<script>` tag.** No build step, no framework, no signup.
- **Works everywhere.** Plain HTML, React, Vue, Svelte, WordPress, Webflow,
  Shopify, static sites — anything that renders HTML in a browser.
- **Local-first & private.** Every comment is stored on the reviewer's device.
  Nothing is sent anywhere.
- **Portable.** Reviewers export their feedback as JSON and send it to you; you
  import it with one click and see every note in place.
- **Polished UI.** A floating toolbar, a Figma-style comments panel, light/dark
  themes that auto-adapt to your page, and full keyboard shortcuts.
- **Tiny & dependency-free.** ~40 KB of vanilla JavaScript, zero dependencies.

---

## Features

| Tool | What it does |
|------|--------------|
| ✏️ **Highlight** | Select any text to highlight and comment on it |
| ▭ **Rectangle** | Draw a box around any region |
| ◯ **Circle** | Circle anything that needs attention |
| 📍 **Pin** | Drop a point marker anywhere |
| 〰️ **Freehand** | Sketch directly on the page |
| ➕ **Section note** | Hover any paragraph/heading for a margin comment button |

Plus: threaded replies, resolve/reopen, search & filter, deep-links to a single
comment (`#an=<id>`), an "off" mode that collapses to a small launcher, and a
**Download / Copy / Import** round-trip for sharing.

---

## Quick start

### 1. The fastest way (CDN)

Add this just before `</body>`:

```html
<script src="https://overbit.github.io/annotate/annotate.js" defer></script>
```

That's it — reload the page and the toolbar appears in the bottom-right corner.

> **Pin a version** for production stability:
> `https://cdn.jsdelivr.net/npm/@reviewjs/annotate@1.2.0/annotate.js`
>
> unpkg works too:
> `https://unpkg.com/@reviewjs/annotate@1.2.0/annotate.js`

### 2. GitHub Pages build

The forked build is published directly from this repository:

```html
<script src="https://overbit.github.io/annotate/annotate.js" defer></script>
```

Live demo: `https://overbit.github.io/annotate/`

### 3. Self-hosted

Download [`annotate.js`](./annotate.js), drop it next to your HTML and:

```html
<script src="/annotate.js" defer></script>
```

---

## Configuration

Configure with `data-` attributes on the script tag — all optional:

```html
<script
  src="https://cdn.jsdelivr.net/npm/@reviewjs/annotate/annotate.js"
  data-project="marketing-site"
  data-accent="#6d28d9"
  data-theme="auto"
  data-position="bottom-right"
  data-start-open="true"
  data-note="Focus on the hero copy and pricing — flag anything off-brand."
  data-share-email="reviews@example.com"
  defer
></script>
```

| Attribute | Default | Description |
|-----------|---------|-------------|
| `data-project` | `""` | Namespace for stored comments. Keep separate sites apart. |
| `data-page` | `location.pathname` | Page key comments are grouped under. |
| `data-accent` | — | Brand color for primary buttons & the active tool. |
| `data-theme` | `auto` | `light`, `dark`, or `auto` (sniffs your page background). |
| `data-position` | `bottom-right` | `bottom-right` or `bottom-left`. |
| `data-blocks` | sensible default | CSS selector for "section note" (+) targets. |
| `data-start-open` | `false` | Set to `true` to show the review toolbar immediately instead of the collapsed Review pill. |
| `data-note` | — | Author's note to reviewers — what should be reviewed. Shown when they start and atop the comments panel. |
| `data-share-email` | — | Where reviewers send comments: an email address, or a Slack / Hangout link. Adds a **Share** button. |

Prefer JS config? Set `window.AnnotateConfig` **before** the script loads:

```html
<script>
  window.AnnotateConfig = {
    project: "marketing-site",
    accent: "#6d28d9",
    theme: "auto",
    note: "Focus on the hero copy and pricing — flag anything off-brand.",
    shareEmail: "reviews@example.com",
  };
</script>
<script src="https://overbit.github.io/annotate/annotate.js" defer></script>
```

---

## Framework integration

reviewjs is a plain browser script, so the goal everywhere is the same:
**load `annotate.js` once, after the page has rendered.** Below are copy-paste
recipes.

### ⚛️ React (and Next.js)

Load it once at the app root with a `useEffect`:

```jsx
// components/Annotate.jsx
import { useEffect } from "react";

export default function Annotate() {
  useEffect(() => {
    if (document.getElementById("annotate-js")) return;
    window.AnnotateConfig = { project: "my-react-app", accent: "#6d28d9" };
    const s = document.createElement("script");
    s.id = "annotate-js";
    s.src = "https://cdn.jsdelivr.net/npm/@reviewjs/annotate/annotate.js";
    s.defer = true;
    document.body.appendChild(s);
  }, []);
  return null;
}
```

```jsx
// App.jsx
import Annotate from "./components/Annotate";

export default function App() {
  return (
    <>
      <Annotate />
      {/* your app */}
    </>
  );
}
```

**Next.js (App Router)** — drop the `<Script>` into `app/layout.js`:

```jsx
import Script from "next/script";

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Script
          src="https://cdn.jsdelivr.net/npm/@reviewjs/annotate/annotate.js"
          strategy="afterInteractive"
        />
      </body>
    </html>
  );
}
```

### 🟩 Vue 3

```vue
<!-- App.vue -->
<script setup>
import { onMounted } from "vue";

onMounted(() => {
  if (document.getElementById("annotate-js")) return;
  window.AnnotateConfig = { project: "my-vue-app", accent: "#10b981" };
  const s = document.createElement("script");
  s.id = "annotate-js";
  s.src = "https://cdn.jsdelivr.net/npm/@reviewjs/annotate/annotate.js";
  s.defer = true;
  document.body.appendChild(s);
});
</script>
```

Or, even simpler, add the `<script>` tag straight into `public/index.html`
(Vue CLI) / `index.html` (Vite) before `</body>`.

### 🧩 WordPress

**Option A — no code.** Install a "header/footer scripts" plugin (e.g. *WPCode*
or *Insert Headers and Footers*) and paste this into the **footer** box:

```html
<script src="https://cdn.jsdelivr.net/npm/@reviewjs/annotate/annotate.js" data-project="my-wp-site" defer></script>
```

**Option B — theme code.** Add to your theme's `functions.php`:

```php
function reviewjs_enqueue() {
  wp_enqueue_script(
    'reviewjs',
    'https://cdn.jsdelivr.net/npm/@reviewjs/annotate/annotate.js',
    array(),
    '1.2.0',
    true // load in footer
  );
}
add_action( 'wp_enqueue_scripts', 'reviewjs_enqueue' );
```

> Tip: wrap the enqueue in `if ( current_user_can('edit_posts') )` to show the
> review tools only to logged-in editors.

### 🔷 Svelte / SvelteKit

```svelte
<!-- src/routes/+layout.svelte -->
<script>
  import { onMount } from "svelte";
  onMount(() => {
    const s = document.createElement("script");
    s.src = "https://cdn.jsdelivr.net/npm/@reviewjs/annotate/annotate.js";
    s.defer = true;
    document.body.appendChild(s);
  });
</script>

<slot />
```

### 🅰️ Angular

In `angular.json`, add to the `"scripts"` array:

```json
"scripts": [
  "https://cdn.jsdelivr.net/npm/@reviewjs/annotate/annotate.js"
]
```

### 🌐 Plain HTML / static sites / Webflow / Shopify / Squarespace

Paste before `</body>` (or into the platform's "custom code / footer" field):

```html
<script src="https://overbit.github.io/annotate/annotate.js" defer></script>
```

---

## Sharing comments

Because everything is local, sharing is an explicit, privacy-friendly action:

1. A reviewer opens the **Comments panel** (toolbar list icon or press `A`).
2. They click **Download** (⬇), enter the name to attach to the export, and save
   a `annotate-<domain-or-project>-<date>.json` file.
3. The download contains **all comments stored for the current domain/project**,
   including comments from other pages. Each comment keeps its own `page` and
   `url`, so a new domain-scoped import restores comments to their original pages.
4. They send you that file and you click **Import** (⬆) anywhere in the same
   review project/domain.

Domain-wide exports add `"scope": "domain"` as an additive metadata field.
Older page-scoped export files do not have this field and remain fully
supported: when imported, their comments are placed on the current page exactly
as in previous releases. Older Annotate releases can still read the new JSON
shape because the existing `kind`, `page`, and `comments` fields are unchanged;
they simply fall back to their legacy page-import behavior.

### Who owns a comment in an export

The name you enter at export is attached to **your own** comments only. A comment
written by somebody else keeps its original `author`, so one reviewer can never
rewrite another reviewer's attribution by downloading a file. Comments saved
before a name was set are stored as `Anonymous` and count as yours, since nobody
else claimed them.

Because a download spans the whole project, the confirmation toast tells you how
many comments kept their original author — worth reading before you forward a
file. Attribution is applied only to the downloaded copy; comments already stored
in the reviewer's browser are not rewritten.

### Importing into the right project

Page keys are namespaced by `data-project`, so a file only restores correctly into
the project it came from:

- Comments whose `page` belongs to a **different project** are skipped, and the
  toast reports how many. Storing them would leave comments that render on no
  page and that `clear()` cannot remove.
- A file exported from a **different origin** but the same project is imported
  normally, with a notice — page paths are matched against the current site.

**Copy** remains page-scoped for compatibility and copies the comments currently
loaded on the active page.

You can also drive this from code (see the API below).

---

## JavaScript API

A global `window.Annotate` is available once the script loads:

```js
Annotate.open();              // show the review layer and open the comments panel
Annotate.close();
Annotate.toggle();
Annotate.enable();            // show the review layer
Annotate.disable();           // collapse to the launcher
Annotate.setTool("highlight");// show the layer, then choose cursor | highlight | rect | circle | pen | pin
Annotate.comments();          // → array of comment objects for this page
Annotate.focus(id);           // scroll to & highlight a comment
Annotate.export();            // download all comments for this domain/project
Annotate.import();            // open the file picker
Annotate.clear();             // delete all comments on this page (local)
Annotate.toast("Saved!");     // show a toast
Annotate.version;             // "1.2.0"
```

### Comment shape

```json
{
  "id": "c…",
  "page": "marketing-site:/pricing",
  "url": "https://example.com/pricing",
  "type": "highlight",
  "author": "Jane Doe",
  "text": "This price looks out of date.",
  "color": "#f59e0b",
  "anchor": { "exact": "…", "prefix": "…", "suffix": "…" },
  "geom": null,
  "resolved": false,
  "replies": [],
  "createdAt": "2026-06-16T10:00:00.000Z",
  "updatedAt": "2026-06-16T10:00:00.000Z"
}
```

---

## Keyboard shortcuts

| Key | Action | Key | Action |
|-----|--------|-----|--------|
| `V` | Browse | `P` | Pin |
| `H` | Highlight | `A` | Comments panel |
| `R` | Rectangle | `O` | Show / hide tools |
| `C` | Circle | `Esc` | Cancel |
| `D` | Freehand | `?` | Shortcuts card |

---

## Try it locally

```bash
git clone git@github.com:reviewjs/annotate.git
cd annotate
npm start          # serves the demo at http://localhost:3000
```

Open [`index.html`](./index.html) and start annotating. Framework examples live
in [`examples/`](./examples).

---

## Browser support

Modern evergreen browsers (Chrome, Edge, Firefox, Safari). Uses standard DOM
APIs only — no polyfills required. Gracefully no-ops where `localStorage` is
unavailable (private mode, sandboxed iframes).

---

## License

[MIT](./LICENSE) — free for personal and commercial use.
