# Eryxian Legacy TCG — Card Codex

A fast, data-driven single-page card browser for the Eryxian Legacy TCG. Runs entirely in the browser with no build step, no server, no dependencies beyond Font Awesome. Designed for GitHub Pages.

![Eryxian TCG](https://eryxian.com)

---

## Quick start

```
eryxian-codex/
├── index.html   ← the full app (HTML + CSS + JS, single file)
└── cards.json   ← all card data
```

1. Put both files in a GitHub repository.
2. Go to **Settings → Pages → Source: main branch / root**.
3. Your codex is live at `https://YOUR-USERNAME.github.io/REPO-NAME/`.

To test locally, open `index.html` in a browser. If `cards.json` cannot be fetched (file:// restriction), the app falls back to the embedded demo data automatically.

---

## cards.json structure

Everything the app displays is driven by `cards.json`. You never need to edit `index.html` to add new cards, factions, keywords, or editions.

### Top level

```json
{
  "meta":     { ... },
  "factions": { ... },
  "rarities": { ... },
  "cards":    [ ... ]
}
```

### meta

```json
"meta": {
  "version":     "1.0",
  "title":       "Eryxian Legacy TCG — Card Codex",
  "updated":     "2026-03-20",
  "description": "Full card database"
}
```

`title` sets the browser tab title and the header logo text.

### factions

Each key is the faction identifier used in card entries. Add as many as you need — new factions appear automatically in the sidebar filter.

```json
"factions": {
  "humans": {
    "label":  "Humans",
    "color":  "#4a90d9",
    "accent": "#3a7acc"
  },
  "eryxians": {
    "label":  "Eryxians",
    "color":  "#3aaa50",
    "accent": "#2d8a40"
  }
}
```

| Field    | Description                                      |
|----------|--------------------------------------------------|
| `label`  | Display name shown in filters and badges         |
| `color`  | Hex colour for badges, accent bars, filter pills |
| `accent` | Slightly darker shade used for hover states      |

### rarities

Controls rarity sort order and display. Add or rename freely.

```json
"rarities": {
  "common":    { "label": "Common",    "color": "#888888", "order": 1 },
  "uncommon":  { "label": "Uncommon",  "color": "#44aa88", "order": 2 },
  "rare":      { "label": "Rare",      "color": "#4a8fd9", "order": 3 },
  "mythic":    { "label": "Mythic",    "color": "#cc6020", "order": 4 },
  "legendary": { "label": "Legendary", "color": "#cc9020", "order": 5 }
}
```

The `order` field controls sort direction when sorting rarity common→legendary.

### cards

Each card is one object in the `cards` array.

```json
{
  "id":        "armored-commander",
  "name":      "Armored Commander",
  "faction":   "humans",
  "type":      "Character",
  "subtype":   "Officer",
  "rarity":    "rare",
  "cost":      4,
  "atk":       3,
  "hp":        5,
  "keywords":  ["Defender"],
  "effect":    "optional effect line shown in italic",
  "lore":      "Full lore paragraph...",
  "mainImage": "https://res.cloudinary.com/YOUR/image/upload/card.jpg",
  "frame":     "https://res.cloudinary.com/YOUR/image/upload/frame.png",
  "editions":  [
    { "name": "Eryxian Civilizations", "image": "url", "foil": false },
    { "name": "Eryxian Cargo",         "image": "url", "foil": false },
    { "name": "Eryxian Cargo Foil",    "image": "url", "foil": true  }
  ],
  "videos": [
    { "type": "youtube",    "id":  "VIDEO_ID",    "label": "Trailer"     },
    { "type": "cloudinary", "url": "video-url",   "label": "Design reel",
                            "poster": "poster-url" }
  ],
  "tags": ["infantry", "heavy", "frontline"]
}
```

#### Field reference

| Field       | Type            | Required | Notes                                                        |
|-------------|-----------------|----------|--------------------------------------------------------------|
| `id`        | string          | yes      | Unique slug, lowercase, hyphens. Used as anchor.             |
| `name`      | string          | yes      | Display name.                                                |
| `faction`   | string          | yes      | Must match a key in `factions`. Unknown factions still work. |
| `type`      | string          | yes      | `Character`, `Spell`, `Artifact`, `Secret`, `Hero`, etc.    |
| `subtype`   | string          | no       | Secondary type shown in smaller text.                        |
| `rarity`    | string          | no       | Must match a key in `rarities`. Defaults to `common`.        |
| `cost`      | number / null   | no       | Set to `null` for cards with no cost (Hero abilities).       |
| `atk`       | number / null   | no       | Set to `null` for spells, secrets, artifacts.                |
| `hp`        | number / null   | no       | Set to `null` for spells, secrets, artifacts.                |
| `keywords`  | string[]        | no       | Any strings — new keywords appear in the sidebar filter.     |
| `effect`    | string          | no       | Mechanic text. Shown in italic below keywords.               |
| `lore`      | string          | no       | Flavour/lore paragraph. Full text shown in card modal.       |
| `mainImage` | string (URL)    | no       | Main art. Cloudinary URLs are auto-upgraded for lightbox.    |
| `frame`     | string (URL)    | no       | PNG frame overlay with transparency. Per-card.               |
| `editions`  | Edition[]       | no       | See edition object below.                                    |
| `videos`    | Video[]         | no       | See video object below.                                      |
| `tags`      | string[]        | no       | Internal search tags. Not displayed but searched.            |

#### Edition object

```json
{ "name": "Eryxian Civilizations", "image": "https://...", "foil": false }
```

| Field   | Description                                              |
|---------|----------------------------------------------------------|
| `name`  | Edition or set name — populates the edition filter       |
| `image` | Art for this edition (can differ from mainImage)         |
| `foil`  | If true, shows a gold ✦ Foil badge and gold dot          |

Editions double as game set identifiers. Use full set names:
`"Eryxian Civilizations"`, `"Eryxian Cargo"`, `"Eryxian Promo"` etc.

#### Video object

```json
{ "type": "youtube",    "id": "dQw4w9WgXcQ", "label": "Trailer" }
{ "type": "cloudinary", "url": "https://res.cloudinary.com/.../video.mp4",
                        "poster": "https://...",  "label": "Design reel" }
```

For Cloudinary videos, if `poster` is omitted the app auto-generates it using `so_0` (frame 0 of the video).

---

## Filtering and search

| Filter            | Behaviour                                                   |
|-------------------|-------------------------------------------------------------|
| Search box        | Searches name, lore, effect, tags, keywords, faction, type  |
| Faction pills     | Multi-select OR — shows cards from any selected faction      |
| Card type         | Multi-select OR                                             |
| Keywords          | Multi-select AND — card must have ALL selected keywords      |
| Editions          | Multi-select OR — card must appear in at least one          |
| Rarity            | Multi-select OR                                             |
| Stat ranges       | Min/max for cost, ATK, and HP independently                 |

All filters combine with AND across categories. Keyboard shortcut: press `/` to focus the search box.

Active filters appear as removable pills below the header. Each filter group has its own clear button. **Reset all** clears everything.

---

## Sorting

| Option                     | Notes                                          |
|----------------------------|------------------------------------------------|
| Name A–Z / Z–A             |                                                |
| Cost low–high / high–low   | Cards with no cost sort last                   |
| ATK high–low               |                                                |
| HP high–low                |                                                |
| Rarity common→legendary    | Follows order defined in `rarities` meta       |
| Rarity legendary→common    |                                                |
| Faction                    | Alphabetical by faction key                    |
| Type                       | Alphabetical by type                           |

---

## Layout and aspect ratios

The app has grid view and list view. Toggle with the buttons in the top-right header.

To change card proportions, find these CSS rules in `index.html`:

```css
/* Main art area in grid view */
.card__art {
  aspect-ratio: 4/3;       /* change to e.g. 3/4 for portrait cards */
}

/* Art panel width in list view */
.cards-grid.is-list .card__art {
  width: 130px;
  min-width: 130px;
}

/* Modal header art */
.modal__art {
  aspect-ratio: 16/7;      /* change to 16/9 for wider, 4/3 for square-ish */
}

/* Edition thumbnails in modal */
.modal__ed-art {
  aspect-ratio: 5/7;       /* standard card ratio — change to 2/3 if preferred */
}
```

Minimum card column width in grid:
```css
.cards-grid {
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
}
```
Change `240px` to make cards wider or narrower.

---

## Adding new content

### New card
Add a JSON object to the `cards` array in `cards.json`. The app rebuilds all filters dynamically on load — no other changes needed.

### New faction
Add a key to `factions` in `cards.json`. Appears automatically in faction filter pills. Use cards with `"faction": "your-new-key"`.

### New keyword
Just use it in a card's `keywords` array. New keywords appear in the keyword filter automatically.

### New card type
Just use it in a card's `type` field. Appears in the type filter automatically.

### New edition / set
Use any string in an edition's `name` field. Appears in the edition filter automatically.

### New rarity
Add a key to `rarities` in `cards.json`. Set `order` to control sort position.

---

## Cloudinary tips

Cloudinary URLs are automatically upgraded to `w_1400,f_auto` when opened in the lightbox — you don't need separate high-res uploads.

For video poster images, append `/so_0/` to the upload path and change the extension to `.jpg`:
```
https://res.cloudinary.com/YOUR/video/upload/so_0/your-video.jpg
```
This generates a thumbnail from frame 0 of the video without a separate upload.

---

## Deployment

### GitHub Pages
1. Push `index.html` and `cards.json` to a repository.
2. Settings → Pages → Source: Deploy from branch → main → / (root).
3. Wait ~60 seconds, then visit `https://USERNAME.github.io/REPO/`.

### Custom domain
Add a `CNAME` file to the repo root containing your domain (e.g. `cards.eryxian.com`). Configure DNS per GitHub documentation.

### Linking from the Eryxian site menu
In Publii, add an external link menu item pointing to your GitHub Pages URL.

### Updating cards
Edit `cards.json`, commit, push. GitHub Pages redeploys in ~30 seconds. No build step.

---

## File structure for a larger deployment

If you want to split card data by faction for easier editing:

```
eryxian-codex/
├── index.html
├── cards.json          ← master file (can be generated from parts)
├── data/
│   ├── humans.json
│   ├── eryxians.json
│   ├── collective.json
│   └── ancients.json
└── assets/
    └── frames/
        ├── frame-silver.png
        ├── frame-gold.png
        └── frame-ancient.png
```

The app currently loads a single `cards.json`. To load multiple files, modify the `loadData()` function to fetch and merge them — the `normaliseCard()` function handles any card object correctly regardless of source.

---

## License

Theme and app code — MIT.  
Card art, lore, and game design — © 2023-2026 Daniel Sandner / Eryxian.  

