# Statistical analysis of top osu! players

R Markdown project for the course *Statistical Data Analysis* (Statystyczna analiza danych),
semester V, Faculty of Management, AGH University of Krakow (2024).

The analysis asks whether the official osu! ranking, which is driven by performance points (pp),
is a fair measure of skill, or whether a ranking that also accounts for accuracy, tournament
success and efficiency tells a different story.

Report: https://neeflez.github.io/osu-player-data-analysis/

The rendered report (`index.html`) is in Polish; this README summarises it in English.

## Data

`OsuPlayers.csv`: the top 50 players of the osu! global performance ranking (November 2024),
with these columns:

| Column | Meaning |
|---|---|
| `ranking` | position in the official pp ranking |
| `nazwa gracza` | player name |
| `celnosc` | accuracy (%) |
| `liczba zagran` | play count |
| `liczba pp` | performance points |
| `Ocena SS`, `Ocena S`, `Ocena A` | number of SS / S / A grades |
| `ilosc medali` | number of tournament medals (profile badges) |

Three accounts with an implausibly low play count for the top 50 (previously restricted players)
were identified on a scatter plot and removed, leaving 47 players.

## Methods

1. **Exploratory analysis**: summary statistics, standard deviations, scatter plots, histograms and box plots.
2. **Correlation analysis** (Pearson): medals vs accuracy, play count vs pp, and the same relationships
   within two groups: *tournament* players (4 or more medals) and *ranked* players (pp-focused).
3. **Linear ordering with Hellwig's method** to build an alternative ranking:
   - **Stimulants** (higher is better): accuracy, pp, number of medals.
   - **Destimulants** (lower is better): play count (rewards reaching the top faster) and a
     *stress index* = A grades / S grades (how often a single mistake drops an S to an A).
   - Destimulants are converted to stimulants (`max(x) - x`), all variables are **standardised** (z-scores),
     the **pattern** (ideal object) takes the maximum of each standardised variable, and each player's
     **Euclidean distance to the pattern** gives the synthetic measure `H = 1 - d / max(d)`.
   - SS grades are excluded: they duplicate the accuracy criterion and are easy to inflate.
4. **Cluster analysis**: k-means on standardised accuracy, play count, pp and medals (number of clusters
   chosen with the elbow method, k = 3), checked against Ward's hierarchical clustering (Euclidean distance).

## Main findings

- **Medals and accuracy are moderately correlated (r = 0.40).** Tournament players are clearly more accurate:
  the lower quartile of their accuracy is at about the median of ranked players.
- **"Just play more" does not hold at the top.** Play count and pp are only weakly correlated (r = 0.19);
  among ranked players the correlation is close to zero (r = 0.05), and among tournament players
  medals and play count are negatively correlated (r = -0.39).
- The pp distribution is strongly right-skewed. Most top players have around 150,000 plays,
  and the median accuracy is about 98 to 98.5%.
- **The Hellwig ranking differs a lot from the official one.** The top spot goes to the most successful
  tournament player (55 medals), who is 29th by pp; the official number one comes second, and two players
  ranked 38th and 39th by pp move into the top 10.
- **Clustering separates three play styles:** top pp-focused ranked players, a group of lower-ranked players
  with lower accuracy, and tournament players with many medals. The Ward dendrogram confirms the main split
  between high-accuracy / high-medal players and high-pp players.

Conclusion: a ranking based on pp alone underrates players with a broader skill set, especially tournament players.

## How to reproduce

Requirements: R (4.x) with RStudio or Pandoc, and these packages:

```r
install.packages(c("rmarkdown", "knitr", "kableExtra", "dplyr", "magrittr", "ggplot2", "factoextra"))
```

Render the report to `index.html` (the file served by GitHub Pages):

```r
rmarkdown::render("osu_player_analysis.Rmd", output_file = "index.html")
```

## Repository structure

```
├── osu_player_analysis.Rmd   # Analysis source (R Markdown, Polish)
├── index.html                # Rendered, self-contained report
├── OsuPlayers.csv            # Data: top 50 osu! players
├── styles.css                # Report styling
└── agh.jpg                   # Logo used on the report title page
```
