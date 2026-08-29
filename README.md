# Covid Relief Fund

A one-page site for a COVID-19 relief charity. It explains what the fund does, shows the three
things donations go towards (shelter, food and medicine, education), takes online donations through
a Razorpay payment button, and gives people a way to get in touch.

The site is plain HTML and CSS with a small amount of vanilla JavaScript. There is no framework, no
build step and no dependency to install: the files in this repository are the files that get served.

## Files

- `index.html` — the whole page, plus a short inline script for the navigation, the slideshow and
  the contact form.
- `style.css` — the only stylesheet. Custom properties at the top hold the palette and spacing
  scale; below that the layout is CSS grid and flexbox with `clamp()` for fluid type.
- `images/` — photographs used on the page. Each one is stored twice, as a WebP and as a JPEG
  fallback, and the page picks whichever the browser supports through `<picture>`.
- `favicon.png`, `images/apple-touch-icon.png`, `images/og.jpg` — the browser tab icon, the icon iOS
  uses when the page is saved to a home screen, and the preview image for social links.
- `.nojekyll` — tells GitHub Pages to serve the files as they are instead of running them through
  Jekyll.

## Running it locally

Any static file server will do. From the repository root:

    python -m http.server 8000

Then open http://localhost:8000 in a browser. Opening `index.html` straight off the disk mostly
works too, but serving it over HTTP is closer to production and avoids surprises with relative
paths.

There is nothing to build, so edits to `index.html` or `style.css` show up on a refresh.

## Deployment

The site is published with GitHub Pages from the default branch of this repository, at
https://vivekkumarq.github.io/Covid_Relief_fund_website/. Pushing to that branch is the deploy.

If you move the site to another domain, update the `canonical`, `og:url` and `og:image` URLs in the
`<head>` of `index.html` — those have to be absolute, so they are the only place a hostname is
hard-coded.

## Donations

The donate buttons are Razorpay payment buttons. Each one is an empty `<form>` containing Razorpay's
`payment-button.js` script tag with a `data-payment_button_id`; the script replaces itself with the
button. The button id lives in `index.html` and can be swapped for a different Razorpay payment
button without touching anything else. Razorpay is the only third-party origin the page contacts,
which is why it is the only host the page preconnects to.

## The contact form

GitHub Pages serves static files and cannot run server code, so there is nothing to POST a form to.
The contact form therefore hands the message to the visitor's own email client: the submit handler
builds a `mailto:` link from the three fields and follows it. If JavaScript is off, the form's
`action="mailto:..."` does roughly the same thing without the tidy subject line.

This keeps the form working with no backend and no third-party account. If you would rather have
messages arrive without the visitor's mail app opening, sign up for a form endpoint such as
Formspree or Basin, set the form's `action` to the URL they give you, set `method="post"`, and
delete the submit handler at the bottom of `index.html`. Nothing else needs to change.

## Notes on a couple of things that were removed

`index.php` used to sit alongside `index.html` and did nothing but `include_once("index.html")`. It
was a passthrough for a PHP host. GitHub Pages cannot execute PHP — it would have been served as a
plain text download — and every static host resolves `index.html` on its own, so the file was dead
weight and has been deleted.

`_config.yml` set a Jekyll theme. Because `index.html` has no front matter, Jekyll copied it through
untouched and the theme never applied to anything. It has been replaced with `.nojekyll`, which
skips the build entirely.

Bootstrap, Font Awesome and four Google Fonts families used to be loaded from CDNs. They accounted
for far more bytes than the page's own code and all of it was render-blocking, so the layout was
rewritten with grid and flexbox, the handful of icons became inline SVG, and the type now uses the
fonts already on the reader's machine.
