Methodology: pdf file

Practice: python files

Order for the python notebook:

1. Data preparation

2. Userdefined_functions

3. PD model
   
5. 02_LGD, EAD, EL


Goal of the project: Build a credit risk model and provide a scorecard to use in bank daily procedures and calculate exposure loss.

Terms:

PD: probability of default in logistic regression
Probability of default means the likelihood that a borrower will default on debt (credit card, mortgage or non-mortgage loan) over a one-year period (Basel).

EAD: exposure at default in beta regression model
The amount to be exposure in the case of default. It is the amount that the borrower has to pay the bank at the time of default.

LGD: Loss given default in beta regression model
The percentage of exposure a lender loses when a borrower defaults, after accounting for recovery from collateral or restructuring. It is calculated as LGD = EAD * (1 - RR) where RR is recovery rate.

After building PD model, use it to develop scorecards that simplify loan approval decisions, making them more practical for bank officers to use than the underlying statistical model.

Main steps

1. Data Preparation processing - Converting columns into dummy variables by fine and coarse classing

2. Develop PD model with logistic regression, turn it into scorecard (in csv) and validate it

3. Model monitor by Population Stability Index (PSI)

4. Construct LGD model with logistic regression and linear regression

5. Build EAD model with linear regression

6. Calculate the exposure loss after obtaining all models

Internal Ratings-Based (IRB) approaches under Basel regulations

- Foundation IRB : PD is estimated by the bank while LGD and EAD are prescribed by regulator.
- Advanced IRB : PD, LGD, and EAD can be estimated internally by the bank.

Corporate portfolio: PD, LGD, EAD, CCF, MA
Retail portfolio: PD, LGD, EAD
A-card vs B-card For credit officier who is not statistical expert, they will use score card which is simplified by PD model.

Application scorecard (A-card): To decide whether to grant a loan or not. What interest rate to apply (higher the risk higher the interest)

Behaviour scorecard (B-card): To decide whether to grant additional loans to existing borrower

Dataset

Dataset contains more than 800,000 consumer loans issued from 2007 to 2015 by Lending Club. Available on kaggle https://www.kaggle.com/datasets/devanshi23/loan-data-2007-2014/data

We divided the data into two periods because we assume that some data are available at the moment when we need to build Expected Loss models, and some data comes from applications after. Data is divided into two periods: 1st part: data from 2007 to 2014 and 2nd part: data in 2015.

Data

Retail PD model:

Individual: demographic info (age, income, edu,..), charateristic of product (purpose, length..) external info (credit score..)..
Credit card: credit limit utilization (percentage spent upto credit limit)
Mortgage:..
Corp: firm size, #yr operate, target market, financial statement info: ROA, ROE, current ratio..

NOTEBOOK DATA PREPARATION

I. DATA PREPARATION

I.2 GENERAL PROCESSING
CONTINOUS VARIABLES and DISCRETE VARIABLES

I.3 CHECK MISSING VALUES N CLEAN

II. PD MODEL. PREPRATION
II.1. DEPENDENT VARIABLES. GOOD BAD DEFINITION. DEFAUL N NON-DEFAULT ACCOUNT

Default definition: overdue > 90 days

Y: (for PD model) loan status. Y = 1 is good and Y = 0 is bad customer

Y = 1 Fully paid off

Y = 0: charge off, default, not meet credit policy, late 31 - 120 days..

For PD: all Xs are categorical (because it is easier to present model in a simplified form and turn it into scorecard) -> must transform Xs into categories or dummy variables. Here we use dummy variables.

+ discrete: 1 category = 1 dummy variable
+ continous: use fineclassing (divide into equal size interval then examnie how well each interval discriminates good/bad customers by WOE) and coarseclassing (merge categories with similar WOE value)

For LGD and EAD: (at default time) choose only defaulted customers for the dataset

Y_LGD: Recoveries

Y_EAD: CCF (credit conversion factor) 

X123..: the same

II.2. SPLIT DATA: TRAIN N TEST DATA
II.3 CAL WOE N IV FOR INDEPENDENT VARIABLES
WOE shows discriminatory power of category or of variable Xi. WOE is far from 0 is good
IV tells predictive power of Xi. IV from 0.1-0.3: medium, >0.3: strong, >0.5: suspiciously high

II.4 EXPORT (TRAIN AND TEST) DATA TO CSV 



NOTEBOOK 01_PD MODEL

Probability of default is the likelihood that a borrower will fail to repay the loan (credit card, mortgage or non-mortgage loan) over a one-year period

Contents

- Build PD model with Logistic regression
- Model evaluation: Accuracy, AUROC, Gini, KS
- Decision making: From PD to Scorecard
- Set Cutoffs
- Monitor model with Population stability index (PSI)

Detail

I. Build PD model with Logistic regression

Remove the reference categories (only neeed n-1 dummies for n categories)

Build LogReg model with p-values

Check statistically significant of coef. of dummy variables (p<alpha here p< 0.05 is good) We only remove a variable Xi if all dummy variables are not sta. sign. (meaning that if only one dummy variable of the original Xi is significant, we will keep that original variable Xi or keep all other dummies of that original variable)

Fit LogReg again

Interpretation: higher coef = lower PD (greater odds of being good borrower)

II. Model evaluation: Accuracy, AUROC, Gini, KS

In this notebook we consider:

Accuracy (not a good metric since data is imblanced: proportion of default customer is much smaller than non-default customer)

AUROC: area under ROC curve. Fair: 70 -80%, Good: 80-90%, Excellent: >90%

Gini: measure inequality between good and bad borrowers

KS: max distance between cdf of good and bad customers

We can also consider others:

Lift: Assess the relative performance of the model at a different cut-off points.

Hosmer Lemeshow Test : Test to determine if the model is well calibrated to the actual risk in the portfolio. (The null hypothesis for the test is that the distributions are the same. The test outputs a P-value that is rejected if it is below a specified level of significance. )

III. Decision making: From PD to Scorecard

It is a requirement imposed by the Basel Accord.

The score range of FICO is from 300 to 850. We will normalize the coefficient of regression result to get the score in that given range.

IV. Set Cutoffs

Cut-off point is the minimum thresold score to decide whether to accept or reject loans.

Borrowers with a credit score higher than the set cut-off point will be accepted for loans and those less than the cutoff point will be rejected.

V. Monitor model with Population stability index (PSI)

Because population changes overtime so we need model maintainance i.e: every 6 mths - 1yr or redevelop after getting 50k - 100k new data points.

Index for the different of popoulation overtime (different between trained -past data and new data - current data): PSI - opulation stability Index.

PSI belong to range [0,1], rebuild model if:

0.1 - 0.25 : little (probably no action needed)
0.25 - 1: big change (action needed)


NOTEBOOK 02. LGD, EAD, EL

I. LGD model

Stage 1: Logistic regression, to check if recovery rate (RR) is 0 or not

Prepare inputs
Test model
Estimate accuracy, use ROC..
Stage 2: Multiple linear regression, to estimate recovery rate

LR
Estimate
Evaluate; how well regression perform by or (1) calculate corr (actual, predicted value). If that value is too high -> question our methodology; (2) distribution of the difference btw actual vs predicted values. The residual should be close to normal dist.
Combine 2 stages: multiply predicted values from 2 stages

II. EAD model

Estimate: Use LR to investigate dist of CCF.
Interpret: (+) coef: same direction with CCF; (-) coef: negative direction with CCF

i.e: if coef of IR feature in the regression = -0.0117, then 1% increase in IR -> expect CCF decreases by 1.17%

Validate: evaluate how well moel predict CCF, use:
(1) corr method: corr (actual, predicted) > 0.35: moderate strong (2) dist of residual: should close to normal dist

III. Expected loss EL

EL = PD x EAD x LGD

EL: we consider loss of all customers:

we calculate: % (total EL/ total funded)
