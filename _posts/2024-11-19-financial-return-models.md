---
title: "Financial Return Models"
date: 2024-11-19 07:48:00 -0400
---

This reference guide walks through how to setup and configure custom financial return models for each client

# Background and Purpose

The analysis for any client can be broken into 3 key questions

1. How much did an activity change consumer behavior?
2. What did it cost to run that activity?
3. How much did we make back as a result of the change in behavior?

The development of a financial return model allows us to convert consumer actions into the financial return for a business. This helps build the connections between the marketing and finance teams.

The exact calculation and data inputs for a financial return model will vary by client and depend on the KPIs that we have measured. In some cases we may be valuing an individual transaction, whereas in others we may be looking at the impact of marketing on new customer acquisition and increasing orders from existing customers. That may involve the application of an LTV model or other financial return calculation.

The platform is setup to be highly customizable using R scripts in order to adapt to each client's requirements.

# Infrastructure

The model results viewers and simulation tools all work from the same financial return model - we set the financial return model centrally and it is then available for application across the product suite for that client.

We distinguish between the _calculation_ steps of a financial return model and the _parameters_ used in the calculation of that model. We expect that the calculation methodology is relatively static over time, but the input values may change quarter to quarter or year to year.

To acheive this, we setup 3 distinct files to support the calculations

1. Worked Example Excel Spreadsheets _these are for sharing with the client team and validation_
2. Input parameters _these are the parameters used in the calculation_
3. Calculation Methodology _this is an r script that contains specific functions to return the calculated metrics_

# Worked Example Spreadsheet

The most appropriate format for sharing a prototype of the calculations with clients is an excel file. 

In each client project there should be a directory **_0_A_GLOBAL_simulators_** which contains a **_payback models_** folder. 

This folder should contain an excel workbook with the steps for calculating the financial return model, including a worked example showing the parameters and the calculation methodology.

The output of the file should look something like the below

![final financial return model]({{site.url}}{{site.baseurl}}/images/financial_return/excel_financial_return.jpg)

This file should be developed, shared with the client for review, and confirmed before proceeding

# Financial Return Model Parameters & Model Versions

In order to calculate the financial return metrics, it is likely that you will need some static data points for calculation. These are contained in a _brandname.csv_ file that is uploaded to the central data storage to be accessible across the applications. The _brandname_ should match the client name being used across the product suite.

The columns used in this file are as follows

1. **version** _this is the name of the set of financial return parameters being applied_
2. **brand** _this is the brand name and should match the settings in the mmm results viewer and simulator tools_
3. **product** _this is the product name and should match the settings in the mmm results viewer and simulator tools_
4. **market** _this is the market that the financial return model parameters apply to, and should match the options in the mmm results viewers and simulator tools_
5. **metric** _this is the name of the metric that will be used in the financial return calculation_
6. **value** _this is the value of the metric for a given mversion/brand/product/market/metric_

This file should be combination-complete: i.e. there should be an entry for all metrics for all available combinations of version/brand/product/market. If a new metric needs to be added, the historic financial return model versions should be updated to have that metric available, with a 0, or other value, applied as appropriate.

![example financial return parameters]({{site.url}}{{site.baseurl}}/images/financial_return/input_parameters.jpg)

This file should be uploaded to the central database under the /payback_models/ directory

# Financial Return Calculation script

The final step is to setup the calculation script. To do this, we need to build a _brandname.r_ script containing the functions to calculate the results as well as display the financial return model parameters. Please note that the R package `library(jrvFinance)` is available in all of the tools to support advanced financial return calculations.

The script is normally broken into 2 sections:

1. **Key Functions** _these must exist and are the functions called by the tools to complete the financial return calcualtion_
2. **Support Functions** _these are user-defined functions that support the calculations in the above_

This file should be uploaded to the central database under the /payback_models/ directory alongside the _.csv_ file containing the financial return model parameters

## Financial Return Calculation - Key Functions

The key functions section of the financial return calcualtion script may look something like the below. 

Please note that the input and output parameters of these functions are fixed and should not be changed: they are called from within the apps.

{% highlight r %}

##### Key functions -------

#' return the metrics for a particular payback model
#'
#' @param payback.db A dataframe (database) of parameters for the financial return calculation.
#' @param payback.model.version the name of the selected financial return model in the database.
#' @return A wide data table of results.
get.payback.data=function(payback.db, payback.model.version){
  payback.data=payback.db%>%
    dplyr::filter(version==payback.model.version)%>%
    tidyr::spread(metric, value, fill=0)
  return(payback.data)
}

#' return the custom financial return model
#'
#' @param summary.table A dataframe of inputs into the financial returns model which must contain `cost` and each of the 
#' @param payback.db A dataframe (database) of parameters for the financial return calculation.
#' @param payback.model.version the name of the selected financial return model in the database.
#' @return A wide data table of results.
calc.return.table=function(summary.table, payback.db, payback.model.version, market.name){
  
  payback.data=get.payback.data(payback.db, payback.model.version)
  
  full.results=summary.table
  
  full.results=full.results%>%
    dplyr::mutate(first.order.loss=0)%>%
    dplyr::mutate(cost.working.plus.fol=0)%>%
    dplyr::mutate(new.customer.value.add.1y=0)%>%
    dplyr::mutate(new.customer.value.add.5y=0)%>%
    dplyr::mutate(new.customer.cac=0)%>%
    dplyr::mutate(new.customer.ltv.div.cost.1y=0)%>%
    dplyr::mutate(new.customer.ltv.div.cost.5y=0)%>%
    dplyr::mutate(existing.order.revenue=0)%>%
    dplyr::mutate(existing.order.margin=0)%>%
    dplyr::mutate(total.margin.1y=0)%>%
    dplyr::mutate(total.roi.1y=0)%>%
    dplyr::mutate(total.margin.5y=0)%>%
    dplyr::mutate(total.roi.5y=0)
  
  for(i in 1:nrow(full.results)){
    if(!(full.results$new_orders[i]==0 && full.results$existing_orders[i]==0 && full.results$cost[i]==0)){
      full.results$first.order.loss[i]=get.first.order.loss(full.results$new_orders[i], payback.data, market.name)
      full.results$cost.working.plus.fol[i]=full.results$cost[i]+full.results$first.order.loss[i]
      full.results$new.customer.value.add.1y[i]=get.nc.value.add(full.results$new_orders[i], payback.data, market.name, 1)
      full.results$new.customer.value.add.5y[i]=get.nc.value.add(full.results$new_orders[i], payback.data, market.name, 5)
      full.results$new.customer.cac[i]=get.a.div.b(full.results$cost[i], full.results$new_orders[i])
      full.results$new.customer.ltv.div.cost.1y[i]=get.a.div.b(full.results$new.customer.value.add.1y[i], full.results$cost.working.plus.fol[i])
      full.results$new.customer.ltv.div.cost.5y[i]=get.a.div.b(full.results$new.customer.value.add.5y[i], full.results$cost.working.plus.fol[i])
      full.results$existing.order.revenue[i]=get.ec.order.revenue(full.results$existing_orders[i], payback.data, market.name)
      full.results$existing.order.margin[i]=get.ec.margin(full.results$existing.order.revenue[i], payback.data, market.name)
      full.results$total.margin.1y[i]=get.total.margin(full.results$new.customer.value.add.1y[i], full.results$existing.order.margin[i])
      full.results$total.roi.1y[i]=get.a.div.b(full.results$total.margin.1y[i], full.results$cost.working.plus.fol[i])
      full.results$total.margin.5y[i]=get.total.margin(full.results$new.customer.value.add.5y[i], full.results$existing.order.margin[i])
      full.results$total.roi.5y[i]=get.a.div.b(full.results$total.margin.5y[i], full.results$cost.working.plus.fol[i])
    }
  }
  
  return(full.results)
}

#' return a formated version of the return calculation
#'
#' @param return.table A (wide) table of financial returns metrics
#' @param currency.prefix the prefix to use for displaying currency values.
#' @return A wide data table of results, with numbers appropriately formated
format.return.table=function(return.table, currency.prefix){
  
  return.table=return.table%>%
    dplyr::mutate(cost=scales::dollar_format(accuracy=1, prefix=currency.prefix)(cost))%>%
    dplyr::mutate(new_orders=scales::comma(round(new_orders,0), big.mark = ","))%>%
    dplyr::mutate(existing_orders=scales::comma(round(existing_orders,0), big.mark=","))%>%
    dplyr::mutate(first.order.loss=scales::dollar_format(accuracy=1, prefix=currency.prefix)(first.order.loss))%>%
    dplyr::mutate(cost.working.plus.fol=scales::dollar_format(accuracy=1, prefix=currency.prefix)(cost.working.plus.fol))%>%
    dplyr::mutate(new.customer.value.add.1y=scales::dollar_format(accuracy=1, prefix=currency.prefix)(new.customer.value.add.1y))%>%
    dplyr::mutate(new.customer.value.add.5y=scales::dollar_format(accuracy=1, prefix=currency.prefix)(new.customer.value.add.5y))%>%
    dplyr::mutate(new.customer.cac=scales::dollar_format(accuracy=0.01, prefix=currency.prefix)(new.customer.cac))%>%
    dplyr::mutate(new.customer.ltv.div.cost.1y=scales::dollar_format(accuracy=0.01, prefix=currency.prefix)(new.customer.ltv.div.cost.1y))%>%
    dplyr::mutate(new.customer.ltv.div.cost.5y=scales::dollar_format(accuracy=0.01, prefix=currency.prefix)(new.customer.ltv.div.cost.5y))%>%
    dplyr::mutate(existing.order.revenue=scales::dollar_format(accuracy=1, prefix=currency.prefix)(existing.order.revenue))%>%
    dplyr::mutate(existing.order.margin=scales::dollar_format(accuracy=1, prefix=currency.prefix)(existing.order.margin))%>%
    dplyr::mutate(total.margin.1y=scales::dollar_format(accuracy=1, prefix=currency.prefix)(total.margin.1y))%>%
    dplyr::mutate(total.roi.1y=scales::dollar_format(accuracy=0.01, prefix=currency.prefix)(total.roi.1y))%>%
    dplyr::mutate(total.margin.5y=scales::dollar_format(accuracy=1, prefix=currency.prefix)(total.margin.5y))%>%
    dplyr::mutate(total.roi.5y=scales::dollar_format(accuracy=0.01, prefix=currency.prefix)(total.roi.5y))
  
  return.table=return.table%>%
    dplyr::select(everything(),
                  `Cost`=cost,
                  `Inc New Orders`=new_orders,
                  `Inc Existing Orders`=existing_orders,
                  `First Order Loss`=first.order.loss,
                  `Total Media Cost plus First Order Loss`=cost.working.plus.fol,
                  `New Customer Value Add 1 year`=new.customer.value.add.1y,
                  `New Customer Value Add 5 year`=new.customer.value.add.5y,
                  `New Customer CAC`=new.customer.cac,
                  `New Customer LTV 1 year / Cost`=new.customer.ltv.div.cost.1y,
                  `New Customer LTV 5 year / Cost`=new.customer.ltv.div.cost.5y,
                  `Existing Order Revenue`=existing.order.revenue,
                  `Existing Order Value Add`=existing.order.margin,
                  `Total Margin 1 year`=total.margin.1y,
                  `Total ROI 1 year`=total.roi.1y,
                  `Total Margin 5 year`=total.margin.5y,
                  `Total ROI 5 year`=total.roi.5y)
  
  return(return.table)
}

#' calculate resutls and return a formatted table of results
#'
#' @param return.table A (wide) table of financial returns metrics
#' @param currency.prefix the prefix to use for displaying currency values.
#' @return A wide data table of results, with numbers appropriately formated
get.return.table=function(summary.table, payback.db, payback.model.version, market.name, currency.prefix){
  return.table=calc.return.table(summary.table, payback.db, payback.model.version, market.name)
  formated.table=format.return.table(return.table, currency.prefix)
  return(formated.table)
}

#' return a table of metrics used in the financial return calculation
#'
#' @param payback.db A datafreme (database) of parameters for the financial return calculation.
#' @param payback.model.version the name of the selected financial return model in the database.
#' @param currency.prefix the prefix to use for displaying currency values.
#' @return A wide data table of results.
get.return.metrics=function(payback.db, payback.model.version, currency.prefix){
  
  payback.data=get.payback.data(payback.db, payback.model.version)
  
  payback.data=payback.data%>%
    dplyr::select(-brand, -product)%>%
    dplyr::mutate(new_customer_value_add_1_year=scales::dollar_format(accuracy=0.01, prefix=currency.prefix)(new_customer_value_add_1_year))%>%
    dplyr::mutate(new_customer_value_add_5_year=scales::dollar_format(accuracy=0.01, prefix=currency.prefix)(new_customer_value_add_5_year))%>%
    dplyr::mutate(existing_customer_avg_order_value=scales::dollar_format(accuracy=0.01, prefix=currency.prefix)(existing_customer_avg_order_value))%>%
    dplyr::mutate(existing_customer_margin=scales::percent_format(accuracy=0.01, suffix="%")(existing_customer_margin))%>%
    dplyr::mutate(first_order_loss=scales::dollar_format(accuracy=0.01, prefix=currency.prefix)(first_order_loss))%>%
    dplyr::select(version, 
                  market, 
                  new_customer_value_add_1_year,
                  new_customer_value_add_5_year,
                  existing_customer_avg_order_value,
                  existing_customer_margin,
                  first_order_loss)
  
  return(payback.data)  
  
}



{% endhighlight %}

## Financial Return Calculation - Support Functions

Support functions are user defined functions that are called from within the key functions and may vary on a client by client basis. They should trap for common errors and make any adjustments necessary.

{% highlight r %}
##### Support Functions ------

get.nc.value.add=function(new_orders, payback.data, market.name, duration){
  payback.data=payback.data%>%
    dplyr::ungroup()%>%
    dplyr::filter(market==market.name)
  
  if(duration==5){
    nc.value.add=new_orders*(payback.data$new_customer_value_add_5_year[1])  
  } else {
    nc.value.add=new_orders*(payback.data$new_customer_value_add_1_year[1])  
  }
  
  return(nc.value.add)
}

get.a.div.b=function(a, b){
  
  c=a/b
  if(is.na(c)){c=0}
  if(is.nan(c)){c=0}
  if(is.infinite(c)){c=0}
  
  return(c)
}

get.ec.order.revenue=function(existing_orders, payback.data, market.name){
  payback.data=payback.data%>%
    dplyr::ungroup()%>%
    dplyr::filter(market==market.name)
  
  ec.order.revenue=existing_orders*(payback.data$existing_customer_avg_order_value[1])
  return(ec.order.revenue)
}

get.ec.margin=function(existing_revenue, payback.data, market.name){
  
  payback.data=payback.data%>%
    dplyr::ungroup()%>%
    dplyr::filter(market==market.name)
  
  ec.order.margin=existing_revenue*(payback.data$existing_customer_margin[1])
  return(ec.order.margin)
  
}

get.total.margin=function(nc.value.add, ec.margin){
  total.margin=nc.value.add+ec.margin
  return(total.margin)
}

get.first.order.loss=function(new_orders, payback.data, market.name){
  payback.data=payback.data%>%
    dplyr::ungroup()%>%
    dplyr::filter(market==market.name)
  
  first.order.loss=new_orders*(payback.data$first_order_loss[1])  
  
  return(first.order.loss)
}
{% endhighlight %}

# Financial Return Model Application

Once the above settings are configured, the financial return model options and output calculations should be available within all of the tools

![final financial return model]({{site.url}}{{site.baseurl}}/images/financial_return/financial_return_option.jpg)