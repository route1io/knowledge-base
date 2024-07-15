---
title: "Geolift market setup"
date: 2024-07-15 16:00:00 -0400
---

When designing a geographic lift test, in normal circumstances we look to select cells of test and control markets from within the list of all possible candidate markets. Running this with as wide a selection of markets as possible should yield the best (minimum variance) possible test design.

# Pre-Specificying Markets

There are some circumstances where we might want to pre-specify some markets as being to either one of the test or control cells. This can be accomplised by setting up the **Region** sheet in the template workbook in a pre-specified manner.

To force a region into a test cell, the _TestStatus_ column on the *Region* sheet should be set to **_Test1_** to prespecify it into the group of test markets. If you are planning a multi-cell test and need to pre-allocate markets to additional test cells, then the format should be **_Test2_**, **_Test3_**, etc. 

To force a region into a control cell, the _TestStatus_ column on the *Region* sheet should be set to **_Control_**.

The final output should look something like the below (cell highlighting is not required)

![Region TestStatus sheet]({{site.url}}{{site.baseurl}}/images/lift_planning/lift_planning_market_preassign.jpg)

The market assignment routine will then force all of the markets that have been prespecified into a test or control cell into their designated cells.

![Region TestStatus sheet]({{site.url}}{{site.baseurl}}/images/lift_planning/lift_planning_market_preassign_results.jpg)

# Changes to market allocation as a result of pre-specification

In implementing this logic, there are some considerations and watch-outs that we have to take into account with the market allocation algorithm

## Cell Sizes

If you are planning a test that is attempting to put 20% of a KPI into each test cell, and your pre-specified test-group has much more than 20%, the pre-specified market allocation will persist and override the 20% value. In this case you could end up with unequal sized test cells.

This may or may not be prolematic, and it may or may not leave you with enough other markets to fully design the test

## Running Multi-Cell Test processes

If you pre-specify markets into multiple test cells (for example _Test2_ groups) and then run a market allocation for a 1-cell test, the system will overwrite your pre-specification of markets into _Test2_ grouping and may allocate them to either the single-test or control cells. The system will then forget the pre-specification going forward. In this case, to restore the pre-specification, please re-upload your test design file.

## Market Exclusions

If you pre-specify a market into a test or control cell, and then exclude it from consideration in the observations included tab within the geo-lift planning app, the market will not be considered for inclusion in either the test or control cells