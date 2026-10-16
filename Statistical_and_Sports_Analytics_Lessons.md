# Statistical and sports analytics lessons by notebook

This guide explains what each notebook teaches beyond sport-specific rules and general pandas operations. It follows the code and questions currently in the six notebooks. The emphasis is on transferable statistical ideas, how they support sports analysis, and the limits of the conclusions they support.

## 1. Hot Hand — Week 6 Assignment

Source: [Week 6 Assignment.ipynb](<Module1_Foundations/Hot Hand/Week 6 Assignment.ipynb>)

The central question is whether recent success contains information about the next outcome, after accounting for a player's usual ability and the difficulty of the attempt.

### Concepts demonstrated

- **Conditional probability.** The notebook estimates the probability of success following a successful attempt. It uses the identity `P(current success | previous success) = P(both successes) / P(previous success)`. This turns a claim about momentum into a measurable conditional relationship. Comparing that probability with a player's overall success rate asks whether performance after success differs from their usual baseline.

- **Serial dependence and autocorrelation.** Correlating the current outcome with the previous outcome tests whether consecutive observations move together. Creating the lag within each player and game defines which observations count as consecutive. This illustrates why time ordering and the boundaries between sequences are part of the statistical model.

- **Differences between players and changes within a player.** Players have different underlying success rates. A pooled association can arise because strong players tend to succeed on both attempts, even if no player's success probability changes after a make. Player-specific probabilities, player-specific correlations, and player indicator variables help distinguish persistent ability from short-term dependence.

- **Performance relative to a baseline.** The variables `error` and `lag_error` subtract each player's average success rate from their current and previous outcomes. They express performance as a departure from expectation. This is a general sports analytics approach: assess whether an athlete exceeded their usual level rather than treating every athlete as having the same starting expectation.

- **Multiple regression and adjustment for context.** The regressions include distance, defender proximity, possession-related variables, and shooter and defender categories. The coefficient on the previous outcome therefore describes an association conditional on these included factors. This matters because apparent momentum may reflect changes in attempt difficulty or defensive responses.

- **Weighting and heterogeneous effects.** The notebook compares ordinary least squares with weighted least squares, using inverse attempts per game as weights, and also fits models for selected individual players. Weighting changes which observations have more influence. Separate player models allow the relationship to differ across athletes rather than imposing one common coefficient.

### What the results can establish

These calculations investigate predictive dependence; they do not establish that a successful attempt causes the next attempt to succeed. A change in behaviour after success could also be part of the mechanism being studied, so controlling for it changes the question being answered.

The hypothesis test needs particular care. The code uses an independent-samples t-test on conditional and baseline probabilities belonging to the same players, after a merge that can repeat player-level probabilities across shot records. Those observations are neither independent groups nor necessarily one observation per player. The resulting p-value is not a clean test of the hot-hand hypothesis. Even a paired analysis would need to consider unequal precision and dependence within games and players. Likewise, inverse attempts per game is a chosen weighting rule; the notebook does not establish that it represents inverse residual variance. The regressions do not explicitly adjust their standard errors for clustering within players or games.

## 2. Regression Analysis — Week 5 Assignment

Source: [Week 5 Assignment.ipynb](<Module1_Foundations/Regression Analysis/Week 5 Assignment.ipynb>)

The central question is how team resources relate to success, and how that relationship changes when previous performance and persistent team differences are included.

### Concepts demonstrated

- **Normalising resources across seasons.** Relative salary divides team spending by total spending in that season. This measures a team's share of the available spending rather than its nominal expenditure. It helps distinguish competitive spending position from changes in the league's overall salary level.

- **Rates and meaningful denominators.** Winning percentage excludes matches with no result from the denominator. This demonstrates that a rate depends on the definition of an eligible opportunity. Teams with different numbers of completed matches can then be compared on a common outcome scale.

- **Simple regression as a conditional mean model.** The initial OLS model relates winning percentage to relative salary. Its slope describes the fitted change in winning percentage associated with a change in salary share. A scatterplot with a fitted line helps assess the relationship visually, while the regression reports its estimated size and uncertainty.

- **Persistence over time.** Adding lagged winning percentage asks whether previous success helps explain current success, and whether relative salary provides information conditional on that history. This connects to forecasting and to the distinction between temporary results and persistent team strength.

- **Fixed effects and within-team comparisons.** Adding `C(team)` allows each team to have its own intercept. This absorbs stable differences between teams that are not explicitly measured. The salary coefficient then uses variation remaining after accounting for those team intercepts and the other included predictor, rather than relying entirely on comparisons between different teams.

- **Model specification and omitted variables.** Comparing the three models shows how an estimated relationship can change when relevant predictors are added. A changing salary coefficient can indicate that the initial model mixed spending with other characteristics associated with winning.

### What the results can establish

The models estimate associations, not the causal return from spending more. Team management, recruitment, injuries, and expected future success can influence both spending and results. Including last season's performance does not resolve all of those explanations.

The models also illustrate limits of adjustment. The first observed season for each team has no lag and drops out of the later regressions, so a change in results can reflect a change in sample as well as a change in specification. A lag created from the previous observed row is the previous calendar season only when the team's records are consecutive. The notebook does not evaluate forecasts on held-out seasons or explicitly account for dependence between repeated observations of the same team.

## 3. Moneyball and Beyond — Week 1 regressions

Source: [Week1_Haukes_Sauer_regressions.ipynb](Module2_Moneyball_and_Beyond/Week1_Haukes_Sauer_regressions.ipynb)

The central question is how a team's performance and its opponents' performance relate to season-level success, and whether those two relationships can be represented by a single performance difference.

### Concepts demonstrated

- **Choosing the unit of analysis.** The models use one team-season as an observation, built from individual games. Their coefficients describe relationships between season-level team characteristics and season-level success. They do not directly describe the effect of a particular player or action.

- **Opportunity-weighted rates.** The notebook calculates performance rates from summed numerators and denominators. A ratio of totals gives each opportunity equal weight. It generally differs from a simple average of game-level rates, which would give a short game and a long game equal influence.

- **Performance relative to opposition.** Including both performance for and performance against recognises that success depends on what a team produces and what it allows opponents to produce. This is a general foundation for separating attacking and defensive contributions.

- **Context contrasts.** Comparing home and away winning percentages constructs a descriptive difference within each team-season. It demonstrates how the same unit can perform differently across contexts, even when its overall season rate is identical to another team's.

- **Restricted versus unrestricted regression.** The unrestricted model is `winning percentage = intercept + b1 × performance for + b2 × performance against`. The difference model is `winning percentage = intercept + b × (performance for − performance against)`. Expanding the second model shows that it imposes `b1 = b` and `b2 = −b`: the coefficients must be equal in magnitude and opposite in sign.

- **Parsimony and substantive assumptions.** The difference model uses fewer parameters and gives a compact measure of net performance. That simplicity comes with a testable assumption about symmetry. A simpler model is useful when its restriction is a reasonable description of the data.

### What the results can establish

Fitting both models makes the restriction visible, but the notebook does not formally test whether imposing it worsens fit. A nested-model F-test would address that question under the relevant assumptions; performance on held-out seasons would address predictive usefulness.

The home-versus-away difference is descriptive, rather than a causal estimate of home advantage: opponent mix and other circumstances may differ. Team observations are also connected through shared games, and season-level associations should not automatically be transferred to individual players.

## 4. Moneyball and Beyond — Week 2 regressions

Source: [Week2_Haukes_Sauer_regressions_P2.ipynb](Module2_Moneyball_and_Beyond/Week2_Haukes_Sauer_regressions_P2.ipynb)

The central question is how measured skills, playing opportunities, roles, and employment status relate to pay, and whether those relationships change across periods.

### Concepts demonstrated

- **Timing explanatory variables before outcomes.** Performance in one season is matched with salary in the following year. This establishes a useful temporal ordering for studying how past performance relates to subsequent rewards. It also illustrates why dates must match the question being asked.

- **Logarithmic outcomes.** Regressing log salary models proportional salary differences. For a predictor change of `d`, a coefficient `b` corresponds to a fitted salary ratio of `exp(b × d)`, holding the other predictors fixed. The approximate percentage difference is `100 × b × d` when the log change is small. A one-percentage-point change in a rate recorded from 0 to 1 means `d = 0.01`, not `d = 1`.

- **Conditional valuation of skills.** Including several skill measures together estimates the salary association of each conditional on the others. This is different from comparing salaries across players with different levels of one skill alone. Correlation between skill measures can make individual coefficients unstable or imprecise.

- **Effectiveness and opportunity.** Skill rates and the number of appearances enter the model separately. This distinguishes how effective a player was per opportunity from how much they participated. It is a recurring distinction in sports analysis and labour-market analysis.

- **Institutional and role differences.** Eligibility indicators approximate different bargaining situations, while positional indicators represent role differences. These variables demonstrate that compensation depends on the conditions under which skills are sold as well as the skills themselves. Indicator coefficients are interpreted relative to the omitted category, conditional on the other predictors.

- **Comparing relationships across time.** Regressions for different periods and individual seasons ask whether the associations between skills and pay remain stable. The broader idea is that the relationship between performance and market rewards can change as information, incentives, or market conditions change.

### What the results can establish

Salary associations do not by themselves prove that a skill was underpriced relative to its contribution to winning. That conclusion would require connecting performance value, compensation, and a relevant alternative investment.

Separate regressions also do not provide a formal test that a coefficient changed: significance in one period and insignificance in another is insufficient. The experience-based eligibility variables are proxies rather than exact measures of contractual status. The sample is restricted by participation and salary availability. Although separate positional indicators are created, the fitted formulas use `catcher` and a combined `Infld` indicator, so they do not estimate separate effects for every position. The pooled period models also omit season indicators and an explicit inflation adjustment.

## 5. Moneyball and Beyond — Week 3 update

Source: [Week3_Moneyball_Update.ipynb](Module2_Moneyball_and_Beyond/Week3_Moneyball_Update.ipynb)

The central question is how the salary associations of distinct skill components evolve over time, and how to represent differences between periods in one regression.

### Concepts demonstrated

- **Decomposing broad measures into components.** The notebook separates general hitting success, additional power, and reaching opportunities through other means using `BA`, `ISO`, and `Eye`. This demonstrates measurement design: a broad outcome can be broken into components that represent different capabilities. The components still need not be statistically independent.

- **Nonlinear relationships.** Including experience and experience squared allows the relationship with log salary to bend. Its slope at a given experience level is `b_experience + 2 × b_experience_squared × experience`. The model can represent diminishing returns or an eventual decline, but the estimated signs determine the shape; adding a squared term does not guarantee either pattern.

- **Role adjustment.** `C(POS)` gives positions separate intercepts. The skill coefficients therefore describe associations after allowing for average salary differences between roles. This is one way to make comparisons more relevant when different roles are rewarded differently.

- **Changing coefficients across seasons.** Fitting a model for each salary year allows the estimated relationship between a skill and pay to vary. Comparing these estimates highlights the distinction between a changing average salary and a changing association between salary and a particular capability.

- **Interactions and structural change.** The pooled model interacts a pre-period indicator with the predictors. Because `PreMB = 1` before 2004, the ordinary predictor coefficients describe the later period. Earlier-period coefficients are the ordinary coefficients plus their corresponding interaction coefficients. An interaction directly represents a difference between periods.

- **Statistical significance versus substantive importance.** Questions about large coefficients and the frequency of significance invite two different assessments: how large an estimated association is, and how precisely it is estimated. Coefficients also depend on predictor units, so the largest raw coefficient across differently scaled variables is not automatically the most important predictor.

### What the results can establish

The interaction model provides a framework for testing period differences, but the notebook does not explicitly run a joint test of all interactions. Counting significance stars across years is not a test of whether coefficients differ across years, and repeated searches for significant results create a multiple-comparison problem.

The estimates apply to the free-agent sample. They do not automatically generalise to all players or establish that a historical publication caused a change in pay. Pooled nominal salary models can also mix period differences with changes in the overall salary level, sample composition, and omitted characteristics. A quadratic turning point, if calculated, should only be interpreted where the data provide reasonable support.

## 6. Moneyball and Beyond — Week 4 run expectancy

Source: [Week4_Run_Expectancy.ipynb](Module2_Moneyball_and_Beyond/Week4_Run_Expectancy.ipynb)

The central question is how to value an action using the situation before and after it, including both its immediate reward and the opportunities it creates.

### Concepts demonstrated

- **Conditional expectation.** Historical future outcomes are averaged separately for each state. This estimates expected remaining reward given the current situation, rather than one overall average. It is a practical example of a conditional mean estimated without fitting a regression.

- **Value added through a state transition.** The action-value calculation is `immediate reward + expected future reward after − expected future reward before`. It credits an action that creates future opportunities even when there is no immediate score. The same structure can support evaluating possession changes, passes, and other actions in different sports.

- **Terminal conditions.** A completed scoring opportunity has zero expected remaining reward. Adding those states explicitly gives the valuation system a meaningful boundary condition and allows actions that end the opportunity to be valued consistently.

- **Rate versus accumulated contribution.** Mean run value by event describes the typical contribution of that event in the observed contexts. Summed run value by player describes accumulated contribution. A larger player total can reflect greater effectiveness, more playing time, or both. Position-level totals similarly depend on the number of opportunities represented in each group.

- **Comparisons across seasons.** Signed differences measure improvement or decline; absolute differences measure the size of change in either direction. Restricting player comparisons to athletes present in both years creates a consistent set of identities, while separate seasonal expectancy tables let each year's actions be valued against that year's observed environment.

- **Selection and sampling uncertainty.** Excluding incomplete scoring periods improves consistency but changes the population being analysed. Keeping only players observed in both seasons creates further survivor selection. Rare event averages are less stable, and selecting the largest change across many events or athletes can select an unusually noisy estimate.

### What the results can establish

The state values provide a useful accounting system; they do not isolate an individual's causal contribution from teammates, opponents, or omitted context. An event's seasonal mean can change because it occurred in different situations, even if its value within each situation remained similar. Player changes also combine differences in opportunity, performance, context, and the season-specific valuation scale.

The denominator must match the question. The notebook filters play-by-play event rows, which can include events other than completed plate appearances. A percentage calculated from all retained rows describes those retained events; a strict plate-appearance percentage would require selecting the appropriate records.

The notebook does not estimate confidence intervals, test the statistical significance of seasonal differences, or evaluate state values on unseen games. Those are useful extensions when the goal moves from describing the sample to estimating stable ability or predicting future performance.
