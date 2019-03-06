---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-06"

keywords: financial risk, troubleshooting

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

# Troubleshooting
{: #ts}

The Portfolio Optimization service may return a value of 400 in the httpcode of the response. This status code indicates that the server cannot or will not process the request due to an apparent client error. When the value of httpcode is 400, the description in the response contains the reason for the error code.
{:shortdesc}

## "description": "Infeasible"

An infeasible problem is a problem that has no solution.
{: tsSymptoms}

Problems are generally infeasible because the constraints are too rigorous, which means there is no portfolio that satisfies all of the constraints.
{: tsCauses}

To obtain an optimal solution, remove, replace, or change the constraints.
{: tsResolve}

## "description": "Unbounded" or "description": "Infeasible or unbounded"

An unbounded problem is a problem that has no optimal solution. When a problem is reported to be "Infeasible or unbounded", you should treat is as being unbounded.
{: tsSymptoms}

The constraints do not restrict the objective enough to provide an optimal solution.
{: tsCauses}

To obtain an optimal solution, add constraints to the problem.
{: tsResolve}

## "description": "XML request error: Did not find a position defined for a childId tag within positionGroup tag. Last reported engine status: PCO started."

The optimizer cannot formulate a problem definition to be sent to the solver.
{: tsSymptoms}

This happens when an asset ID in the portfolio or benchmark is specified incorrectly or does not exist.
{: tsCauses}

Refer to the [Portfolio Optimization Service Sample Data Set](http://public.dhe.ibm.com/software/analytics/solutions/en/fintech/portfolio_optimization_service_sample_dataset.xlsx) spreadsheet for valid asset ID’s and check that all asset ID’s are formatted correctly.
{: tsResolve}
