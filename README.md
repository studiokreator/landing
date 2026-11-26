# A space on the web.

A universal, brand-free landing page for domains that are not hosting a website.
One HTML file, no JavaScript, no external requests, no build step.

It is designed to be deployed unchanged on any domain or subdomain, and to look
like a deliberate design choice rather than an error page or a parking page.

Designed and built by [Kreator](https://studiokreator.com/) - modern software
engineering.

## What this is for

- A domain that has no website yet.
- A server whose default virtual host needs a presentable page.
- A fallback for requests that match no configured site.
- An intentionally parked domain, without advertising.

The page contains no domain name, organisation name, contact details, calls to
action, analytics or third-party resources, so it stays neutral for any owner.

## Deploy

Copy `index.html` to the web root of the host. That is the whole deployment.

```
/var/www/default/
└── index.html
```

`README.md` and `LICENSE` do not need to be uploaded.

### Nginx (default server)

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;

    root /var/www/default;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

`default_server` makes this block answer every request that does not match
another `server_name`. Add an equivalent `listen 443 ssl default_server;`
block once a certificate is available.

### Apache (default virtual host)

```apache
<VirtualHost *:80>
    ServerName _
    DocumentRoot /var/www/default
    DirectoryIndex index.html
    FallbackResource /index.html

    <Directory /var/www/default>
        Options -Indexes
    </Directory>
</VirtualHost>
```

On Debian and Ubuntu this is usually `/etc/apache2/sites-available/000-default.conf`,
enabled with `a2ensite 000-default`. On other systems the first virtual host in
the configuration acts as the default.

### Caddy

```caddy
:80 {
    root * /var/www/default
    try_files {path} /index.html
    file_server
}
```

### GitHub Pages

1. Push this repository to GitHub.
2. Open **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**.
4. Select the `main` branch and the `/ (root)` folder, then save.

The page is served at `https://<user>.github.io/<repo>/`. If you attach a custom
domain, add the `CNAME` file in that deployment only, never in the shared
template.

### Cloudflare Pages

Connect the repository, choose no framework preset, leave the build command
empty and set the output directory to `/`. Alternatively, use **Direct Upload**
and drop `index.html` in. No other configuration is needed.

## Customize

All visual settings live in the `:root` block at the top of `index.html`.

| Token | Default | Controls |
| --- | --- | --- |
| `--paper` | `#F5F2EC` | page background |
| `--paper-top` | `#FAF8F3` | top of the background gradient |
| `--ink` | `#171613` | headline colour |
| `--muted` | `#6B665C` | supporting sentence |
| `--muted-soft` | `#706A5F` | secondary line |
| `--line` | `#CFC8BA` | hairline |
| `--wave-line` | `#8B8274` | background waves |
| `--accent` | `#8C6F4E` | the dot and one wave |
| `--font` | system sans stack | typeface |

The copy is plain text in three elements:

```html
<h1>A space on the web.</h1>
<p class="lede">This domain is not currently<br>hosting a website.</p>
<p class="note">Please check back another time.</p>
```

The `<br>` in the supporting sentence sets the line break; remove it to let the
text wrap on its own.

Keep the wording neutral: no "coming soon", no "under construction", no offer
to sell, no technical details.

The favicon is an inline SVG data URI that repeats the accent colour. If you
change `--accent`, update the `fill` value in the `<link rel="icon">` tag too.

The background waves use `--wave-line`, with one wave in `--accent`. Each wave
is a filled ribbon with a vertical transparent → colour → transparent gradient
defined in the inline SVG; a radial mask dissolves the field at the viewport
edges. Strength is controlled by the opacity of `.waves-bg`; set it to zero for
a plainer composition. They drift on long, staggered CSS-only cycles, which
stop under `prefers-reduced-motion`.

## Keeping it universal

Before reusing the page on another domain, check that:

- no domain, hostname, owner, company or project name appears in `index.html`;
- no analytics, tracking, cookies, fonts or images are added;
- no contact details, social links or calls to action are added;
- all references remain inline, so the page works from any path or host;
- the page still renders completely with JavaScript disabled.

## Local preview

Open `index.html` directly in a browser, or serve the folder:

```sh
python3 -m http.server 8000
```

## License

[MIT](LICENSE)
