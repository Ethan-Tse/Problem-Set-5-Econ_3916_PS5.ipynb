# Problem-Set-5-Econ_3916_PS5.ipynb
# Right 93% of the Time: Evaluating a Credit Default Model

## Objective
I tested a vendor's "93% accuracy" credit-default model against a model that approves everyone, and used the cost of each kind of mistake to choose the threshold at which the model should decline an application.

## Methodology
- Simulated 10,000 loan applications at a 7% default rate, with five real features and six with no relationship to default, and split off a test set before looking at anything. Every number reported after that comes from the test set.
- Used the null model (approve everyone) as the floor.
- Fitted logistic regression, LASSO and Ridge with the penalty chosen by 5-fold cross-validation, and a random forest scored out of bag. Compared its built-in (MDI) importances with permutation importance.
- Reported the confusion matrix, precision, recall, F1, ROC AUC and precision-recall AUC at 7% prevalence.
- Built the expected-cost curve for every threshold from 0 to 1, with a missed default costing 60% of principal and a declined good applicant 10%, and varied the cost ratio.
- Tested the forest against the logistic model with McNemar's test, and checked the model's calibration with a reliability diagram, the Brier score, Platt scaling and isotonic regression.

## Key Findings
- Approving everyone scores an accuracy of 0.9303 on the test set. The penalised logistic model scores 0.9460, so the vendor's 93% proves nothing on its own.
- LASSO with the one-standard-error rule set all six irrelevant features to zero and discarded none of the five real ones. At the cross-validation minimum it zeroed none.
- The forest does not beat the logistic model: McNemar's test gives p = 0.70 on 60 discordant rows.
- The cost-minimising threshold is 0.201, against the default of 0.5. It costs $328 per application, against $481 at 0.5 and $791 for approving everyone.
- The model is well calibrated overall, and neither Platt scaling nor isotonic regression improves it. On calibrated probabilities the threshold is 0.131.

## Recommendation to the head of risk

**To the head of risk: sign, but not on the vendor's number.** Approving every applicant scores 0.930 accuracy on our test set, so a "93%" proves nothing. The model scores 0.946, a gain of under two points that hides what matters, which is money.

**Worth against the null.** Approving everyone costs $791 per application in losses; the model at the default 0.5 cutoff costs $481; at the cutoff I recommend it costs $328, 58% below the null. It declines 330 of 3,000, catching 144 of 209 defaulters and declining 186 good applicants.

**Threshold and assumptions.** Decline when predicted default probability is at least **0.20**. Assumptions: a default loses 60% of principal; a declined good applicant costs 10% (8% margin plus 2% reputation), a 6:1 ratio. The rule is to decline above 1 / (1 + ratio). The cost curve is flat: 0.14 costs $346, so anything from 0.14 to 0.20 is defensible.

**Do not pay extra for the forest:** it ties the simple model (McNemar p = 0.70).

**What would change my answer:** a production default rate far from 7%. The probabilities, and the cutoff, are only as good as that match; check calibration on the first three months of live loans.
