# Adding a New Collection

## Add Content

Add a new folder in `src/content`:

```
# add a new collection called animals

📁 /src/content/animals
└── 📁 lion
      └── 📄 index.md
└── 📁 zebra
      └── 📄 index.md
```

## Add Templates

Add a template in `pages` So your content can be rendered

```
# add a new page called animals

📁 /src/pages/animals
└── 📁 lion
      └── 📄 index.md
```

## Define collection

In `src/content/config.ts` define a collection

```ts
const animals = defineCollection({
  type: "content",
  schema: z.object({
    title: z.string(),
    description: z.string(),
    date: z.coerce.date(),
    draft: z.boolean().optional(),
    demoURL: z.string().optional(),
    repoURL: z.string().optional()
  }),
});
```

## Use collection
Now you can use the collection

```ts
const field_notes = (await getCollection("animals"))
  .filter(post => !post.data.draft)
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf())
  .slice(0,SITE.NUM_POSTS_ON_HOMEPAGE);
```
