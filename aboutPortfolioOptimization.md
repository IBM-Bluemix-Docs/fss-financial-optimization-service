---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-06"

keywords: financial risk, concepts

subcollection: fss-financial-optimization-service


---

<!-- Common attributes used in the template are defined as follows: -->
<!--{:new_window: target="_blank"}-->
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:download: .download}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:faq: data-hd-content-type='faq'}

# About {{site.data.keyword.portfolio_optimization_short}}
{: #about_portfolio_optimization}

The {{site.data.keyword.portfolio_optimization_short}} service helps investment managers understand the optimal trade-offs between risk and reward based on changes in the portfolio. The framework is built upon a flexible mathematical model which allows for solving a wide range of investment problems based on the objectives and constraints.
{:shortdesc}

Some applications of the optimizer for investment management include
* Index Tracking
* Hedging and Pricing
* Asset Allocation
* Portfolio Compression
* Risk Restructuring

In order to setup an optimization problem, you must define a set of expressions:
1. Portfolios - an initial portfolio or universe, benchmarks, and subportfolios
2. Objective - a penalty measure that is minimized or a reward measure that is maximized in the objective function
3. Constraints - a set of inequalities or equalities that impose a restriction on the tradable portfolio for given sets of scenarios

The service translates the expressions into an algebraic form that can be understood by the solver IBM ILOG CPLEX® and applies adjustments to improve solution time numerical stability.

For more details on the optimization methodology, see [Portfolio Optimization Methodology](http://public.dhe.ibm.com/software/analytics/solutions/en/fintech/portfolio_optimization_methodology.pdf).

## Portfolios
{: #portfolio_optimization_portfolios}

A portfolio specifies a list of assets. For each asset in the portfolio, the asset parameter specifies the ID of the asset and the quantity parameter specifies the number of units held. For example, if you hold 100 position units in Coca-Cola, define the asset as follows:
```
    {
        "asset":"CX_US1912161007_NYQ",
        "quantity":100,
        "description":"Coca-Cola"
    },
```
{:codeblock}

There are three types of portfolios:
* A root portfolio defines the set of tradeable assets to be optimized
* Benchmark portfolios define sets of non-tradable assets that are used for relative objectives and constraints
* A subportfolio is a subset of the assets in the root portfolio and/or one of the benchmark portfolios. This subset is grouped by a common characteristic. For example, all assets in the financial sector, all assets which are common stock, or all assets within the growth strategy. Subportfolios are used in setting allocation-based constraints.

### The root portfolio

You must define a single root portfolio.

The following code snippet shows an example of a root portfolio.
```
    {
        "name":"InitialPortfolio",
        "type": "root",
        "description":"In this example the root portfolio consists assets in equities, fixed income and ETFs",
        "holdings":[
            {
                "asset":"CX_US031162BK53_USD",
                "quantity":1231,
                "description":"Amgen 5.15% 11/15/2041"
            },
            {
                "asset":"CX_US03232PAD06_USD",
                "quantity":1374,
                "description":"AmSurg Corp 5.625% 07/15/2022"
            },
            {
                "asset":"CX_US912810EJ35_USD",
                "quantity":1154,
                "description":"US Treasury 05/15/2021"
            },
            {
                "asset":"CX_US1912161007_NYQ",
                "quantity":3198,
                "description":"Coca-Cola"
            },
            {
                "asset":"CX_US4878361082_NYQ",
                "quantity":2156,
                "description":"Kellogg"
            },
            {
                "asset":"CX_US4642872422_NYQ",
                "quantity":0,
                "description":"iShares iBoxx $ Inv Grade Corporate Bond ETF"
            },    
            {
                "asset":"CX_US4642875987_NYQ",
                "quantity":0,
                "description":"iShares Russell 1000 Value ETF"
            }    
        ]
    },
```
{:codeblock}

### Benchmark portfolios

You can define a benchmark portfolio to represent a set of assets that the optimization process uses to compare the root portfolio against. The assets in a benchmark portfolio do not need to be part of the root portfolio.

The following code snippet shows an example of a benchmark portfolio.
```
    {
        "name":"Benchmark",
        "type": "benchmark",
        "description":"A benchmark of 2 ETFs 50% Equity and 50% in Fixed Income",
        "holdings":[
            {
                "asset":"CX_US4642872000_NYQ",
                "quantity":2021,
                "description":"iShares Core S&P 500 ETF"
            },
            {
                "asset":"CX_US4642872422_NYQ",
                "quantity":4125,
                "description":"iShares iBoxx $ Inv Grade Corporate Bond ETF"
            }    
        ]
    }
```
{:codeblock}

### Subportfolios

You can specify as many subportfolios as you need. A subportfolio groups together assets with similar characteristics from either the root or a benchmark portfolio. The value of the parameters for each asset must be the same as that specified in the root or benchmark portfolio.

For example, you can use a subportfolio if you require an allocation constraint. Let's say you want the optimal portfolio to contain a 20% weighting in equities. To accomplish this, you specify a subportfolio that contains the current set of assets that are equities.

The following code snippet shows a subportfolio that contains equities and another subportfolio that contains fixed income assets.
```
    {
        "name":"Equities_Port",
        "type": "subportfolio",
        "ParentPortfolio": "InitialPortfolio",
        "description":"Grouping of the equities assets in the InitialPortfolio",
        "holdings":[
        	{
                "asset":"CX_US1912161007_NYQ",
                "quantity":3198,
                "description":"Coca-Cola"
            },
			{
                "asset":"CX_US4642875987_NYQ",
                "quantity":0,
                "description":"iShares Russell 1000 Value ETF"
            },
            {
                "asset":"CX_US4878361082_NYQ",
                "quantity":2156,
                "description":"Kellogg"
            }
        ]
    },
	{
        "name":"FixedIncome_Port",
        "type": "subportfolio",
        "ParentPortfolio": "InitialPortfolio",
        "description":"Grouping of the fixed income assets in the InitialPortfolio",
        "holdings":[
        	{
                "asset":"CX_US031162BK53_USD",
                "quantity":1231,
                "description":"Amgen 5.15% 11/15/2041"
            },
            {
                "asset":"CX_US03232PAD06_USD",
                "quantity":1374,
                "description":"AmSurg Corp 5.625% 07/15/2022"
            },
            {
                "asset":"CX_US912810EJ35_USD",
                "quantity":1154,
                "description":"US Treasury 05/15/2021"
            },
            {
                "asset":"CX_US4642872422_NYQ",
                "quantity":0,
                "description":"iShares iBoxx $ Inv Grade Corporate Bond ETF"
            }    
        ]
    },

```
{:codeblock}

## Objectives
{: #portfolio_optimization_objectives}

An objective specifies the optimizer will minimize or maximize the function of the attribute specified by the measure parameter. The objective may be set up on an absolute basis or a relative basis. Specify just the root portfolio to set up the objective on an absolute basis. Specify the root portfolio and a benchmark portfolio as a TargetPortfolio to set up the objective on a relative basis.

You can use any one of the following objectives:
* Minimize the variance on the return from the root portfolio
* Minimize tracking error squared, which is the variance of the difference between the root portfolio and benchmark portfolio returns
* Maximize the expected return from the root portfolio

### Example: Minimize variance of the root portfolio return at 30 day time horizon

```
"objectives":[
    {
      "sense": "minimize",
      "measure": "variance",
      "attribute": "return",
      "portfolio": "InitialPortfolio",
      "timestep": 30
    }
]
```
{:codeblock}

### Example: Minimize tracking error squared in 30 days

```
"objectives": [
    {
      "sense": "minimize",
      "measure": "variance",
      "attribute": "return",
      "portfolio": "InitialPortfolio",
      "TargetPortfolio": "Benchmark",
      "timestep": 30
    }
]
```
{:codeblock}

### Example: Maximize expected return of the root portfolio in 30 days

```
"objective": [
    {
      "sense": ""maximize",
      "measure": "expectation",
      "attribute": "return",
      "portfolio": "InitialPortfolio",
      "timestep": 30
    }
]
```
{:codeblock}

## Constraints
{: #portfolio_optimization_constraints}

Constraints specify the relation to impose when optimizing the portfolio. For the construct endpoint, you must specify a constraint on the root portfolio that contains a positive value for CashAdjust. This value represents the amount to be invested into the new portfolio.

You can specify any of the following constraints.
* the expected return of a portfolio at a given time period on either the root portfolio or a subportfolio
* the weight allocation of a particular subportfolio of root portfolio assets. For example, the allocation to fixed income must be greater than or equal to 50% of the optimal portfolio.
* the weight allocation of individual assets. For example, each asset in the fixed income subportfolio must be less than or equal to 5% of the optimal portfolio.
* a maximum number of assets that have an optimal quantity of non-zero. This is known as a cardinality constraint.
* no short-selling. This means that in the optimal portfolio no assets will have a negative quantity.
* an amount to be invested into the portfolio in addition to the current portfolio value

### Example: Expected return of the portfolio is at least 0.75% at a 30 day time horizon

```
"constraints": [
    {
      "measure": "expectation",
      "attribute": "return",
      "portfolio": "InitialPortfolio",
      "timestep": 30,
      "relation": "greater-or-equal",
      "constant": 0.0075
    }
]
```
{:codeblock}

### Example: Weight allocation of Fixed Income in the root portfolio is at most 60%

```
"constraints": [
    {
      "attribute": "weight",
      "portfolio": "FixedIncome_Port",
      "in-portfolio": "InitialPortfolio",
      "relation": "less-or-equal",
      "constant": 0.6
    }
]
```
{:codeblock}

### Example: Weight allocation of each asset in Fixed Income subportfolio does not exceed 5%

```
"constraints": [
    {
        "attribute": "weight",
        "members": "FixedIncome_Port",
        "relation": "less-or-equal",
        "constant": 0.05
    }
]
```
{:codeblock}

### Example: At most 5 positions are non-zero

In order to set a cardinality constraint on the number of non-zero positions, you must do the following:
* create constraints that specify a lower and upper bound of weights of the individual assets in the root portfolio. In the first two constraints in this example, you specify that each individual asset must have a weight between 0% and 100% in the optimal portfolio.
* create a constraint that specifies the number of individual assets that should have non-zero quantities in the optimal solution. In the third constraint in this example, the number of assets that should have non-zero quantities is less than or equal to 5.

```
"constraints": [
    {
        "attribute":"weight",
        "relation":"greater-or-equal",
        "members":"InitialPortfolio",
        "constant":0,
        "description":"Lower bound"
    },
    {
        "attribute":"weight",
        "relation":"less-or-equal",
        "members":"InitialPortfolio",
        "constant":1,
        "description":"Upper bound"
    },
    {
        "attribute": "count",
        "relation": "less-or-equal",
        "constant": 5
    }
]
```
{:codeblock}

Note that constraints on the minimal and the maximal weight for each asset in the root portfolio are required to be specified for the cardinality constraint to work mathematically.

### Example: No short sales
The following constraint specifies that each asset in the root portfolio must have an optimal quantity of at least 0. This constraint prevents negative quantities.

```
"constraints": [
    {
      "attribute": "weight",
      "members": "InitialPortfolio",
      "relation": "greater-or-equal",
      "constant": 0
    }
]
```
{:codeblock}

### Example: A cash inflow of 100000 monetary units to the root portfolio

The following constraint on the root portfolio contains a positive value for CashAdjust. This value represents the amount to be invested into the new portfolio.
```
"constraint": [
    {
      "attribute": "value",
      "portfolio": "InitialPortfolio",
      "CashAdjust": 100000.00
    }
]
```
{:codeblock}
