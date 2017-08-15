---

copyright:
  years: 2017
lastupdated: "2017-08-15"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# Getting started tutorial
In this getting started tutorial, we'll take you through rebalancing and constructing a portfolio.

## Before you begin
{: #prereqs}
The following steps show you how to obtain access to the Portfolio Optimization service and invoke API methods. To use the Portfolio Optimization REST API, it is important that you understand the basics of RESTful web services and JSON representations.

You must install cURL before you can use the service.

For a list of the sample assets available for use with the Portfolio Optimization Service, see the [Portfolio Optimization Service Sample Data Set](http://public.dhe.ibm.com/software/analytics/solutions/en/fintech/portfolio_optimization_service_sample_dataset.xlsx) spreadsheet.

## Step 1: Getting your credentials

1. Create an instance of the service.
2. Review the service instance access credentials, similar to the following example:
```
{
  "accessToken": "<api-key>",
  "uri": "https://fss-analytics.mybluemix.net/"
}
```

## Step 2: Rebalancing a portfolio

This operation optimizes an existing portfolio with the objective of minimizing tracking error squared (the variance of the difference between the InitialPortfolio portfolio and Benchmark_Blended portfolio returns) at a 30 day time horizon.

The constraints on the rebalancing are as follows:
* The expected return of the InitialPortfolio portfolio should be greater than or equal to 0.5% in 30 days
* The weight of FixedIncome_Port in the InitialPortfolio portfolio should be greater than or equal to 50%.
* There are no short-sales for assets in InitialPortfolio portfolio.
* The optimized portfolio should contain no more than 3 assets.

Before using the example in this step, save the following JSON code in a file named test1.json:
```
{
    "portfolios":[
        {
            "name":"InitialPortfolio",
            "type": "root",
            "description":"In this example, the root portfolio consists of assets in equities, fixed income, and ETFs.",
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
        {
            "name":"Equities_Port",
            "type": "subportfolio",
            "ParentPortfolio": "InitialPortfolio",
            "description":"Grouping of the Equities assets in the InitialPortfolio",
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
            "description":"Grouping of the fixed income assets in the InitialPortfolio ",
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
        {
            "name":"Benchmark_Blended",
            "type": "benchmark",
            "description":"A benchmark of 2 ETFs 50% equity and 50% in fixed income",
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
    ],
    "objectives":[
        {
            "sense": "minimize",
            "measure": "variance",
            "attribute": "return",
            "portfolio": "InitialPortfolio",
            "TargetPortfolio": "Benchmark_Blended",
            "timestep": 30,
            "description": "Minimize tracking error squared (variance of the difference between InitialPortfolio portfolio and Benchmark_Blended portfolio returns) at time 30 days"
        }
    ],
    "constraints":[  
        {
            "measure": "expectation",
            "attribute": "return",
            "portfolio": "InitialPortfolio",
            "timestep": 30,
            "relation": "greater-or-equal",
            "constant": 0.005,
            "description": "The expected return of the portfolio should be greater-than-or-equal to 0.5% in 30 days"
        },
        {
            "attribute": "weight",
            "portfolio": "FixedIncome_Port",
            "InPortfolio": "InitialPortfolio",
            "relation": "greater-or-equal",
            "constant": 0.5,
            "description": "The weight allocation to Fixed Income greater than or equal to 50%"
        },    
        {
            "attribute": "weight",
            "members": "InitialPortfolio",
            "relation": "greater-or-equal",
            "constant": 0,
            "description": "No short-selling (Lower bound)"
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
            "constant": 3,
            "description":"No more than 3 assets have non-zero quantities"
        }
    ]
}
```
{:codeblock}

To use the following example, replace api-key and service-url with your service credentials from step 1.

```
curl -X POST -H "X-IBM-Access-Token: <api-key>" -H "Content-Type: application/json" -d @test1.json {service-url}/api/v1/optimization/portfolio/rebalance
```
{:codeblock}

The service returns a response that contains the status of the portfolio, the information about all trades that were performed, and the performance statistics of the process.

The following is the response to a successful request:
```
{
 "Holdings": [
  {
   "Asset": "CX_US031162BK53_USD",
   "Quantity": 1231,
   "OptimizedTrade": 1258.88113490802,
   "OptimizedQuantity": 2489.8811349080197
  },
  {
   "Asset": "CX_US03232PAD06_USD",
   "Quantity": 1374,
   "OptimizedTrade": -1374,
   "OptimizedQuantity": 0
  },
  {
   "Asset": "CX_US912810EJ35_USD",
   "Quantity": 1154,
   "OptimizedTrade": -1154,
   "OptimizedQuantity": 0
  },
  {
   "Asset": "CX_US1912161007_NYQ",
   "Quantity": 3198,
   "OptimizedTrade": -3198,
   "OptimizedQuantity": 0
  },
  {
   "Asset": "CX_US4878361082_NYQ",
   "Quantity": 2156,
   "OptimizedTrade": -2156,
   "OptimizedQuantity": 0
  },
  {
   "Asset": "CX_US4642872422_NYQ",
   "Quantity": 0,
   "OptimizedTrade": 563.014515626434,
   "OptimizedQuantity": 563.014515626434
  },
  {
   "Asset": "CX_US4642875987_NYQ",
   "Quantity": 0,
   "OptimizedTrade": 3072.58550537634,
   "OptimizedQuantity": 3072.58550537634
  }
 ],
 "Metadata": {
  "Status": "Optimal",
  "Message": "Optimal",
  "StatusCode": 101,
  "ObjectiveValue": 0.000060479806881079,
  "TransactionID": "98d7e23e-d603-4534-9d83-9cb45a086c7c",
  "RequestStartTimestamp": "2017-08-11T20-20-01.786983655-UTC",
  "RequestEndTimestamp": "2017-08-11T20-20-01.940746666-UTC"
 },
 "Performance": {
  "Algorithm": "MIP",
  "Threads": 2,
  "Time": 0
 }
}
```
{:codeblock}

## Step 3: Constructing a portfolio

This operation creates a new portfolio named Universe. The objective when creating the portfolio is to minimize the variance on the return from the Universe portfolio in 30 days. 

The constraints on the creation of the new portfolio are as follows:
* The weight of the Government_Port assets in the Universe portfolio is equal to 10%
* The weight of the Corporate_Port assets in the Universe portfolio is equal to 20%
* The weight of the Equities_Port assets in the Universe portfolio is equal to 70%
* There are no short-sales for assets in Universe portfolio.
* There must be a cash inflow of 100,000 monetary units to the Universe portfolio.

Before using the example in this step, save the following JSON code in a file named test2.json:
```
{
"portfolios":[
        {
            "name":"Universe",
            "type": "root",
            "description":"In this example, the root portfolio consists of equity, corporate, and government ETFs.",
            "holdings":[
                {
                    "asset":"CX_US4642874576_NYQ",
                    "quantity":0,
                    "description":"iShares 1-3 Year Treasury Bond"
                },
                {
                    "asset":"CX_US4642874329_NYQ",
                    "quantity":0,
                    "description":"iShares 20+ Year Treasury Bond"
                },
                {
                    "asset":"CX_US4642882819_NYQ",
                    "quantity":0,
                    "description":"iShares J.P. Morgan USD Emerging Markets Bond"
                },
                {
                    "asset":"CX_US4642872422_NYQ",
                    "quantity":0,
                    "description":"iShares iBoxx $ Investment Grade Corporate Bond"
                },
                {
                    "asset":"CX_US4642874659_NYQ",
                    "quantity":0,
                    "description":"iShares MSCI EAFE"
                },    
                {
                    "asset":"CX_US4642875987_NYQ",
                    "quantity":0,
                    "description":"iShares Russell 1000 Value ETF"
                }       
            ]
        },  
        {
            "name":"Corporate_Port",
            "type": "subportfolio",
            "ParentPortfolio": "Universe",
            "description":"All ETF's that are corporate",
            "holdings":[
                {
                    "asset":"CX_US4642882819_NYQ",
                    "quantity":0,
                    "description":"iShares J.P. Morgan USD Emerging Markets Bond"
                },
                {
                    "asset":"CX_US4642872422_NYQ",
                    "quantity":0,
                    "description":"iShares iBoxx $ Investment Grade Corporate Bond"
                }
            ]
        },
        {
            "name":"Equities_Port",
            "type": "subportfolio",
            "ParentPortfolio": "Universe",
            "description":"All ETF's which are Equities",
            "holdings":[
                {
                    "asset":"CX_US4642874659_NYQ",
                    "quantity":0,
                    "description":"iShares MSCI EAFE"
                },    
                {
                    "asset":"CX_US4642875987_NYQ",
                    "quantity":0,
                    "description":"iShares Russell 1000 Value ETF"
                }    
            ]
        },
        {
            "name":"Government_Port",
            "type": "subportfolio",
            "ParentPortfolio": "Universe",
            "description":"All ETF's which are government",
            "holdings":[
                {
                    "asset":"CX_US4642874576_NYQ",
                    "quantity":0,
                    "description":"iShares 1-3 Year Treasury Bond"
                },
                {
                    "asset":"CX_US4642874329_NYQ",
                    "quantity":0,
                    "description":"iShares 20+ Year Treasury Bond"
                }
            ]
        },
        {
            "name":"Benchmark1",
            "type": "benchmark",
            "description":"Benchmark1 : Corporate Fixed Income Benchmark ",
            "holdings":[
                {
                    "asset":"CX_US031162BK53_USD",
                    "quantity":1000
                }
            ]
        }  
    ],  
    "objectives":[
        {
            "sense": "minimize",
            "measure": "variance",
            "attribute": "return",
            "portfolio": "Universe",
            "timestep": 30,
            "description": "Finding the minimal variance portfolio at time 30 days"
        }
    ],
    "constraints":[
        {
            "attribute": "weight",
            "portfolio": "Government_Port",
            "InPortfolio": "Universe",
            "relation": "equal",
            "constant": 0.1,
            "description": "The weight allocation to Government equal to 10%"
        },
        {
            "attribute": "weight",
            "portfolio": "Corporate_Port",
            "in-portfolio": "Universe",
            "relation": "equal",
            "constant": 0.2,
            "description": "The weight allocation Corporate equal to 20%"
        },
        {
            "attribute": "weight",
            "portfolio": "Equities_Port",
            "in-portfolio": "Universe",
            "relation": "equal",
            "constant": 0.7,
            "description": "The weight allocation to Equities equal to 70%"
        },
        {
            "attribute": "weight",
            "members": "Universe",
            "relation": "greater-or-equal",
            "constant": 0,
            "description": "No Short-Selling"
        },
        {
            "attribute": "value",
            "portfolio": "Universe",
            "CashAdjust": 100000.00
        }
    ]
}
```
{:codeblock}

To use the following example, replace api-key and service-url with your service credentials from step 1.

```
curl -X POST -H "X-IBM-Access-Token: <api-key>" -H "Content-Type: application/json" -d @test2.json {service-url}/api/v1/optimization/portfolio/construct
```
{:codeblock}

The service returns a response that contains the status of the portfolio, the information about all trades that were performed, and the performance statistics of the process.

The following is the response to a successful request:
```
{
 "Holdings": [
  {
   "Asset": "CX_US4642874576_NYQ",
   "Quantity": 0,
   "OptimizedTrade": 76.1273141516458,
   "OptimizedQuantity": 76.1273141516458
  },
  {
   "Asset": "CX_US4642874329_NYQ",
   "Quantity": 0,
   "OptimizedTrade": 28.5707163256101,
   "OptimizedQuantity": 28.5707163256101
  },
  {
   "Asset": "CX_US4642882819_NYQ",
   "Quantity": 0,
   "OptimizedTrade": 0,
   "OptimizedQuantity": 0
  },
  {
   "Asset": "CX_US4642872422_NYQ",
   "Quantity": 0,
   "OptimizedTrade": 165.016501650165,
   "OptimizedQuantity": 165.016501650165
  },
  {
   "Asset": "CX_US4642874659_NYQ",
   "Quantity": 0,
   "OptimizedTrade": 461.579228097328,
   "OptimizedQuantity": 461.579228097328
  },
  {
   "Asset": "CX_US4642875987_NYQ",
   "Quantity": 0,
   "OptimizedTrade": 339.100009793996,
   "OptimizedQuantity": 339.100009793996
  }
 ],
 "Metadata": {
  "Status": "Optimal",
  "Message": "Optimal",
  "StatusCode": 1,
  "ObjectiveValue": 0.000632304892398389,
  "TransactionID": "5dfb5ebc-ce61-465b-8e29-f82fb0ca910a",
  "RequestStartTimestamp": "2017-08-11T12-38-14.358287733-UTC",
  "RequestEndTimestamp": "2017-08-11T12-38-14.457395790-UTC"
 },
 "Performance": {
  "Algorithm": "Dual",
  "Threads": 2,
  "Time": 0
 }
}
```
{:codeblock}
