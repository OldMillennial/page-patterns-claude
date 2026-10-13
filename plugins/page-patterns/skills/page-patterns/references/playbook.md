# Page Patterns backgrounds

How to give a section of a website a geometric pattern background with the Page Patterns tools: read the site, explore variants, show the user options in their real page, then put the chosen SVG in place.

## Summary

Page Patterns makes geometric pattern backgrounds (triangles, hexagons, cubes, halftone dots, fine line and contour textures and more) as small SVG files that sit behind a section's content. Work in four steps:

1. Read the site: the section's colour, the size of the box the pattern will fill at 1440 and 390 px wide, and where the headline and buttons sit.
2. Explore and shortlist: send 12 to 16 variants to `explore`, study the contact sheet, refine the best with `from`, and keep 3 or 4 varied options.
3. Present: show each option in the user's real page and let them pick.
4. Implement: `export` with `insertInto` puts the chosen SVG in the page; add the returned CSS and check both widths.

Without the page-patterns skill loaded, call `guide` for the full instructions first.

## Step 1: Read the site

1. Find the section the user means and the element that paints its colour.
2. Read the CSS: the background colour or gradient, and the site's colour tokens (custom properties such as `--brand`). Note related shades in the same palette, such as a darker version of the brand colour. They make good pattern colours. Give gradient angles in CSS degrees, as in `linear-gradient(180deg, ...)`; a radial gradient comes out as a centred circle.
3. Decide which box the pattern will fill. Usually it is the section itself: the SVG is positioned to fill it, behind the content.
4. Check where the colour is covered or stops. It often does not fill the whole section:
   - a child element can paint over part of it, for example a band with `linear-gradient(var(--brand) 0 50%, var(--grey) 50% 100%)` that turns grey halfway down;
   - photos, cards or video can sit on top of it;
   - padding and text wrapping change its height at each width.

   Note these. Step 4 deals with them.
5. Measure the box at 1440 px and at 390 px wide. With a browser, resize the viewport and use `getBoundingClientRect()`. Without one, work it out from the CSS and tell the user the numbers are estimates.
6. Measure the content box: the smallest rectangle around the headline, subheading and buttons, in px from the top-left corner of the box from item 3. Measure it at both widths.

Worked example: a hero with centred copy on deep red (`#8b0000`). Inside the section, below the copy, a band paints its top half red and its bottom half grey, with a wide photo on top. The pattern fills the whole section. The photo hides the pattern behind it; in step 4 the band's red top half is made transparent, so the pattern shows either side of the photo while the grey half covers the rest.

## Step 2: Explore and shortlist

1. Call `describe_options` once. It lists the presets and shapes, what each suits, and every setting.
2. Build `site` from step 1: `background`, `width`, `height` and `contentBox` at 1440 px, and `phone` with the 390 px measurements.
3. Round 1: call `explore` with 12 to 16 variants spread across:
   - shapes that suit the brand and its industry;
   - colours: black, white, and a darker or lighter shade from the site's own tokens;
   - strength: mostly `subtle` and `medium`, one or two `bold`;
   - emphasis: `edges` for centred copy. When the copy sits on one side, choose the side away from it (`right` when the copy is on the left), because `emphasis` names where the pattern stays strongest.

   Always set `colours` and `strength`, because presets were tuned for other backgrounds. Set `clearBehindContent: true` whenever text sits on the pattern.

   Colours are hex (`#8b0000` or `#fff`); convert `rgb()`, `hsl()` or named colours first. Each side of the area must be 16 to 4000 px, and designs over about 60,000 shapes are refused, so use a larger `size` on big areas.
4. Study the contact sheet. The dashed outline is the content box. Drop variants that compete with the headline, look busy, or disappear.
5. Round 2: refine the best 4 to 6 with `from`, changing one or two settings each: strength, size, emphasis, or `seed` for a different arrangement of the same pattern. If you send `advanced` with `from`, include every advanced setting you want to keep: `advanced` replaces as a whole.
6. Run a third round only if nothing works yet. Stop after three.
7. Shortlist 3 or 4 by the design rules below.

## Step 3: Present

Show the options in the user's real page, not as bare swatches.

With a browser the user can see, such as the Claude desktop app's browser pane:

1. For each shortlisted code, call `export` with `parentSelector` set to a class selector for the section (for example `.hero`). It must be a class selector that matches the copies too, not an id. Use `downloadUrl`, `css` and `variables` from the result; you do not need the SVG text.
2. Add the CSS once, then add one labelled copy of the section per option, in memory only, with no file edits. Run this in the page for each option, filling in the values:

   ```js
   (async () => {
     const original = document.querySelector('.hero:not([data-page-patterns-option])');
     if (!document.getElementById('page-patterns-css')) {
       document.head.insertAdjacentHTML('beforeend', `<style id="page-patterns-css">${CSS}</style>`);
     }
     const copy = original.cloneNode(true);
     copy.removeAttribute('id');
     copy.dataset.pagePatternsOption = 'A';
     copy.querySelectorAll('svg[data-page-patterns]').forEach((n) => n.remove());
     copy.insertAdjacentHTML('afterbegin', await (await fetch(DOWNLOAD_URL)).text());
     for (const [name, value] of Object.entries(VARIABLES)) copy.style.setProperty(name, value);
     const label = document.createElement('p');
     label.dataset.pagePatternsLabel = '';
     label.style.cssText = 'margin:0;padding:12px 16px;font:600 16px/1.3 system-ui,sans-serif;background:#fff;color:#111';
     label.textContent = 'Option A';
     original.before(label, copy);
   })();
   ```

   Each run places its labelled copy directly above the real section, so the options stack in order A, B, C, D. If the fetch fails (the site's Content-Security-Policy or the browser blocks requests to 127.0.0.1), use `preview` for the options instead. If `export` returns no `downloadUrl`, use `preview` for the options.

   Apply any fix from step 4 (such as the transparent band) to the copies too.
3. Screenshot each copy at 1440 and 390 px and check them yourself.
4. Tell the user the options are stacked in the browser, labelled A to D, with one line each on how they differ. Mention the file size of any option over 30 KB.

Without a browser the user can see, call `preview` with the shortlisted codes and labels A to D, and show the user those images.

Then:

- Accept a pick or a change request ("B, but stronger"). Refine with `from` and show the changed option again.
- Remove the copies when done (`[data-page-patterns-option]`, `[data-page-patterns-label]` and `#page-patterns-css`), or reload the page.
- Do not edit any file until the user has picked.

## Step 4: Implement

1. Tell the user which files you will change.
2. In the page's HTML, add an empty placeholder as the first child of the section, named for where it is: `<svg data-page-patterns="hero"></svg>`.
3. Call `export` with the chosen code, `parentSelector` set to the section's class selector, and `insertInto: { file, slot: "hero" }` with the absolute path of the HTML or PHP file. It replaces the placeholder with the SVG and leaves the rest of the file untouched. Calling it again with another code swaps the pattern.
4. Step 4 edits HTML or PHP files. For JSX, Vue or other templates, call `export` with `format: "inline"` and `savePath` (an absolute path ending in `.svg`). Without `format: "inline"`, `savePath` writes the standalone file format, which bakes in the background and returns no `css`. Include the saved SVG inline the way that framework does (so CSS `color` can still recolour it), then add the returned `css` as in item 5. Or use `format: "react"` for a React component. On a hosted server, which has no `savePath`, use `curl.exe -o file.svg "<downloadUrl>"` (`curl` on macOS and Linux; in Windows PowerShell, plain `curl` is an alias of Invoke-WebRequest).
5. Add the returned `css` to the site's stylesheet. If the section already sets `position`, `overflow` or `background`, merge the rules rather than repeating them. The CSS sets only its own variables (`--pattern-bg`, `--pattern-tint`, `--pattern-strength`), so the site's tokens are untouched; after the change, check that buttons and links in the section still read as before.
6. Deal with what you noted in step 1, item 4. Where a child paints the brand colour over part of the section, make that part transparent so the pattern shows through; do not resize the SVG to fit. In the worked example, the band's background changes from `linear-gradient(var(--brand) 0 50%, var(--grey) 50% 100%)` to `linear-gradient(transparent 0 50%, var(--grey) 50% 100%)`.
7. Reload and screenshot at 1440 and 390 px. Check the headline is as readable as before and the pattern shows only where the colour does. If the section had content that deliberately overflowed it (for example a photo hanging over the next section), check it is not clipped: the CSS sets `overflow: hidden`, which you can remove because the SVG clips itself.
8. If the 390 px crop moves the quiet area off the text (more likely when the copy sits to one side), explore a second design measured at 390 px. Add its placeholder next to the first one, `<svg data-page-patterns="hero-phone"></svg>`, export it with `className: "pattern-bg-phone"` and `insertInto` with that slot, and add only its `.pattern-bg-phone` rule from the returned CSS. If the phone design's first colour differs, add `color: <its --pattern-tint value>` to the `.pattern-bg-phone` rule. Then show one at a time, using the site's own breakpoint:

   ```css
   .hero .pattern-bg-phone { display: none; }
   @media (max-width: 600px) {
     .hero .pattern-bg { display: none; }
     .hero .pattern-bg-phone { display: block; }
   }
   ```

9. Report the file size, the share link (it opens the design in Page Patterns for fine tuning by hand) and the CSS variables: `--pattern-tint` recolours the first shape colour and the sheen, and `--pattern-strength`, from 0 to 1, fades the pattern.

## Design rules

- Nothing competes with the headline. Behind the text the pattern stays close to the flat colour; `clearBehindContent` does this.
- The shortlist is varied: different shapes or colour approaches, not four strengths of one pattern.
- Restraint by default. Most business sites suit subtle or medium. Offer bold as one option at most.
- Use the site's own colours. Black and white at low opacity work on almost any brand colour; a darker or lighter shade of the brand colour reads as part of the palette.
- Clustered or mix variation reads as designed; random can look noisy at higher strengths.
- Keep hero SVGs under about 30 KB where possible. Bigger shapes and lower coverage make files smaller. Halftone patterns are large, and so are the textures (lines, topography, checkers): 80 to 180 KB on a 1440 px hero. Offer a texture when the brand wants quiet detail rather than shapes, and say its size.
- Do not add a gradient or a colour the site does not already use.

## Remote mode

On a hosted Page Patterns server there is no `insertInto` or `savePath` (locally, `savePath` writes the `.svg` file for you). Save the SVG from `downloadUrl` with `curl.exe -o file.svg "<downloadUrl>"` instead of retyping it (`curl` on macOS and Linux; in Windows PowerShell, plain `curl` is an alias of Invoke-WebRequest), then insert it into the page. If a call fails with a rate limit, wait the time it states before trying again.

## When a tool asks for a key

If `explore`, `preview` or `export` replies that Page Patterns needs a free key, or that the key isn't recognised or has been turned off, show the user that message as it is and stop. Don't retry or work around it. The user fixes it at https://pagepatterns.com/account, then restarts the MCP server.

## Tool reference

- `guide`: this document.
- `describe_options`: presets, shapes, settings and the advanced field reference.
- `explore`: up to 16 variants for one site; returns a contact sheet image and a code per variant.
- `preview`: 1 to 4 codes at desktop and phone size, with the content box outlined.
- `export`: the final SVG for one code, as inline markup with CSS and variables, a file, a CSS background, React or Tailwind; plus `downloadUrl`, a share link, and local writing with `savePath` or `insertInto`.
