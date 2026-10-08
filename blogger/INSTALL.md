# Installing the Blogger theme

Everything in `blogger/` is new. The original static site at the repo root
(`index.html`, `about.html`, `services.html`, `contact.html`, `style.css`) is untouched and
still works — this conversion does not modify or replace it.

| File | What it is |
|---|---|
| `theme.xml` | The Blogger theme. This is the main artifact. |
| `pages/about.html` | Body content for the About page |
| `pages/services.html` | Body content for the Services page |
| `pages/contact.html` | Body content for the Contact page |

**Test first.** Blogger themes can't be previewed locally, so do this on a throwaway
Blogger blog before touching a live one. If step 2 fails, nothing else is worth doing yet.

---

## 1. Back up whatever is currently on the blog

*Theme → ⋮ (next to Customise) → Backup*, and download the current theme. If the blog is
brand new this is a no-op, but do it anyway.

## 2. Paste in the theme

*Theme → ⋮ → Edit HTML*.

Select everything in the editor and delete it, then open `blogger/theme.xml` in a text
editor, copy **the entire file**, and paste it in. Click **Save**.

If Blogger rejects it, it will tell you a line number and a reason. Fix nothing by hand —
report the message and it will be corrected in the source file.

> **Paste the whole file**, including the `<?xml ... ?>` first line and the `<!DOCTYPE html>`
> on line 2. Both are required.

## 3. Turn off Blogger's mobile template

*Settings → Mobile* → choose **"No. Show desktop theme on mobile devices."**

The theme is already responsive. If you skip this, Blogger serves its own stripped-down
mobile template instead and the design is lost on phones.

## 4. Create the three pages

*Pages → New page*, three times. The **title must match exactly**, and set the **custom
permalink** in the right-hand page settings:

| Title | Custom permalink | Body to paste |
|---|---|---|
| `About` | `about` | `pages/about.html` |
| `Services` | `services` | `pages/services.html` |
| `Contact` | `contact` | `pages/contact.html` |

For each one: switch the editor from **Compose** to **HTML view**, then paste the file's
contents. **Do not paste the leading `<!-- ... -->` comment block** — it's an instruction
note for you, not page content.

Getting the permalinks right matters: the theme's navigation links to
`/p/about.html`, `/p/services.html` and `/p/contact.html`. If a permalink differs, that
nav link 404s.

Publishing order doesn't matter, but if you want the *Services* page's alternating
background stripes to sit where they do on the original site, keep the sections in the
order given in the file.

## 5. Add the contact form

*Layout* → find the **Contact Form** section → **Add a Gadget** → **Contact Form** → Save.

This is Blogger's own contact form: submissions are emailed to the blog's admin address,
with no third-party service and no API key. It only renders on the page titled `Contact`
(the theme is what enforces that), and it appears **below** the contact details rather
than beside them — see "Known differences" below.

## 6. Comments off, navbar off, post count

- **Comments** — *Settings → Comments → Comment Location* → **Hide**. The theme has no
  comment markup, so this just keeps things consistent.
- **Navbar** — *Layout → Navbar → Edit → Off*. The theme also hides it in CSS, so this is
  belt-and-braces.
- **Post count** — *Settings → Posts → Max posts shown on main page* → **3**. This
  controls how many posts the "Insights" listing shows per page.

## 7. Check it

Visit each of these on the test blog and confirm the design:

- [ ] **Homepage** — should be pixel-identical to the current site, with the documents
      tabs on "Private Limited" clickable and switching panels
- [ ] **Insights** (nav link → `/search`) — post list. Empty until you publish a post.
- [ ] **About** / **Services** / **Contact** — hero, content, footer
- [ ] **Services** — the sticky sub-nav jumps to each of the nine sections
- [ ] **Contact** — the form renders below the contact details and actually sends
- [ ] **A published post** — article layout
- [ ] **A bad URL** (e.g. `/p/does-not-exist.html`) — the 404 page
- [ ] **On a phone** — the hamburger appears, tapping it drops down the menu with all six
      links plus the "Book a consultation" button, the icon animates to an ✕, and the menu
      closes when you pick a link, tap outside, or press Escape

### Text round-trip check

Blogger's editor can re-encode non-ASCII characters. After saving, open *Edit HTML* again
and confirm these survived:

- `—` and `–` (em/en dashes) in the footer and service copy
- `©` in the footer
- `·` in the hero eyebrow
- `→` in "See all services →"
- `—` used as the list bullet marker in `.svc-block li::before` and `.doc-panel li::before`

If any turned into `?` or mojibake, the theme needs re-pasting with the encoding checked.

---

## How it's put together

- **The homepage is hardcoded in the theme.** The hero, services grid, process timeline,
  documents tabs and reviews live in `theme.xml`. Editing that copy means editing the
  theme, not the Blogger UI. There's an editable **Homepage extras** section at the bottom
  of the homepage if you want to add gadgets without touching XML.
- **About / Services / Contact are real Blogger Pages**, so their content is editable in
  the normal page editor.
- **All CSS lives in one `<b:skin>` block.** There is no external stylesheet. The brand
  colours are exposed as Theme Designer variables — *Theme → Customise → Advanced → Brand
  Colours* lets you retint the site without touching CSS. Note that `--line` (the hairline
  borders) is a literal `rgba()` and won't follow a colour change.
- **Navigation uses root-relative links** (`/p/about.html`, `/search`), not Blogger data
  tags, so the same file works on `*.blogspot.com` and on a custom domain with no edits.
- **The stylesheet is deliberately 100% ASCII.** Blogger re-encodes non-ASCII characters
  as HTML entities when it saves a theme. That's harmless in HTML text (browsers decode
  `&#8212;` back to `—`), but CSS has no concept of HTML entities, so a `content:'—'`
  becomes the six literal characters `&#8212;` on the page. Any character that must appear
  in CSS `content:` is therefore written as a CSS escape — the list bullets use
  `content:'\2014'`, not a literal em dash. **If you add CSS with a dash, symbol or accent
  in `content:`, use the escape form** (`\2014` em dash, `\2192` →, `\00a9` ©).
- **The homepage deliberately renders no post list**, per the agreed scope.

## Known differences from the static site

These are intentional, not bugs:

1. **The contact form sits below the contact details** instead of beside them. Blogger's
   contact form is a gadget, and a gadget cannot be placed inside a page's body content —
   so it renders in its own section after the page. Moving it earlier would require
   giving up the native form.
2. **"Insights" was added to the nav** so the blog is reachable. Remove it by deleting that
   one `<a href='/search'>` line in `theme.xml` if you don't want it.
3. **The brand logo is now a link** on every page, including the homepage. In the original
   static site the homepage logo wasn't clickable while the other three pages' were.
4. **Footer service links point at the deep anchors** (`/p/services.html#gst`) on all
   pages. The original was inconsistent — `index.html` and `about.html` pointed at
   `services.html` with no anchor.
5. **The four near-duplicate copies of the header and footer are collapsed into one.** The
   original had drifted slightly between files; this picks one canonical version.

## Not done here

Pointing the GoDaddy domain at Blogger and moving off Vercel is a separate piece of work
with its own risks (301 redirects, Search Console, canonical tags) — worth planning on its
own rather than bolting onto this. The same URLs will resolve on a different host after
the cutover, and search rankings can suffer if that isn't handled deliberately.