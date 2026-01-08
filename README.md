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

## Author Abdulhadi Kobiowu

Built with Quarto + Shinylive
Inspired by Venny (https://bioinfogp.cnb.csic.es/tools/venny/)
