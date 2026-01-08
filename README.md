# VennCompare - Shinylive Edition

A Quarto-based Venn diagram comparison tool that runs entirely in the browser using WebAssembly (no server required).

## Features

- Compare 2-7 lists with interactive visualizations
- Venn diagrams for 2-4 sets
- UpSet-style plots for 5-7 sets  
- Customizable colors for each set
- Export results as CSV
- **Runs entirely client-side** - host on any static server

## Prerequisites

1. **Quarto** (≥1.4): https://quarto.org/docs/get-started/
2. **Quarto Shinylive extension**

## Setup

### 1. Install the Shinylive extension

```bash
quarto add quarto-ext/shinylive
```

### 2. Preview locally

```bash
quarto preview index.qmd
```

This will open a browser with the app running locally.

### 3. Build for deployment

```bash
quarto render index.qmd
```

This creates a `_site/` or `index_files/` directory with all static assets.

## Deployment Options

### GitHub Pages

1. Create a GitHub repository
2. Add all files including the rendered output
3. Enable GitHub Pages in repository settings (Settings → Pages → Source: main branch)
4. Your app will be live at `https://username.github.io/repo-name/`

**Quick setup with GitHub Actions:**

Create `.github/workflows/publish.yml`:

```yaml
name: Build and Deploy

on:
  push:
    branches: [main]

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
      
      - uses: quarto-dev/quarto-actions/setup@v2
      
      - name: Install Shinylive extension
        run: quarto add quarto-ext/shinylive --no-prompt
      
      - name: Render
        run: quarto render index.qmd
      
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./_site
```

### Quarto Pub

```bash
quarto publish quarto-pub index.qmd
```

### Netlify / Vercel

Simply connect your GitHub repo and set the build command to:
```
quarto render index.qmd
```
And publish directory to `_site`.

## File Structure

```
venn_shinylive/
├── index.qmd           # Main Quarto document with Shiny app
├── README.md           # This file
├── _quarto.yml         # Optional: Quarto project config
└── .github/
    └── workflows/
        └── publish.yml # Optional: GitHub Actions for auto-deploy
```

## Notes on Shinylive

- The app runs entirely in the browser using WebR (R compiled to WebAssembly)
- First load may take 10-20 seconds as R packages are downloaded
- Subsequent visits are faster due to browser caching
- No server costs - host anywhere that serves static files

## Differences from Server Version

This Shinylive version uses base R graphics instead of ggVennDiagram/UpSetR for better WebR compatibility. The functionality is equivalent:

| Feature | Server Version | Shinylive Version |
|---------|---------------|-------------------|
| Venn diagrams | ggVennDiagram | Base R graphics |
| UpSet plots | UpSetR | Custom base R implementation |
| Color selection | ✓ | ✓ |
| Intersection browser | ✓ | ✓ |
| CSV export | ✓ | ✓ |
| Hosting | Requires R server | Any static host |

## Author

Built with Quarto + Shinylive
Inspired by Venny (https://bioinfogp.cnb.csic.es/tools/venny/)
