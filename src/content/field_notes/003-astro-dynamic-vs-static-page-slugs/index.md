# Two Approaches to Dynamic Routes in Astro

When building a site with Astro, there are two primary approaches for handling dynamic routes (like blog posts, product pages, etc.). This document outlines both methods with examples and explains when to use each approach.

## Approach 1: Using `getStaticPaths()`

This approach pre-generates all pages at build time. It's part of Astro's static site generation (SSG) capability.

### Example Code:

```astro
---
import { type CollectionEntry, getCollection } from 'astro:content';

type Props = CollectionEntry<'details'>;

// Entry is passed as props from getStaticPaths
const entry = Astro.props as Props;
const { Content } = await entry.render();

export async function getStaticPaths() {
  const details = await getCollection('details');
  return details.map((detail) => ({
    params: { slug: detail.slug },
    props: detail,
  }));
}
---

<div>
  <h1>{entry.data.title}</h1>
  <Content />
</div>
```

### Advantages:

- **Performance**: Pages are pre-rendered at build time, resulting in faster page loads
- **SEO friendly**: All content is available immediately to search engines
- **Reduced server load**: No processing required at request time
- **Works with static hosting**: Can be deployed on any static hosting service

### Best for:

- Sites with content that doesn't change frequently
- Projects with a known, finite number of pages
- When maximum performance is required

## Approach 2: Dynamic Fetching from URL Parameters

This approach fetches content dynamically based on the URL parameters at request time. It's part of Astro's server-side rendering (SSR) capabilities.

### Example Code:

```astro
---
import { getCollection } from 'astro:content';

// Get the slug from URL parameters
const { slug } = Astro.params;

// Fetch the entry dynamically
const details = await getCollection('details');
const entry = details.find((detail) => detail.slug === slug);

// Handle missing entries
if (!entry) {
  return new Response('Not found', { status: 404 });
}

// Render content
const { Content } = await entry.render();
---

<div>
  <h1>{entry.data.title}</h1>
  <Content />
</div>
```

### Advantages:

- **Always up-to-date**: Content reflects the latest data on each request
- **No build-time limit**: Can handle unlimited dynamic routes
- **Better for dynamic content**: Ideal for content that changes frequently
- **Handles user-specific content**: Good for personalized pages

### Best for:

- Sites with frequently changing content
- Applications with user-generated content
- When you need to access request-specific data (cookies, headers, etc.)
- When the number of potential routes is very large or unknown

## Conclusion

Choose `getStaticPaths()` when you want pre-rendered, high-performance pages for content that doesn't change often. Choose dynamic fetching when you need flexibility, real-time data, or have too many routes to pre-render.

In both cases, ensure you handle errors appropriately, especially when entries might not exist.
