# Krop Portfolio

A one-page portfolio website that is a single HTML file plus a PDF. There is no build step, no framework and no package to install. It has an intro animation, hover effects, a light and dark theme, and a CV section that shows your PDF on the page with Share and Download buttons.

## Contents

- [What is in the folder](#what-is-in-the-folder)
- [Quick start](#quick-start)
- [Customising the content](#customising-the-content)
- [Replacing the CV](#replacing-the-cv)
- [How the CV viewer works](#how-the-cv-viewer-works)
- [Theme](#theme)
- [Animations](#animations)
- [Responsive behaviour](#responsive-behaviour)
- [Hosting it](#hosting-it)
- [Performance](#performance)
- [Accessibility](#accessibility)
- [Browser support](#browser-support)
- [Troubleshooting](#troubleshooting)

## What is in the folder

| File | Purpose |
| --- | --- |
| `index.html` | The whole site: markup, styles and scripts in one file. |
| `cv.pdf` | The CV the page displays. The one included is a sample. Replace it with your own. |
| `README.md` | This file. |

Keep `index.html` and `cv.pdf` in the same folder. The page looks for `cv.pdf` next to itself.

## Quick start

1. Replace `cv.pdf` with your own CV, keeping the name `cv.pdf`.
2. Edit the text in `index.html` (see [Customising the content](#customising-the-content)).
3. Preview it locally:

   ```bash
   cd portfolio
   python3 -m http.server 8000
   ```

   Then open <http://localhost:8000>.

4. Upload the folder to any static host (see [Hosting it](#hosting-it)).

Opening `index.html` by double-clicking it also works, but the CV then uses the browser's own PDF viewer instead of the built-in renderer. See [How the CV viewer works](#how-the-cv-viewer-works).

## Customising the content

Everything is in `index.html`. Search for the text you want to change.

### Name

Change "Krop" in these places:

- `<title>` and the `<meta name="description">` in the head.
- The brand link in the header (`<a class="brand">`).
- The large hero heading. Each letter is its own `<span style="--i:N">` so it can animate separately. If your name has a different number of letters, add or remove spans and keep `--i` counting up from 0.
- The intro screen (`.intro-word`), which uses the same one-span-per-letter pattern.
- The footer, and the `"Krop CV"` share title and `Krop-CV.pdf` download name in the CV section and script.

### Hero text

The two paragraphs inside `.hero-meta`: a one-line description of what you do, and a small block with the year, location and availability.

### Selected work

Each project is one `<li>` inside `<ul class="work">`:

```html
<li>
  <a href="https://your-project.example" data-h="250" data-i="A">
    <span class="y mono">2026</span>
    <span class="t">Project name</span>
    <span class="arrow" aria-hidden="true">&#8599;</span>
    <p class="d">Two lines on the problem, your role and the result.</p>
  </a>
</li>
```

- `href`: where the project lives. The samples use `#work`, which makes the link do nothing. Set a real address and the link opens it. The script only blocks clicks on links whose `href` is still `#work`.
- `data-h`: a colour hue from 0 to 360 for the tile that follows the pointer on hover.
- `data-i`: the short label shown in that tile, usually one letter.
- Copy a whole `<li>` to add a project, and delete one to remove it.

### About

The two paragraphs and the skills list in the `#about` section. Each skill is one `<li>` in `<ul class="skills">`.

### Contact

The email address is the text inside `<p class="mail" id="mail">`. The Copy email button copies whatever is in there. Write the address as plain text. Email links (`mailto:`) are deliberately not used because they fail in many embedded browsers.

## Replacing the CV

1. Export your CV as a PDF.
2. Name it `cv.pdf`.
3. Put it in the same folder as `index.html`, replacing the sample.

If you would rather use another file name, search `index.html` for `cv.pdf` and change all four places: the Download link, the `getDocument("cv.pdf")` call in the script, and the two references in the fallback viewer code.

The Download button saves the file as `Krop-CV.pdf`. Change the `download="Krop-CV.pdf"` attribute to rename it.

Tips for the PDF:

- Keep it small. A one or two page CV should be well under 1 MB. Large files slow the first view.
- Use standard page sizes (A4 or Letter). The page is drawn at up to 820 pixels wide.
- Make sure text is real text rather than a scanned image if you want people to be able to select and search it.

## How the CV viewer works

The CV section has two ways of showing the PDF.

**Main renderer.** When you scroll to within about 600 pixels of the CV section, the page loads [pdf.js](https://mozilla.github.io/pdf.js/) version 3.11.174 from cdnjs and draws each page onto a canvas. This looks the same in every browser, including phones that cannot show a PDF inside a page. Nothing is loaded until the section is near, so the first visit stays light.

**Fallback viewer.** If pdf.js cannot load, cannot read the file, or has not drawn anything after 8 seconds, the page switches to the browser's built-in PDF viewer (`<object type="application/pdf">`). If the browser cannot show PDFs inline, it shows a link to open the file instead.

The fallback is what you will see in these situations:

- You open `index.html` straight from disk. Browsers block pages opened this way from reading local files, so pdf.js cannot load the PDF.
- You are offline, or cdnjs is blocked on your network.
- The browser has pdf.js loading disabled.

The Download and Share buttons work in every case.

To avoid the external request entirely, download `pdf.min.js` and `pdf.worker.min.js` from the pdf.js release, put them in the folder, and change the `PDFJS` address in the script to `""` so they load from your own site.

### Buttons

- **Share** opens the device's share sheet when the browser supports it (most phones, Safari, recent Edge). Otherwise it copies the page address and shows a short message. The shared address is whatever is in the address bar, so sharing only makes sense once the site is hosted.
- **Download** is a normal link with the `download` attribute. It saves the PDF.

## Theme

The page follows the device's light or dark setting automatically. The Theme button in the header switches it, and the choice is remembered in the browser (stored under the key `theme` in `localStorage`). If storage is unavailable, the toggle still works and simply forgets on reload.

All colours are variables at the top of the `<style>` block:

| Variable | Used for |
| --- | --- |
| `--bg` | Page background |
| `--fg` | Main text |
| `--muted` | Secondary text and labels |
| `--line` | Borders and dividers |
| `--accent` | Highlights, hover colour, intro bar |
| `--accent-ink` | Text placed on the accent colour |
| `--panel` | Hover fill and the CV panel |

Each variable is defined three times: for light, for dark when the device is set to dark, and for dark when the toggle forces it. When you change a colour, change it in all three places so both themes stay consistent.

Fonts are also variables: `--display` for text and headings, and `--mono` for small labels. The page loads Bricolage Grotesque from Google Fonts and falls back to the system font if it cannot. To drop the external font entirely, delete the three `<link>` lines for Google Fonts in the head. The page then uses the system font and one less request is made.

## Animations

| Effect | Where | How |
| --- | --- | --- |
| Intro screen | On load | The name rises letter by letter over a dark panel with a growing accent bar. The panel slides away after about 1.5 seconds. |
| Hero reveal | After the intro | The big name rises letter by letter, then the two intro paragraphs fade up. |
| Navigation underline | Header links | A line draws in from the left on hover. |
| Work list | Project rows | A panel fills behind the row, the row shifts right, the arrow moves, and a coloured tile follows the pointer. |
| Skills | About section | Chips lift and fill with the accent colour on hover. |
| Buttons | CV and Contact | A fill slides up from below on hover. |

Notes:

- The intro and hero are pure CSS, so they finish on their own even if the script fails.
- The pointer-following tile only appears on devices with a mouse. It is hidden on touch screens.
- Anyone whose device is set to reduce motion sees no intro and no movement, and the page appears immediately.

Timing is controlled by the `animation-delay` values in the CSS: `.intro` (`1.5s`), `.hero h1 span` (`2.1s` plus a step per letter) and `.fade` (`2.7s`). If you remove the intro, set the hero and fade delays to `0s` and delete the `.intro` element.

## Responsive behaviour

The layout is built to fit from 320 pixels wide up to large desktops:

- The content is centred with a maximum width of 1120 pixels and a side gutter of at least 16 pixels. The gutter also clears rounded corners and notches on phones.
- The hero name scales with the screen width.
- On narrow screens the header brand moves onto its own line, project rows stack their year, title and description, and the About columns become one column.
- On phones the CV Share and Download buttons share the full width.
- The page never scrolls sideways. This was checked at 320, 390 and 1280 pixels in both themes.

## Hosting it

Any static host works because the site is only files. Upload the folder contents (`index.html` and `cv.pdf`).

- **GitHub Pages**: push the folder to a repository, then in Settings open Pages and choose the branch. The site appears at `https://<user>.github.io/<repository>/`.
- **Netlify**: drag the folder onto the Netlify dashboard, or connect the repository. No build command is needed, and the publish directory is the folder itself.
- **Cloudflare Pages**: create a project from the repository or by direct upload. Leave the build command empty.
- **Vercel**: import the repository and select "Other" as the framework. Leave the build command empty.
- **Your own server**: copy the files into the web root.

Use HTTPS. It is the default on all of the hosts above, and the Share button and the clipboard need a secure address to work properly.

After hosting, open the page on a phone and a laptop, and check that the CV shows and that Download saves the file.

## Performance

- `index.html` is about 20 KB, including all styles and scripts.
- No framework or build output.
- One optional font request, with a system font fallback.
- pdf.js (roughly 300 KB plus its worker) is fetched only when the CV section is about to be scrolled into view. A visitor who never reaches the CV never downloads it.
- Animations use transform and opacity, which browsers draw smoothly without redoing layout.
- The CV is drawn at the device's pixel density, capped at 2x, to keep memory use sensible on phones.

## Accessibility

- Visible keyboard focus on every link and button.
- Each CV page is exposed as an image with a label such as "CV page 1".
- The status message for Share and Copy email is announced to screen readers.
- Colours are chosen for readable contrast in both themes.
- Reduced-motion preferences are respected.
- The decorative intro and arrows are hidden from screen readers.

## Browser support

Current versions of Chrome, Edge, Firefox and Safari, on desktop and mobile. The page uses CSS variables, grid, `IntersectionObserver` and `localStorage`, with a safe fallback where it matters:

- No `IntersectionObserver`: the CV loads immediately.
- No `navigator.share`: Share copies the link.
- No clipboard access: Share and Copy email show a message asking to copy by hand, or select the text.
- No `localStorage`: the theme is not remembered.

## Troubleshooting

**The CV area shows "Loading CV…" for a long time.** After 8 seconds it switches to the fallback viewer. If it still does not show, check that `cv.pdf` is in the same folder as `index.html` and that the name matches exactly, including lowercase.

**The CV shows the browser's PDF viewer instead of the drawn pages.** This is the fallback. It is normal when the file is opened from disk or without internet. Host the site or run the local server from [Quick start](#quick-start) to use the main renderer.

**The CV is blank or says the browser can't show the PDF, on a phone.** Some phone browsers cannot show PDFs inline. On a hosted site with internet the main renderer avoids this. If it still fails, Download and the Open link still work.

**Share does nothing visible.** On desktop browsers without a share sheet it copies the link and shows "Link copied" for two seconds at the bottom of the screen. If clipboard access is blocked it shows a message to copy the address by hand.

**Theme does not stick after reload.** Browser storage is blocked, for example in a private window. The toggle still works during the visit.

**The intro plays every visit.** That is by design. To remove it, delete the `.intro` element and set the hero animation delays to `0s`.

**The fonts look different from the screenshot.** The Google Font did not load, so the system font is used. This is harmless. Check your network or remove the font links to make the system font permanent.
