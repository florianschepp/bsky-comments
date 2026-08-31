

# bsky-comments

https://github.com/user-attachments/assets/4e649daa-670e-4f66-8397-04ef731c9cba

A **zero-dependency Web Component** to embed Bluesky discussion threads on any website.

- **Lightweight:** ~3 kB gzipped. No heavy SDKs.
- **Universal:** Works with any framework — React, Vue, Svelte, Astro, or just plain HTML.
- **Styleable:** Renders Light DOM (not Shadow DOM) for easy styling via CSS/Tailwind.

## Why bsky-comments?

Most Bluesky embed libraries fall into two categories: **React Wrappers** (which bundle the heavy official SDK) or **Shadow DOM Widgets** (which are hard to style).

**bsky-comments is different:**

| Feature | bsky-comments | Typical React Library | Standard Web Component |
| :------ | :------------ | :-------------------- | :--------------------- |
| **Input** | **Public URL** OR **AT-URI** | **AT-URI** Only | **AT-URI** Only |
| **Styling** | **Light DOM** (Use Tailwind/CSS) | CSS Modules / Props | **Shadow DOM** (Locked) |
| **Engine** | **Native Fetch** (~3kb) | **@atproto/api** (~60kb) | **Lit / Stencil** (~15kb) |
| **Frameworks** | **All** (Universal) | React Only | All |

### Key Differentiators

1. **Dual Input Modes:**
   - **Easy Mode:** Just paste the public `https://bsky.app/...` link. We handle the handle resolution automatically.
   - **Direct Mode:** Pass the `at://did:plc...` URI to skip resolution for maximum performance (great for static builds).

2. **Headless / Light DOM:**
   We do not use Shadow DOM. This means your global CSS, **Tailwind classes**, and font settings apply immediately to the comments. No fighting against style encapsulation.

3. **Zero Dependencies:**
   We don't bundle the official AT Protocol SDK. We use lightweight, native HTTP requests to fetch only the data needed to render the thread.

## Installation

### npm / pnpm / yarn

```bash
npm install bsky-comments
```

### CDN (No Build Step)

Drop this into any HTML page — no bundler required:

```html
<script type="module" src="https://unpkg.com/bsky-comments"></script>

<bsky-comments post="https://bsky.app/profile/me.bsky.social/post/3lwt25ajsic2k"></bsky-comments>
```

## Usage

### Option 1: The Easy Way (Web Link)

Just copy the URL from your browser address bar. The component handles the resolution automatically.

```html
<bsky-comments
  post="https://bsky.app/profile/me.bsky.social/post/3lwt25ajsic2k"
></bsky-comments>
```

### Option 2: The Direct Way (AT-URI)

If you are generating your site programmatically (e.g. Astro/Next.js) and already know the DID, use the URI to skip the resolution step for maximum performance.

```html
<bsky-comments
  uri="at://did:plc:vb7cn66.../app.bsky.feed.post/3lwt25ajsic2k"
></bsky-comments>
```

## Customizing Icons

You can customize the Like and Reply icons in two ways:

### 1. Via Attributes (SVG or Emoji)

You can pass raw strings (or even SVG code) directly into the attributes.

```html
<bsky-comments
  post="..."
  icon-like="💙"
  icon-reply="↩️"
></bsky-comments>

<!-- Using SVGs -->
<bsky-comments
  post="..."
  icon-like='<svg viewBox="0 0 24 24" width="16" height="16" fill="currentColor"><path d="..."/></svg>'
></bsky-comments>
```

### 2. Via CSS (Recommended)

Since we wrap icons in specific classes, you can hide the default icon and use CSS/Tailwind to add your own via background-image or pseudo-elements.

```css
/* Hide default text/emoji */
.bsky-icon-like {
  display: inline-block;
  width: 16px;
  height: 16px;
  color: transparent; /* Hide the emoji */
  background-image: url('/heart-icon.svg');
  background-size: contain;
  background-repeat: no-repeat;
}
```

## API Reference

| Attribute | Type | Default | Description |
| :-------- | :--- | :------ | :---------- |
| `post` | string | `null` | The public web URL (e.g., `https://bsky.app/...`). The component will automatically resolve the handle to a DID. |
| `uri` | string | `null` | The internal AT-URI (e.g., `at://did:plc:...`). If provided, this takes precedence over `post`. |
| `sort` | string | `asc` | Sort order for comments: `asc` (oldest first) or `desc` (newest first). |
| `service` | string | `public.api.bsky.app` | The PDS endpoint. Use this for self-hosted instances. |
| `icon-like` | string | `❤️` | Custom HTML/Text for the Like icon. |
| `icon-reply` | string | `💬` | Custom HTML/Text for the Reply icon. |
| `depth` | string | `10` | Maximum reply depth to fetch (minimum `1`). |

## Styling Reference

This component renders **Semantic HTML** in the Light DOM. You can style it using standard CSS or Tailwind.

### HTML Structure

```html
<div class="bsky-container">
  <div class="bsky-header">
    <span>Discussion found on <a>Bluesky</a></span>
    <a class="bsky-reply-link">Reply to join discussion</a>
  </div>
  <div class="bsky-comment">
    <div class="bsky-comment-header">
      <img class="bsky-avatar" src="..." />
      <div class="bsky-meta">
        <a class="bsky-author">Display Name</a>
        <span class="bsky-handle">@handle.bsky.social</span>
        <a class="bsky-date">· Jan 5, 2026</a>
      </div>
    </div>
    <div class="bsky-body">
      <p>Comment text...</p>
    </div>
    <div class="bsky-actions">
      <span class="bsky-like">
        <span class="bsky-icon bsky-icon-like">❤️</span>
        <span class="bsky-count">12</span>
      </span>
      <span class="bsky-reply">
        <span class="bsky-icon bsky-icon-reply">💬</span>
        <span class="bsky-count">2</span>
      </span>
    </div>

    <!-- Nested Replies -->
    <div class="bsky-replies">
      <div class="bsky-comment">...</div>
    </div>
  </div>
</div>
```

### Tailwind Example

```html
<div class="
  [&_.bsky-comment]:border-l-2 [&_.bsky-comment]:border-gray-200 [&_.bsky-comment]:pl-4 [&_.bsky-comment]:mb-4
  [&_.bsky-actions]:text-sm [&_.bsky-actions]:text-gray-500 [&_.bsky-actions]:flex [&_.bsky-actions]:gap-4
">
  <bsky-comments post="..." />
</div>
```

## Framework Integration

### React / Next.js

```tsx
import 'bsky-comments';

export function BlogPost() {
  return (
    <div className="comments-section">
      <bsky-comments post="https://bsky.app/profile/..." />
    </div>
  );
}
```

### Vue / Nuxt

```vue
<script setup>
import 'bsky-comments';
</script>

<template>
  <bsky-comments :post="currentUrl" />
</template>
```

### Svelte / SvelteKit

```svelte
<script>
  import 'bsky-comments';
</script>

<bsky-comments post="https://bsky.app/profile/..." />
```

### Astro

```astro
---
import 'bsky-comments';
---

<bsky-comments post="https://bsky.app/profile/..." />
```

Or use the CDN approach without an import:

```astro
---
---

<bsky-comments post="https://bsky.app/profile/..." />

<script>
  import 'bsky-comments';
</script>
```

## License

MIT
