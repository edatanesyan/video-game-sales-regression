# Regression Analysis of Global Video Game Sales

Multiple linear regression in R predicting global video game sales from critic score, genre and console, framed as a decision tool for a game publisher deciding where to invest.

Final project for BMGT430 at the University of Maryland, Robert H. Smith School of Business. 

**[View the full report](Final_Project_BMGT430.html)** (download and open in a browser, or view through GitHub Pages)

## The question

Many games fail to recoup their development costs. A publisher choosing which genres to fund, which platforms to target and how much to spend on polish needs to know which of those factors actually move sales, and by how much.

## Data

[Video Game Sales 2024](https://www.kaggle.com/datasets/asaniczka/video-game-sales-2024) from VGChartz via Kaggle: 64,016 releases from 1971 to 2024. After keeping titles with both a recorded sales figure and a critic score, and dropping zero-sales titles, the working sample is 3,978 games.

The CSV is not included in this repository. Download `vgchartz-2024.csv` from the Kaggle link and place it in the repository root.

## Method

- **Response:** log(global sales, millions of units). The log reduces the pull of a few extreme blockbusters and lets coefficients be read as percentage changes.
- **Predictors:** critic score (1 to 10), genre and console. Consoles with fewer than 100 titles and genres with fewer than 50 are grouped into "Other" so every coefficient rests on enough data to be stable.
- **Model selection:** partial F-tests, adjusted R², AIC and BIC across five candidate models.
- **Checks:** residual diagnostics, generalized VIF for multicollinearity, and a sensitivity refit excluding the lowest-selling titles.

| Model | Specification | Coefficients | Adj. R² | AIC | BIC |
|---|---|---|---|---|---|
| 1 | Critic score only | 2 | 0.117 | 13,407 | 13,426 |
| 2 | + genre + console (all levels) | 47 | 0.332 | 12,341 | 12,643 |
| 3 | + genre + console (grouped) | 28 | 0.317 | 12,410 | 12,592 |
| 4 | Model 3 with log(critic score) | 28 | 0.300 | 12,509 | 12,691 |
| **5** | **Model 3 + critic score × console** | **41** | **0.332** | **12,337** | **12,601** |

Model 5 is the final model. It matches Model 2's fit with fewer coefficients, and its interaction term is highly significant in a partial F-test against Model 3.

## Key findings

- **Critic score:** a one-point higher score is associated with about 61% higher sales on the reference console (Xbox 360), holding genre constant.
- **The payoff from reviews depends on the platform:** about 56% to 71% more sales per point on Sony and Microsoft home consoles, against 13% to 18% on Nintendo handhelds (GBA, DS, 3DS).
- **Genre:** holding critic score and console constant, Puzzle titles sell about 47% less than Action titles, Strategy about 35% less and Role-Playing about 27% less, while Shooters sell about 16% more.
- **Robustness:** dropping titles that sold 20,000 units or fewer leaves the critic score coefficient essentially unchanged.

The full report covers limitations, including that VGChartz mainly tracks physical sales, which understates PC and other digital-heavy platforms.

## Reproducing the analysis

Requires R with the `tidyverse`, `car`, `GGally` and `rmarkdown` packages.

```r
# Install the packages the report uses (one time)
install.packages(c("tidyverse", "car", "GGally", "rmarkdown"))

# Knit the report from the repository root; vgchartz-2024.csv must be in the same folder
rmarkdown::render("Final_Project_BMGT430.Rmd")
```

## Repository contents

| File | Description |
|---|---|
| `Final_Project_BMGT430.Rmd` | R Markdown source: data cleaning, models, diagnostics and write-up |
| `Final_Project_BMGT430.html` | Rendered report |
| `README.md` | This file |
