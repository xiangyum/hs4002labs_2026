# Review of Lab Notebooks, Weeks 1–6

This review covers the lab notebooks from before the current week (`week8`):

| Week | Notebook | Data | Saved outputs? |
|---|---|---|---|
| 1 | `week1/week1_notebook.ipynb` | `week1/lib_toy.csv` | No (never run, or outputs cleared) |
| 2 | `week2/week2_notebook_student.ipynb` | `week2/gss2022_mini.csv` | No |
| 3 | `week3/week3_notebook_student.ipynb` | `week3/gss2022_mini.csv` | Yes (run out of order) |
| 4 | `week4/week4_notebook_student.ipynb` | `week4/gss2022_mini.csv` | No |
| 5 | `week5/week5_notebook_student.ipynb` | `week5/gss2022_mini.csv` | Yes, with the student's own interpretation comments |
| 6 | `week6/week6_notebook_student.ipynb` | `week6/gss2022_mini.csv` | No |

How I checked: I read every cell and compared the variable codings with the data. The five `gss2022_mini.csv` copies are byte-identical (3,544 rows, 49 columns). I ran weeks 1, 4 and 6 on copies in a temporary folder outside the repo to see whether they work. No notebook or data file was changed.

---

## Things that affect several notebooks

1. **Using only complete cases on the arts questions throws away most of the sample (weeks 3–6).** Each notebook drops every row with a missing value in *any* chosen column. That includes `yrlvmus`, `yrartxbt`, `yrmovie` and `yrcreat`, which only 826 of the 3,544 respondents answered (they were asked of only part of the sample). Every analysis therefore uses about 707–728 people, even ones that never use the arts variables, such as age vs. income in week 4, the income ~ prestige models in week 5, and the age/income/gender models in week 6. For example, `age`, `income16` and `prestg10` together have 2,850 complete cases, but the week 4–6 models use 707. Results could change, and the respondents who were asked the arts questions may differ from everyone else.
2. **`income16` is total family income, not the respondent's own income.** Week 3 calls it "Household Income", which is correct. Weeks 4–6 call it "Annual Income ($)" or "Income (USD)" and regress it on the respondent's own occupational prestige, gender and age. The data includes `rincom16` (the respondent's own income), which would fit "does job prestige predict income?" better. If family income is what you want, the labels should say so.
3. **The income recode depends on an arbitrary top value.** The `income_map` midpoints match the GSS brackets, but the top bracket (26, "$170,000 or over") is set to 250,000. That is the most common category among complete cases (330 of them in week 3), so this one guess has a big effect on the means, the skewness and the OLS coefficients. It is worth mentioning, or testing other values (e.g. 1.5× the lower bound).
4. **`age` is top-coded at 89** (19 respondents). This barely matters here, but it applies to every model that uses age.
5. **The same setup code is copied into weeks 4, 5 and 6.** The copies match, so this isn't a bug, but a fix in one copy won't reach the others.
6. **Two different environments.** Weeks 3 and 5 were last run with the "Python (hs4002)" kernel (Python 3.14, `../.venv`). The others record a `.venv` kernel on Python 3.13. The repo also contains a separate `venv/` folder whose Python has no pandas installed. Make sure everyone uses the same environment.

---

## Week 1: Love Is Blind toy data

**What it does:** Loads a 120-row toy dataset of *Love Is Blind* contestants and shows pandas and plotnine basics: `head`, `describe`, `value_counts`, a histogram and density plots of age, then mean age and density plots split by marriage outcome, faceted by race and then by gender. Plots are saved as PNGs.

**Variables:** `Age` (24–39), `Married` (0/1; 32 married), `Race` (White/Black/Hispanic/Asian/Mixed), `Gender` (0/1, recoded to `'Woman'` where 1 and `'Man'` otherwise), `Season`. Also present but unused: `Name`, `Partner`, and a junk column `Unnamed: 7` (e.g. "LIB + Lauren Speed").

**Things that look off:**
- **Cells 15 and 16 both save to `ntu4eva.png`**, so the density plot overwrites the histogram. They need different file names.
- **`binwidth=0.5` on whole-number ages** gives a histogram with gaps between the bars. `binwidth=1` would fit the data.
- **The gender recode in cell 24 breaks if run twice.** After the first run, `Gender` holds strings, so `df['Gender'] == 1` is always false and everyone becomes `'Man'`. A new column (e.g. `gender_label`) would avoid this.
- The research question asks about the *likelihood of marriage*, but the analysis only compares age distributions by marriage status. It never shows marriage rates by age, race or gender. Faceting by race also leaves very small groups (6 Asian, 2 Mixed), so those density curves mean little.
- `Unnamed: 7` comes from a trailing column in the CSV. It's harmless here but shows up in `head()`.
- All plots use `xlim(18, 40)`, but the youngest contestant is 24, so the left part of every plot is empty.

## Week 2: Python basics and the first GSS table

**What it does:** Covers Python basics (variables, `if/elif/else`, functions, objects and methods). Then it selects five GSS variables, drops missing values, takes a random sample of 500, saves it to CSV, recodes work status and gender, computes mean occupational prestige for working men and women, exports that table to CSV and LaTeX, and draws an age × prestige scatterplot.

**Variables:** `id`, `wrkstat` (1–8; 1–3 recoded as "Working"), `prestg10`, `age`, `sex` (2 recoded as "Woman"). Derived: `working`, `sex_woman`, `sex_str`.

**Things that look off.** This is a fill-in-the-blanks student copy, so most of these are unfinished exercises rather than bugs. As saved, though, the notebook won't run:
- **Cell 6 has a syntax error** (`if respondent_age <  :`, with the thresholds left blank).
- **Cell 8 calls `categorize_age()` with no argument**, which raises a `TypeError`. The instructions say to apply it "to every age in `ages`", but `ages` is never defined, and the loop exercise is missing (the intro mentions lists, dictionaries and loops, but there are no sections for them).
- **Cell 4** prints only `name`, but the task asks for all three variables and their types.
- **Cells 12, 14 and 16 are empty**, so `df` is never loaded and every later cell fails with a `NameError`.
- **`chosen_variables = []` in cell 18** is still empty. Even with `df` loaded, `df2` would have no columns and `sample(n=500)` would fail.
- Cell 28 saves `df_mini` **before** `working` and `sex_woman` are created, so the "cleaned" CSV doesn't include them. That may be intended, but it's easy to miss.
- `wrkstat <= 3` counts code 3 ("with a job but not at work") as working. That matches the instructions, so it's just a note.
- The week 2 dropna uses only five columns, so it keeps far more rows (3,145) than the later weeks do. Sample sizes aren't comparable between weeks.

## Week 3: Sampling, distributions, skewness and kurtosis

**What it does:** Builds binary arts-participation flags and a 0–4 "omnivorousness" index, turns income brackets into dollar values, draws samples of 30, 100 and 500, compares proportions across them, plots a movie-attendance bar chart and an income histogram, standardises income and prestige, and computes skewness and kurtosis.

**Variables:** `id`, `yrlvmus`, `yrartxbt`, `yrmovie`, `yrcreat` (1 = yes, 2 = no), `income16`, `prestg10`. Derived: `binary_lvmus`, `binary_artxbt`, `binary_movie`, `binary_creat`, `income_cont`, `omni`, `scaled_income`, `scaled_prestg`. N = 728 after dropna.

**Things that look off:**
- **The heading and code don't match (cells 21–22).** The heading says "histogram of the omnivorousness index", but the code plots `income_cont` and saves `hist_income.png`. Either the heading or the code is wrong, and the omnivorousness histogram is never made.
- **Cell 26 still has the placeholder `'CORRECT_COLUMN_NAME'`**, and the cell neither shows nor saves `p`. Because plotnine only builds a plot when it's displayed, the cell "ran" (execution count 56) without an error, which hides the problem. The title "Household Income" and x-label "Normalized Values" suggest it should use `scaled_income`. The heading says "each variable", but prestige is never plotted.
- **Skewness and kurtosis are only computed for income**, though the headings say "each variable". `scaled_prestg` is created and never used.
- **Standardising doesn't change skewness or kurtosis.** Both are unaffected by shifting and rescaling, so running them on `scaled_income` gives the same result as on `income_cont`. It isn't wrong, but the notebook implies the scaling matters.
- **"Full GSS" is the wrong label** for the 728 complete cases, not the full 3,544-row file.
- **The three samples come from the same data with the same `random_state=47`,** so they overlap heavily rather than being independent. Also, `np.random.seed(47)` does nothing when `random_state` is passed.
- **Cells were run out of order** (execution counts 50, 54, 52, 56, 57, 58). The saved outputs may not match a fresh top-to-bottom run.
- The income histogram uses `binwidth=5000` on 26 fixed bracket values, so it shows tall separate spikes, with a big one at the made-up 250,000.
- Small issues: the code mixes tabs and spaces, "medium:" is lowercase while "Small:" and "Large:" are capitalised, the last cell is empty, and a comment mentions "the R version".

## Week 4: Two-variable relationships

**What it does:** Looks at three relationships. (1) Age vs. income: covariance, a scatterplot with a linear fit, and Pearson's r. (2) Omnivorousness vs. income: a boxplot and Spearman's ρ. (3) Race vs. degree: stacked and faceted bar charts, then chi-square and Cramér's V.

**Variables:** `id`, `age`, `yrlvmus`, `yrartxbt`, `yrmovie`, `yrcreat`, `income16`, `prestg10`, `degree` (0–4), `race` (1–3). Derived: `income_cont`, the `binary_*` flags, `omni`, `race_recoded` (1 = White, 2 = Black, otherwise Other), `degree_recoded` ("1:<HS" … "5:Grad."). N = 707.

**Things that look off:**
- **Chi-square and Cramér's V are never shown.** Cell 20 saves `chi2_result` and `cramerv_result` and stops there, so the notebook ends without the statistic, p-value or effect size the section is about.
- **Income is labelled as the respondent's own annual income** but is family income (see the first section).
- When I ran it, age vs. income gave Pearson r ≈ 0.006 (p = 0.88). Income is probably not linear in age (it tends to rise and then fall; week 6 adds age² for this reason), and the 707-person subsample makes this weaker still. That's worth saying when interpreting the result.
- Cell 5 prints the covariance and then shows the full 2×2 matrix, which repeats the number and adds two variances that aren't explained.
- The stacked bar chart (cell 17) shows counts. White respondents are the large majority, so the chart mostly shows group size. Proportions within each race (`position='fill'`) would show the association the section is testing.
- `.fillna('Other')` in `race_recoded` works by turning code 3 into NaN and then filling it. That works, but `{3: 'Other'}` would be clearer. Missing race values were already dropped, so they aren't being labelled "Other".
- `prestg10` is selected but never used.
- When I ran the notebook on a copy, it ran with no errors.

## Week 5: OLS regression

**What it does:** Fits three nested OLS models of income (`income_cont ~ prestg10`, then `+ sex_woman`, then `+ age`). It compares them with R², adjusted R², AIC and F-tests (`anova_lm`) and makes a stargazer LaTeX table. The bonus model `lm_w` adds `degree`.

**Variables:** `id`, `age`, `yrlvmus`, `yrartxbt`, `yrmovie`, `yrcreat`, `income16`, `prestg10`, `degree`, `race`, `sex`, `wrkstat`. Derived: `income_cont`, `binary_*`, `omni`, `race_bin`, `ba_binary`, `sex_woman`, `working`. Only `income_cont`, `prestg10`, `sex_woman`, `age` and `degree` are used in the models. N = 707.

**Things that look off.** The code runs and the outputs are saved. Most problems are in the interpretation comments:
- **Cell 9 misreads the intercept:** "estimated intercept is -2.001..". The output says `-2.001e+04`, which is about **−$20,010**, not −2.
- **Cell 9 says "AIC is pretty small"** (it's about 17,689). The size of an AIC means nothing on its own, as cell 18 correctly says later. The comment also mixes up prestige's p-value with "0.000 chance" of the null being true.
- **Cell 12 misreads the p-value:** "P value is 0.034 is how likely it is that the true coefficient for gender is 0". A p-value is the chance of data at least this extreme *if* the coefficient were 0. It is not the chance that the coefficient is 0.
- **Cell 35 gets a number wrong and uses the wrong term.** It says the gender gap grew from "−10,067" in Model 3, but Model 3's coefficient is **−10,669**. It also calls the drop in the prestige coefficient (2,234 → 1,267) a fall in "bias". The coefficient shrank once education was controlled, which *suggests* Model 3 had omitted-variable bias, but the figures are coefficients, not bias.
- **Cell 23 ("Which model do you prefer?") is still `# Your answer here`.** Cell 21's comment ("assessment for how likely it is that additional variables are gender") is unclear and should mention the null hypothesis (that the gender coefficient is 0) and the result (F = 4.50, p = 0.034).
- **`degree` goes into `lm_w` as a single 0–4 number.** That assumes each step up (e.g. <HS→HS and BA→Grad) is worth the same amount of income. `C(degree)` or `ba_binary` would avoid the assumption, and it should at least be mentioned.
- Model 3 has a slightly lower adjusted R² and a higher AIC than Model 2, and age is not significant (F p = 0.35). The comparison table in cell 27 shows this, but no comment draws the conclusion.
- The Overleaf markdown (cell 29) says "the block the cell above printed", but the stargazer cell comes *after* it.
- `import pandas as pd` is repeated in cell 36, `anova_lm` is imported again in cell 37, and the last cell is empty.

## Week 6: Polynomials, interactions, LPM, logit and marginal effects

**What it does:** Regresses income on age and age², adds BA and gender and then a BA × gender interaction, models live-music attendance with a linear probability model and two logit models, compares the logits with AIC and a likelihood-ratio test, and computes the average marginal effect and the marginal effect at the mean of a BA using `marginaleffects`. Tables are made with stargazer.

**Variables:** The same setup and derived variables as week 5. Used in models: `income_cont`, `age`, `I(age**2)`, `ba_binary` (`degree >= 3`, i.e. BA *or higher*), `sex_woman`, `binary_lvmus`, `np.log(income_cont)`. N = 707.

**Things that look off.** No outputs are saved. When I ran it on a copy, every cell worked, including all three stargazer tables and both `marginaleffects` calls.
- **The interpretation in cell 29 still says "X percentage points".** The run gave an average marginal effect of about **0.124** (12.4 points, 95% CI 0.044–0.204) and about 0.126 at the mean.
- **The marginal-effect wording says "holding other variables constant".** For an *average* marginal effect, it's more accurate to say "averaged over the sample's observed values of the other covariates". That wording fits the at-the-mean version better.
- **`ba_binary` is labelled "BA degree"** in tables and text but also includes graduate degrees. "BA or higher" would be accurate.
- `ols_model4` (the interaction) is only shown in the stargazer table. There is no `.summary()` or comment, and no section explains how to read the BA × Woman term.
- The age² models still use the 707-person complete-case sample (see the first section), which weakens the test of the age curve. Using all available rows would help.
- The LPM and logit models share one stargazer table with one "Dependent variable" header. A comment warns not to compare magnitudes, which is good, but readers of the exported table won't see it, so a table note would help.
- For reference, from my run: logit AIC 933.58 → 924.49 and LR = 15.09 on 3 df (p = 0.0017), both favouring `logit_model2`. The notebook has no written conclusion for this comparison.
- `anova_lm` is imported but never used.
