# Dr. Surendheran K. — Portfolio

An Astro static portfolio backed by file-based content collections and Decap CMS. Content edits are YAML files committed to GitHub; there is no database or application server.

## Local use

Requires Node.js 20.3+.

```bash
npm install
npm run dev
```

Open `http://localhost:4321`. The production check is `npm run build`; generated files go to `dist/`.

### Test the admin panel locally

The easiest option starts both required services in one Terminal window:

```bash
npm run admin
```

Then visit `http://localhost:4321/admin/`. Port 8081 is only the background file proxy and does not contain a webpage. Local mode does not require a login. When you save an entry, Decap writes directly into `src/content/` or `public/uploads/`; Astro will refresh the preview. These are real local file changes, so keep the ones you want and use Git to review or undo experiments.

## Content and media

- Structured entries live in `src/content/<collection>/`.
- CMS uploads go to `public/uploads/` and are committed with the content.
- Add video as a YouTube or Vimeo URL. Do not upload large video files to the repository.
- Collection schemas are in `src/content.config.ts`.
- The Decap form definitions are in `public/admin/config.yml`.

The CMS is connected to `surensmart/suren-portfolio` on GitHub.

## 1. Create and push the GitHub repository

1. On GitHub, create an empty repository named `suren-portfolio`. Do not add a README or `.gitignore there.
2. In this project folder, run:

```bash
git init
git add .
git commit -m "Launch portfolio"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/suren-portfolio.git
git push -u origin main
```

3. Update the `repo:` value in `public/admin/config.yml`, commit, and push again.

## 2. Recommended hosting: Netlify

This is the simplest route because Netlify can host both the static Astro site and the GitHub OAuth handshake required by Decap CMS.

1. Sign in to Netlify with GitHub and choose **Add new project → Import an existing project**.
2. Select the `suren-portfolio` repository.
3. Build command: `npm run build`. Publish directory: `dist`. No environment variables are required.
4. Deploy. In **Domain management**, add `surensmart.me` and `www.surensmart.me`.
5. In **Site configuration → Access & security → OAuth**, install/configure GitHub as an authentication provider. Decap's `github` backend uses Netlify's `/auth` endpoint; only GitHub users with push access to the repo can edit.
6. Set the Netlify production domain in `site_url`, `display_url`, and `logo_url` in `public/admin/config.yml` until the custom domain is active.

### Spaceship DNS for Netlify

In Spaceship Advanced DNS, remove conflicting A/AAAA/CNAME records for `@` and `www`, then add:

| Type | Host | Value | TTL |
|---|---|---|---|
| A | `@` | `75.2.60.5` | Automatic |
| CNAME | `www` | `YOUR-NETLIFY-SITE.netlify.app` | Automatic |

Replace only `YOUR-NETLIFY-SITE` with the subdomain Netlify assigns. Make `www.surensmart.me` the primary domain in Netlify; Netlify redirects the apex automatically. DNS can take up to 48 hours, though it is usually much faster.

## 3. Cloudflare Pages alternative

Cloudflare Pages hosts the site well, but Decap's GitHub login still needs an OAuth proxy. The low-maintenance option is to keep Netlify only for its OAuth endpoint; the fully Cloudflare option requires deploying a small Worker and storing a GitHub OAuth client secret.

1. In Cloudflare, choose **Workers & Pages → Create → Pages → Connect to Git** and select the repo.
2. Framework preset: Astro. Build command: `npm run build`. Output directory: `dist`.
3. Deploy, then open **Custom domains → Set up a custom domain** and add `surensmart.me` and `www.surensmart.me`.
4. Cloudflare requires the apex domain to use Cloudflare nameservers. Add the domain as a Cloudflare zone, then replace the current Spaceship nameservers with the two nameservers Cloudflare assigns. Cloudflare creates the Pages DNS records after the custom-domain flow.
5. If using only `www` while keeping Spaceship DNS authoritative, add this record instead:

| Type | Host | Value | TTL |
|---|---|---|---|
| CNAME | `www` | `YOUR-CLOUDFLARE-PROJECT.pages.dev` | Automatic |

Do not manually CNAME the apex to `pages.dev` unless Spaceship explicitly supports CNAME flattening/ALIAS. Use Cloudflare nameservers for reliable apex-domain support.

## 4. Using `/admin`

1. Visit `https://www.surensmart.me/admin/` and choose **Login with GitHub**.
2. Authorize the GitHub OAuth application. The account must have push access to the repository.
3. Pick a collection, choose **New**, complete the fields, and select **Publish**.
4. Decap commits the YAML file and any uploaded image to `main`. The host notices the commit and rebuilds the site automatically, normally within a few minutes.
5. To edit or remove an item, open the entry in the same collection and choose **Edit** or **Delete**.

If login redirects incorrectly, verify that the final custom domain is set in `site_url`/`display_url`, that the GitHub repo is correct, and that the OAuth provider's callback URL matches Netlify's value exactly.

## Notes before launch

- Dates inferred from month-only CV entries use the first or last day of the month for reliable sorting. The public UI displays month/year only.
- Several CV media items did not include source URLs; add their links later through `/admin`.
- The starter project cards intentionally use designed monograms until project photography or video links are uploaded.
- The headshot was recovered from the supplied CV. Replace `public/profile.jpg` at any time with a higher-resolution original using the same filename.
