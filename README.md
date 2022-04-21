# astro-backlinks

Reproduction of error getting the astro object after await import.meta.glob('../pages/seeds/*.md')


### Problem:

after upgrading from v21 to v1.0.0-x In a project where I am using backlinks for a Digital Garden

Previously I could do
// import all the md pages with meta.glob to prevent infinity loop if I import from the same page
```js
const allSeeds = await import.meta.glob('../pages/garden/seeds/*.md')
let seeds = await Promise.all(
    Object.entries(allSeeds).map(([key, seed]) => {
        const url = key.replace('../pages/garden/seeds/', '/garden/seeds').replace('.md', '/')
        return seed().then(content => ({
            ...content.frontmatter,
            url
        }))
    })
)

seeds = seeds.filter(seed => {
    // compare the contant to see the pages my current seed is mentioned in
    return seed.astro.source.toLowerCase().includes(content.title.toLowerCase())
});
```

After the upgrade astro.source seems no longer available in the frontmatter how can I get the content now?
Thanks in advance.

### Solution:

The object received had an async function called `default` that returns the object that was provided in the previous version.
```js
{
  /** Frontmatter from a blog post
  "title": "Astro 0.18 Release",
  "date": "Tuesday, July 27 2021",
  "author": "Matthew Phillips",
  "description": "Astro 0.18 is our biggest release since Astro launch.",
  "draft": false,
  "keywords": ["astro", "release", "announcement"]
  **/
  "astro": {
    "headers": [
      {
        "depth": 1,
        "text": "Astro 0.18 Release",
        "slug": "astro-018-release"
      },
      {
        "depth": 2,
        "text": "Responsive partial hydration",
        "slug": "responsive-partial-hydration"
      }
      /* ... */
    ],
    "source": "# Astro 0.18 Release\nA little over a month ago, the first public beta [...]"
  },
  "url": ""
}
```

[Edit on StackBlitz ⚡️](https://stackblitz.com/edit/astro-backlinks)
